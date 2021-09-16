.. _managing_key_findings:

Managing Key Findings
-------------------------------------

Key Findings is an easy-to-use, secure online platform that allows you to share important research results with
colleagues and clients. Any MarketSight item can be added to Key Findings, and Key Findings users do not need to learn
MarketSight in order to understand your insights.

for demonstration purposes, let's try to come up with some artificial and useless
example, that covering the every aspects of working with the Key Findings in MarketSight
through the api.


Assume that we have some data that can be presented in the following table form:

     .. image:: _static/working_with_key_finding_00.png
       :alt: Some initial Data
       :align: center

Suppose we have a list of people:

     .. image:: _static/working_with_key_finding_01.png
       :alt: Some initial Data
       :align: center


For a better understanding of how to work with permissions,
let’s assume that each of the people on the list above should only see their
own data and no one else’s, so:

**Mariatta Mudle** can see (ID, Variable_1, Variable_3):

     .. image:: _static/working_with_key_finding_001.png
       :alt: Mariatta Mudle's Data
       :align: center

**Dudley Tuckett** can see (ID, Variable_2, Variable_3):

     .. image:: _static/working_with_key_finding_001.png
       :alt: Dudley Tuckett's Data
       :align: center

**Alexandrina Tune** can see everything (ID, Variable_1, Variable_2, Variable_3):

     .. image:: _static/working_with_key_finding_00.png
       :alt: Alexandrina Tune's Data
       :align: center


Let's divide the whole task into several fairly independent steps:

#. Step: **Creating Key Findings-only user**;
#. Step: **Creating DataSet and personal DataView for each user from list**;
#. Step: **Creating Key Findings for each DataView**;
#. Step: **Adding permission for user on Key Findings folder**;


Define the structure that will be used with almost every steps:


.. code-block:: python
   :linenos:

    UserData = namedtuple(
        'UserData', ['firstName', 'lastName', 'email', 'DataViewVariables']
    )
    users_data = [
        UserData("Marietta", "Mudle", "Marietta.Mudle@example.com", ['ID', "Variable_1", "Variable_3"]),
        UserData("Dudley", "Tuckett", "Dudley.Tuckett@example.com", ['ID', "Variable_2", "Variable_3"]),
        UserData("Alexandrina", "Tune", "Alexandrina.Tune@example.com", ['ID', "Variable_1", "Variable_2", "Variable_3"]),
    ]

Now:

.. tabs::

   .. tab:: 1. Step

      .. code-block:: python
         :linenos:

         import uuid

         for u in users_data:
         # if user doesn't exist in account then create it
           if api.Users.list(email=u.email).count == 0:
                # creating new user with "keyFindingsOnly" role
                api.Users.create(
                    model={
                        "account": account_id,
                        "password": "pa$$word",
                        "userName": f"{u.firstName.lower()}_{u.lastName.lower()}",
                        "firstName": u.firstName,
                        "lastName": u.lastName,
                        "email": u.email,
                        "role": "keyFindingsOnly",
                        "status": "active",
                        "partnerUserId": str(uuid.uuid4())  # put here some your user.id if you have
                    }
                )

   .. tab:: 2. Step

      Let's upload next DataSet:

      .. image:: _static/working_with_key_finding_00.png
         :alt: Some initial Data
         :align: center

      and create DataView according to the rules described in `users_data`:

      .. code-block:: python
         :linenos:

         csv_file = open("demo.csv",  "w")
         csv_file.write(
                "\n".join([
                    "ID,Variable_1,Variable_2,Variable_3",
                    "1,a,aa,aaa",
                    "2,b,bb,bbb",
                    "3,c,cc,ccc",
                ])
            )
          csv_file.close()

         async_job = api.Datasets.create(
                account_id=account,
                model={"folderPath": ["Demo", "KeyFindings", time.time()]},
                data=open('demo.csv', 'rb')
         )

         # wait until dataset will be uploaded successfully

          dataset = api.helper.wait_until(
                fnc=api.Datasets.retrieve,
                fnc_kwargs={"dataset_id": async_job.dataset.id},
                conditions={"status": "ready"},
          )

          # Creating dataView for each user
          # we will use field `DataView.description` for user's email
          # (we will use it letter in example)
          for u in users_data:
                api.Data_Views.create(
                    dataset_id=dataset.id,
                    model={
                        "dataset": dataset.id,
                        "name": f"DataView for {u.email}",
                        "description": f"{u.email}",
                        "definition": {
                            "columns": {
                                "hideEmptyResponses": True,
                                "variables":
                                    [{"code": x} for x in u.DataViewVariables]
                            }
                        },
                    },
                )


     .. note::
        We placed email of person into `DataView.description` attribute just for tutorial purpose.
        It is some kind of "mapping" between user and its own object (DataView in our case).

   .. tab:: 3. Step

      Let's create Key Findings for each DataViews that was created it `Step 1`:

      .. code-block:: python
         :linenos:

         # fetching all DataViews available for current dataset_id
         data_views = api.Data_Views.list(dataset_id=dataset.id).items

         for u in users_data:
            user_data_view = None
            for dv in data_views:
                if dv.description == u.email:
                    user_data_view = dv
                    break

            api.Key_Findings.create(
                account_id=account_id,
                model={
                    "name": user_data_view.name,
                    "description": u.email,
                    "folderPath": [u.email],
                    "account": account_id,
                    "type": "DataView",
                    "item": user_data_view.id
                }
            )

     as a result of invoking the code above should be something like this:

      .. image:: _static/working_with_key_finding_05.png
         :alt: Some Key-Finding
         :align: center


     .. note::
        Same as for `DataView` we put `email` of the user into description.
        This trick just helps us to find correct Key Findings on next step.


   .. tab:: 4. Step

      Next code will assign correct permissions:

      .. warning::

         Permission can be assigned for top level folder in `Key-Findings`
         that is why we have recursion block in `while` construction.
         We can check that Key Finding items it top level by  attribute `parent`:
         if it `None` then we can assign permission for it.


      .. code-block:: python
         :linenos:

         # Get all keyFinding users and assign them permission
         users = api.Users.list(account_id=account_id, role="keyFindingsOnly").items

         # Get all keyFinding available in account
         key_findings = api.Key_Findings.list(account_id=account_id).items

         for user in users:
             for kf in key_findings:
             if kf.description == user.email:
                 # if user.email same as description in KeyFindigns
                 # then make such user owner of this folder
                 root = api.Key_Findings.retrieve(key_finding_id=kf.parent)
                 while root.parent is not None:
                     root = api.Key_Findings.retrieve(key_finding_id=root.parent)

                 api.Key_Findings.put_user_permission(
                     account_id=account_id,
                     key_finding_id=root.id,
                     user_id=user.id,
                     model={"user": user.id, "permission": "full", },
                 )

                 # check that user is owner
                 user_permissions = api.Key_Findings.get_user_permissions(
                     account_id=account_id,
                     key_finding_id=root.id
                 ).items

                 assert list(filter(
                     lambda x: x.user == user.id and x.permission == 'full',
                     user_permissions
                 ))

      Now we can verify that only  proper user has correct (`full`) permissions for folder.

      .. image:: _static/working_with_key_finding_04.png
         :alt: Some Key-Finding
         :align: center

   .. tab:: Together

      .. code-block:: python
         :linenos:


         account_id = <Your account ID>
         import uuid
         import time
         from collections import namedtuple

         for kf in api.Key_Findings.list(account_id=account_id).items:
             api.Key_Findings.delete(key_finding_id=kf.id)

         UserData = namedtuple(
             'UserData', ['firstName', 'lastName', 'email', 'DataViewVariables']
         )
         users_data = [
             UserData("Marietta", "Mudle", "Marietta.Mudle@example.com", ['ID', "Variable_1", "Variable_3"]),
             UserData("Dudley", "Tuckett", "Dudley.Tuckett@example.com", ['ID', "Variable_2", "Variable_3"]),
             UserData("Alexandrina", "Tune", "Alexandrina.Tune@example.com", ['ID', "Variable_1", "Variable_2", "Variable_3"]),
         ]

         for u in users_data:
             # if user doesn't exist in account then create it
             if api.Users.list(email=u.email).count == 0:
                 # creating new user with "keyFindingsOnly" role
                 api.Users.create(
                     model={
                         "account": account_id,
                         "password": "pa$$word",
                         "userName": f"{u.firstName.lower()}_{u.lastName.lower()}",
                         "firstName": u.firstName,
                         "lastName": u.lastName,
                         "email": u.email,
                         "role": "keyFindingsOnly",
                         "status": "active",
                         "partnerUserId": str(uuid.uuid4())
                     }
                 )

         csv_file = open("demo.csv",  "w")
         csv_file.write(
             "\n".join([
                 "ID,Variable_1,Variable_2,Variable_3",
                 "1,a,aa,aaa",
                 "2,b,bb,bbb",
                 "3,c,cc,ccc",
             ])
         )
         csv_file.close()

         async_job = api.Datasets.create(
             account_id=account,
             model={"folderPath": ["Demo", "KeyFindings", time.time()]},
             data=open('demo.csv', 'rb')
         )

         # wait until dataset will be uploaded successfully

         dataset = api.helper.wait_until(
             fnc=api.Datasets.retrieve,
             fnc_kwargs={"dataset_id": async_job.dataset.id},
             conditions={"status": "ready"},
         )

         # Creating dataView for each user
         # we will use field `DataView.description` for user's email
         # (we will use it letter in example)
         for u in users_data:
             api.Data_Views.create(
                 dataset_id=dataset.id,
                 model={
                     "dataset": dataset.id,
                     "name": f"DataView for {u.email}",
                     "description": f"{u.email}",
                     "definition": {
                         "columns": {
                             "hideEmptyResponses": True,
                             "variables":
                                 [{"code": x} for x in u.DataViewVariables]
                         }
                     },
                 },
             )

         # Creating KeyFindings
         # Fetching all DataViews available for current dataset_id
         data_views = api.Data_Views.list(dataset_id=dataset.id).items

         for u in users_data:
             for dv in data_views:
                 if dv.description != u.email:
                     continue

                 api.Key_Findings.create(
                     account_id=account_id,
                     model={
                         "name": dv.name,
                         "description": u.email,
                         "folderPath": [u.email],
                         "account": account_id,
                         "type": "DataView",
                         "item": dv.id
                     }
                 )

         # Get all keyFinding users and assign them permission
         users = api.Users.list(account_id=account_id, role="keyFindingsOnly").items

         # Get all keyFinding available in account
         key_findings = api.Key_Findings.list(account_id=account_id).items

         for user in users:
             for kf in key_findings:
                 if kf.description == user.email:
                     # if user.email same as description in KeyFindigns
                     # then make such user owner of this folder
                     root = api.Key_Findings.retrieve(key_finding_id=kf.parent)
                     while root.parent is not None:
                         root = api.Key_Findings.retrieve(key_finding_id=root.parent)

                     api.Key_Findings.put_user_permission(
                         account_id=account_id,
                         key_finding_id=root.id,
                         user_id=user.id,
                         model={"user": user.id, "permission": "full", },
                     )

                     # check that user is owner
                     user_permissions = api.Key_Findings.get_user_permissions(
                         account_id=account_id,
                         key_finding_id=root.id
                     ).items

                     assert list(filter(
                         lambda x: x.user == user.id and x.permission == 'full',
                         user_permissions
                     ))



Deleting Key Findings
-----------------------------------------

.. warning::

    Next code will remove every Key Findings item in you account without possibility to revert it back!

      .. code-block:: python
         :linenos:

          try:
             for kf in api.Key_Findings.list(account_id=account_id).items:
                 api.Key_Findings.delete(key_finding_id=kf.id)
          except:
              pass