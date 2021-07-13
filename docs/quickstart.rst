*********************************************
Quickstart: Patterns and Best Practices
*********************************************

.. contents::
  :depth: 3
  :backlinks: entry

------------------------

Installation
===================

.. include:: _installation.rst

---------------------------

Initialization
=====================

.. include:: _initialization.rst

------------------------------------

Authorization
====================

Basic Authorization
--------------------------

.. include:: _authorization.rst

---------------------------

Cached Access Tokens
---------------------------------

Each time you invoke :meth:`api.connect <marketsight.application_api.ApplicationAPI.connect>`
to retrieve a new authorization, the **MarketSight API Client** makes a request
to the API to retrieve a fresh token.

This token will expire in two weeks, so it may be wise to cache it within your
environment and use the cached value for your next requests against the API.

You can use the
:attr:`api.access_token <marketsight.application_api.ApplicationAPI.access_token>`
property to help you read/write the token value. For example:

.. code-block:: python
   :linenos:

    import time

    # Import the MarketSight API Client
    import marketsight

    # From YOUR project, import whatever cache class you use to facilitate your
    # caching logic. For example, this might be a wrapper around a Redis or
    # Memcache environment.
    from your_project import CacheClass

    # Define your partner credentials
    client_id = <YOUR client_id>
    client_secret = <YOUR client_secret>

    # Initialize your MarketSight API Client
    api = marketsight.client()

    # Instantiate your cache.
    cache = CacheClass()

    # Retrieve a cached token from your cache
    api.access_token = cache.cache.get('access_token')

    # If there's no available access token, then authorize against the API.
    if not api.access_token:
        api.connect(client_id = client_id,
                    client_secret = client_secret)

        # Save the fresh token to your cache
        cache.set("access_token",
                  api.access_token,
                  exp = time.time() + 14 * 24 * 3600)

    # (optional) Check that the API instance is properly authorized.
    print(api.Membership.get_me())

------------------------

Upload a Dataset
======================

.. tabs::

   .. tab:: SPSS File

      .. code-block:: python
         :linenos:

         # Create the dataset model.
         dataset_model = dict(
           folderPath = ["test_folder", "sub-folder"],
           account = "f1f13fa566144e7ba6610794c03c98cf"
         )

         # Create an asynchronous job.
         async_job = api.Datasets.create(
             model = dataset_metadata_model,
             data = open("data_file.sav", "rb"),
         )

         # (optional) Check that the asynchronous job has a given ID.
         print(_asyncJob.job.id)

          # Use a Helper `wait_until` to wait until the dataset has been fully
         # processed and is ready to use.
         job = api.helper.wait_until(
             fnc = api.Jobs.retrieve,
             fnc_kwargs = {"job_id": async_job.job.id},
             conditions = {"result": "succeeded"},
         )

   .. tab:: Excel File with Dataset Labeling Workbook

      .. code-block:: python
         :linenos:

         # Create the dataset model.
         dataset_model = dict(
           folderPath = ["test_folder", "sub-folder"],
           account = "f1f13fa566144e7ba6610794c03c98cf"
         )

         # Create an asynchronous job.
         async_job = api.Datasets.create(
             account_id = "f1f13fa566144e7ba6610794c03c98cf",
             model = dataset_metadata_model,
             data = open("data_file.xlsx", "rb"),
             metadata = open("labeling_workbook.xlsx", "rb")
         )

         # (optional) Check that the asynchronous job has a given ID.
         print(_asyncJob.job.id)

         # Use a Helper `wait_until` to wait until the dataset has been fully
         # processed and is ready to use.
         job = api.helper.wait_until(fnc = api.Jobs.retrieve,
                                     fnc_kwargs = {"job_id": async_job.job.id},
                                     conditions = {"result": "succeeded"}
                                     )

Upload a Collection of SPSS Files
--------------------------------------

Let's imagine that on your local machine you have a set of SPSS files
in a directory `test` with a directory structure as follows:

.. code-block:: bash
   :linenos:

      ── test
          ├── 1
          │   ├── 1.1
          │   │   ├── 1.1.1
          │   │   │   └── business-operations-survey-1.1.1.sav
          │   │   └── business-operations-survey-1.1.sav
          │   └── business-operations-survey-1.sav
          └── business-operations-survey.sav

If we want to upload all of these SPSS files into Dynata's Reporting & Analytics
platform, we can easily do so using a function like what is shown below:

.. code-block:: python
   :linenos:

    import pathlib
    import marketsight

    # Set your CLIENT ID and CLIENT SECRET
    client_id = os.getenv('MARKETSIGHT_CLIENT_ID')
    client_secret = os.getenv('MARKETSIGHT_CLIENT_SECRET')

    # Initialize the MarketSight API Client.
    api = marketsight.client()

    # Authorize against the API
    api.connect(client_id = client_id,
                client_secret = client_secret)

    # Create a function to navigate a directory structure and upload your SPSS
    # files.
    def mass_upload(source, account_id):
        for file_ in pathlib.Path(source).glob("**/*.sav"):
            _async_job = api.Datasets.create(
                model = dict(
                    folderPath = file.parts[:-1],
                    account = account_id,
                ),
                data = file_.open("rb")
            )

    # Call the function to upload the SPSS files.
    mass_upload("test",
                account_id = "SOME-ACCOUNT-ID-GOES-HERE")



Upload a Collection of SPSS Files in parallel (simultaneously)
----------------------------------------------------------------------------

Sometimes it makes sense to do some tasks in parallel.
For example, when you create a new Dataset with sav-file,
you actually create a job on MarketSight server. The process of creating
a Dataset on the server is delayed, and its execution is asynchronous.
Sometimes you have to wait for quite a long time to complete the creation of DataSet.
If you need to uploading a relatively large number of small sav-files,
it makes sense to load them in parallel and all at once.
Let's try to perform a task similar to the one above,
but to do the same with parallel loading of sav-files:


.. code-block:: bash
   :linenos:

     import pathlib
     import threading
     from queue import Queue

     ACCOUNT_ID = "<YOUR ACCOUNT ID>"

     MAX_THREADS = 20  # max treads that can be run simultaneously

     source = pathlib.Path() / "tests" / "data"

     _queue = Queue()

     def executor(api, q: Queue):
        while not q.empty():
            params = q.get()
            api.Datasets.create(**params)
        q.task_done()

     # put all the params for api.Datasets.create method into `Queue`
     for file in source.glob("**/*.sav"):

        model = {
            "account_id": ACCOUNT_ID,
            "model": {
                    "folderPath": list(file.relative_to(source.parent).parts[:-1]),
                    "account": ACCOUNT_ID,
                    "name": file.name,
            },
            "data": file.open("rb")
        }
        _queue.put(dict(model))

     # Creating and running threads for parallel execution
     for i in range( min(_queue.qsize(), MAX_THREADS)):
        worker = threading.Thread(target=executor, args=(api, _queue,))
        worker.start()

     # Waiting for all jobs to be finished
     _queue.join()



Append Data to an Existing Dataset
-----------------------------------------

.. tabs::

  .. tab:: Respondent Records

    .. todo::

      Add an example of how to append data to an existing data.

  .. tab:: New Waves

    .. todo::

      Add an example of appending a whole new wave of data.

  .. tab:: New Variables

    .. todo::

      Add an example of appending new variables within a given wave.

---------------------

Export a Dataset to SPSS
==================================

.. code-block:: python
   :linenos:

   dataset_id = "1bbe88ceb6214ab2a0dd4cb9c1cb788a"

   # Verify that the dataset has a "ready" status
   if api.Datasets.retrieve(dataset_id=dataset_id).status == 'ready':

       # Create an Asynchronous Job for the export.
       async_job = api.Datasets.export(dataset_id = dataset_id)

   # Use a Helper `wait_until` to wait until the exported SPSS file is ready
   # to download.
   job = api.helper.wait_until(
       fnc = api.Jobs.retrieve,
       fnc_kwargs = {"job_id": async_job.job.id},
       conditions = {"result": "succeeded"},
   )

   # Download the SPSS file
   with open('exported-dataset.sav', 'wb') as fp:
       for blob in api.Jobs.retrieve(job_id = job.id).blobs:
           blob_data = api.Jobs.get_blob_data(job_id = job.id,
                                              blob_id = blob.id)

           # Write the data to disk
           fp.write(blob_data)

-------------------------------

Retrieve a Crosstab
--------------------------------

.. tabs::

  .. tab:: JSON Format

    .. code-block:: python
       :linenos:

       # Set the Crosstab ID
       crosstab_id = "SOME-CROSSTAB-ID-GOES-HERE"

       # Create an asynchronous job that will calculate the Crosstab result.
       async_job = api.Crosstabs.calculate(crosstab_id = crosstab_id,
                                           model = {
                                               "format": "xlsx",
                                               "preferSync": False
                                           })

       # Use a Helper `wait_until` to wait until the crosstab has been
       # calculated and is ready to be retrieved.
       job = api.helper.wait_until(
           fnc = api.Jobs.retrieve,
           fnc_kwargs = {"job_id": async_job.job.id},
           conditions = {"result": "succeeded"},
           sleep_time = 5,
           max_attempts = 5
       )

       # Retrieve the results of the crosstab in JSON format.
       result = None
       for blob in job.blobs:
           result = api.Jobs.get_blob_data(job_id = job.id,
                                           blob_id = blob.id)

  .. tab:: Excel Format

    .. code-block:: python
       :linenos:

       # Set the Crosstab ID
       crosstab_id = "SOME-CROSSTAB-ID-GOES-HERE"

       # Create an asynchronous job that will calculate the Crosstab result.
       async_job = api.Crosstabs.calculate(crosstab_id = crosstab_id,
                                           model = {
                                               "format": "xlsx",
                                               "preferSync": False
                                           })

       # Use a Helper `wait_and_download_blob` to wait until the crosstab has been
       # calculated and then download its data.
       data = api.helper.wait_and_download_blob(job_id = async_job.job.id,
                                                sleep_time = 5,
                                                max_attempts = 5
                                                )

       # Write the Excel file to disk.
       with ('my-downloaded-crosstab.xlsx', 'wb') as downloaded_file:
           downloaded_file.write(data)