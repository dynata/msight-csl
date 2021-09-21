:orphan:

**********************************
API Reference
**********************************

.. _api_reference:

.. contents::
  :local:
  :depth: 4
  :backlinks: entry

---------------------------------------------

.. note::

  While the **MarketSight API Client** provides a Pythonic way of interacting
  with the :term:`Dynata Reporting & Analytics API`, you may be interested in
  either calling the RESTful API directly or using a different programming
  language.

  In that case, we recommend you review the
  `MarketSight API Reference Documentation <https://api-documentation.marketsight.com>`_

.. module:: marketsight

``client`` factory function
----------------------------------

.. autofunction:: client

---------------------------------------------

ApplicationAPI
---------------------

.. module:: marketsight.application_api

.. autoclass:: ApplicationAPI
  :members:
  :inherited-members:

------------------------------------------------

Helper
---------------

.. autoclass:: Helper
  :members:
  :inherited-members:

------------------------------------------------

.. _resources:

Resources
-----------------

As described in :doc:`Using the MarketSight API Client <using>`, the
:class:`ApplicationAPI` instance is the primary entry-point to interacting with
Dynata's Reporting & Analytics API. Resources that you can interact with that
are exposed by the API are available as members within the
:class:`ApplicationAPI` instance, and are documented below.

So for example, you would access the :class:`Accounts` resource by calling
``api.Accounts``, where ``api`` is your :class:`ApplicationAPI` instance.



Accounts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Accounts

    

    Accounts correspond to business entities that have some sort of relationship,
    either directly or indirectly, with the MarketSight platform.
    Think of Accounts as "companies".

    Accounts may contain :class:`Users`, :class:`Datasets`, :class:`Crosstabs`,
    :class:`Charts`, etc.

    Each Account has a primary contact. This is the main point of contact for the
    Account, and the individual who is directly responsible for everything related
    to that Account.

    Accounts may also have multiple administrators, who are allowed to perform more
    actions than a typical :class:`User <Users>`, but not as many actions as the
    primary contact.

    Accounts may be registered as a fully-paid Account, or as a Trial Account. Trial
    Accounts will expire after a certain amount of time, will have a different
    experience within the MarketSight platform, and will receive periodic
    notifications about their trial status encouraging them to sign up for a paid
    Account.


  

    .. py:method:: create(model)

      Create an Account.

      To connect the created Account to a related Partner, supply the `externalId`property. This will create an Account-Association

      
      :param model: Properties specified when creating a new account.
      :type model: 
      

      
      
      
      :rtype: `Account`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example:

          from uuid import uuid4
          from datetime import datetime, timedelta


          account = api.Accounts.create(
              model=dict(
                  externalId=f"{uuid4()}",
                  name=f"Test name of account-{uuid4()}",
                  stage="paid",
                  startDate=datetime.now() - timedelta(days=3),
                  endDate=datetime.now() + timedelta(days=3653),
                  maxFullAccessUsers=10,
                  maxReadOnlyUsers=10,
                  maxKeyFindingsOnlyUsers=10,
              )
          )

          print(account.id)
          print(account.name),
          print(account.datasetLimits.variablesLimit)
          
    

  

    .. py:method:: retrieve(account_id)

      Retrieve an Account.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      

      
      
      
      :rtype: `Account`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example of usage

          account = api.Accounts.retrieve(account_id="6007bada67c84907911d0b844268ab96")

          print(account.id)
          print(account.name),
          print(account.datasetLimits.variablesLimit)

          # or just

          print(
              api.Accounts.retrieve("6007bada67c84907911d0b844268ab96").name
          )
          
    

  

    .. py:method:: modify(account_id, model)

      Update an Account.

      

      
      :param account_id: ID of the Account to update.
      :type account_id: str
      
      :param model: Properties to update for the Account.
      :type model: 
      

      
      
      
      :rtype: `Account`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example of usage
          account = api.Accounts.modify(
              account_id="f2a591dd9be9489a9f23d9800259e9c1",
              model={
                  "name": "new name of account",
              }
          )

          print(account.name)
          
    

  

    .. py:method:: get_user_tokens(account_id, user_id)

      Retrieve User's OAuth Tokens for the current Partner.

       These OAuth Tokens allow the User to execute requests against the API.  If `account_id` in the URL is a wildcard (`-`), tokens for the requester's Account are returned. 



      
      :param account_id: ID of the Account whose users/tokens should be retrieved
      :type account_id: str
      
      :param user_id: ID of the User whose OAuth tokens should be retrieved
      :type user_id: str
      

      
      
      
      :rtype: `OAuthTokens`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: Works with `cmix` type partners only
          token = api.Accounts.get_user_tokens(
              user_id="ff365b9921e94e989ed3c0bcea19eb98"
          )
          print(tokens.access_token)
          print(tokens.refresh_token)
          print(tokens.expires_in)
          
    

  

    .. py:method:: put_user_tokens(account_id, user_id, tokens)

      Set a User's OAuth Tokens for the current Partner.

      These OAuth Tokens are used to authenticate requests by the User against theAPI.            If `account_id` in the URL is a wildcard (`-`), tokens for the requester'sAccount are set.



      
      :param account_id: ID of the Account whose tokens should be set
      :type account_id: str
      
      :param user_id: ID of the User whose tokens should be set
      :type user_id: str
      
      :param tokens: New access tokens
      :type tokens: 
      

      
      
      
      :rtype: `OAuthTokens`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          import uuid

          cmix_account = "7337c2c6f4f8488fa89895bad0eb1927"
          access_token = str(uuid.uuid4())
          refresh_token = str(uuid.uuid4())

          result = api.Accounts.put_user_tokens(
              account_id=cmix_account,
              user_id=random_user.id,
              tokens={
                  "token_type": "bearer",
                  "access_token": access_token,
                  "refresh_token": refresh_token,
                  "expires_in": 14 * 24 * 3600
              }
          )
          print(result.access_token)
          
    

  

    .. py:method:: delete_user_tokens(account_id, user_id)

      Invalidates the User's OAuth Tokens for the current Partner.

      This operation will prevent API transactions by the User for the currentPartner.If `account_id` in the URL is a wildcard (`-`), tokens for the requester'sAccount are invalidated.



      
      :param account_id: ID of the Account for which tokens should be invalidated
      :type account_id: str
      
      :param user_id: ID of the User from whom tokens should be invalidated
      :type user_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          from bravado.exception import HTTPNotFound

          # Check that tokens exists
          user_id="303919aa26424b87bd94610a7fc4531a"
          tokens = api.Accounts.get_user_tokens(user_id=<user_id)
          if tokens:
              api.Accounts.delete_user_tokens(user_id=user_id)
              try:
                  # getting the tokens should raise HTTPNotFound error
                  api.Accounts.get_user_tokens(user_id=<user_id)
              except HTTPNotFound:
                  print("tokens was deleted successfully")
          
    

  

----------------------------------------------------------------------------



Advanced_Analytics
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Advanced_Analytics

    

  

    .. py:method:: get_advanced_analyzes(account_id, dataset_id)

      Retrieve List of advanced analitics.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: ID of the Dataset
      :type dataset_id: str
      

      
      
      
      :rtype: `CollectionOfAdvancedAnalysis`_
      
      

    

  

    .. py:method:: post_advanced_analysis(account_id, dataset_id, model)

      Add new Advanced Analysis.

      

      
      :param account_id: account to add
      :type account_id: str
      
      :param dataset_id: dataset to add
      :type dataset_id: str
      
      :param model: Advanced Analysis with command
      :type model: 
      

      
      
      
      :rtype: `AdvancedAnalysis`_
      
      

    

  

    .. py:method:: get_advanced_analysis(account_id, dataset_id, advanced_analysis_id)

      Retrieve the advanced analysis by id.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to retrieve
      :type advanced_analysis_id: str
      

      
      
      
      :rtype: `AdvancedAnalysis`_
      
      

    

  

    .. py:method:: put(account_id, dataset_id, advanced_analysis_id, model)

      Full replacing of the advanced analysis.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: Dataset of the advanced analysis
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to update
      :type advanced_analysis_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `AdvancedAnalysis`_
      
      

    

  

    .. py:method:: patch(account_id, dataset_id, advanced_analysis_id, model)

      Update the advanced analysis with difefrence only.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: Dataset of the advanced analysis
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to patch
      :type advanced_analysis_id: str
      
      :param model: All properties are optional
      :type model: 
      

      
      
      
      :rtype: `AdvancedAnalysis`_
      
      

    

  

    .. py:method:: delete(account_id, dataset_id, advanced_analysis_id)

      Delete the advanced analysis.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: Dataset of the advanced analysis
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to delete
      :type advanced_analysis_id: str
      

      
      
      

    

  

    .. py:method:: get_advanced_analysis_definition(account_id, dataset_id, advanced_analysis_id)

      Retrieve the advanced analysis definition by id.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to retrieve
      :type advanced_analysis_id: str
      

      
      
      
      :rtype: `AdvancedAnalysisDefinition`_
      
      

    

  

    .. py:method:: put_definition(account_id, dataset_id, advanced_analysis_id, model)

      Full replacing of the advanced analysis definition.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: Dataset of the advanced analysis
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to update
      :type advanced_analysis_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `AdvancedAnalysisDefinition`_
      
      

    

  

    .. py:method:: patch_definition(account_id, dataset_id, advanced_analysis_id, model)

      Update the advanced analysis definition with difference only.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: Dataset of the advanced analysis
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to patch
      :type advanced_analysis_id: str
      
      :param model: All properties are optional
      :type model: 
      

      
      
      
      :rtype: `AdvancedAnalysisDefinition`_
      
      

    

  

    .. py:method:: run(account_id, dataset_id, advanced_analysis_id)

      Run the advanced analysis.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to retrieve
      :type advanced_analysis_id: str
      

      
      
      

    

  

    .. py:method:: copy(account_id, dataset_id, advanced_analysis_id)

      Copy the advanced analysis.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: Dataset of the advanced analysis
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to delete
      :type advanced_analysis_id: str
      

      
      
      
      :rtype: `AdvancedAnalysis`_
      
      

    

  

    .. py:method:: export(account_id, dataset_id, advanced_analysis_id)

      Export the result of the advanced analysis.

      

      
      :param account_id: ID of the Account to retrieve
      :type account_id: str
      
      :param dataset_id: Dataset of the advanced analysis
      :type dataset_id: str
      
      :param advanced_analysis_id: Id of the analysis to delete
      :type advanced_analysis_id: str
      

      
      
      

    

  

----------------------------------------------------------------------------



Charts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Charts

    

    :class:`Charts` are data visualizations that are produced from
    :class:`Crosstabs` or :class:`Data_Views`.

    As with :class:`Crosstabs` themselves, each :class:`Chart <Charts>` is built based on a
    :ref:`ChartDefinition <ChartDefinition>` which provides the configuration
    details for how a given visualization should be rendered.

    :class:`Charts` can also be exported from the platform using the :meth:`Charts.export`
    method, which can return a chart in either an image or PowerPoint format.


  

    .. py:method:: list(account_id, dataset_id, name)

      Retrieve List of Charts.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to filter for
      :type dataset_id: str
      
      :param name: Name of the Chart to search for
      :type name: str
      

      
      
      
      :rtype: `CollectionOfChart`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example 1: getting all chart nme from the dataset

           for chart in api.Charts.list(dataset_id="9d369e7213334344a1380d9b274a5a9d").items:
              print(chart.id)
              print(chart.name)

          # Example 2: check if chart with name `some name` exists in the dataset
           if api.Charts.list(dataset_id="9d369e7213334344a1380d9b274a5a9d", name="some name").items:
              # Do something here
              pass
          
    

  

    .. py:method:: create(account_id, dataset_id, model)

      Create a new chart.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `Chart`_
      
      

    

  

    .. py:method:: modify(account_id, dataset_id, chart_id, model)

      Change an existing chart.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param chart_id: MarketSight chart ID.
      :type chart_id: str
      
      :param model: All properties are optional
      :type model: 
      

      
      
      
      :rtype: `Chart`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          updated_chart = api.Charts.modify(
              chart_id="3fe0914bb7364f058f0f99e77cfc8ac0",
              model={"description": "Some description"}
          )
          print(updated_chart.description)
          
    

  

    .. py:method:: create_or_modify(account_id, dataset_id, chart_id, model)

      Set a new or update an existing chart.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param chart_id: MarketSight chart ID.
      :type chart_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `Chart`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          chart = api.Charts.retrieve(chart_id="7978c8a4f6574267bdc60f3a93d66441")

          # do some changes with the chart
          chart.description = "NEW DESCRIPTION"

          updated_chart = api.Charts.create_or_modify(
              chart_id=chart.id,
              model=chart
          )
          print(updated_chart.description)
          
    

  

    .. py:method:: delete(account_id, dataset_id, chart_id)

      Delete a chart.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param chart_id: MarketSight chart ID.
      :type chart_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          # check that chart are existing

          api.Charts.delete(chart_id=chart.id)
          
    

  

    .. py:method:: retrieve(account_id, dataset_id, chart_id)

      Retrieve Chart Metadata.

      

      
      :param account_id: The ID of the Account whose Chart Metadata should be retrieved
      :type account_id: str
      
      :param dataset_id: The ID of the Dataset whose Chart Metadata should be retrieved
      :type dataset_id: str
      
      :param chart_id: The ID of the Chart whose Chart Metadata should be retrieved
      :type chart_id: str
      

      
      
      
      :rtype: `Chart`_
      
      

    

  

    .. py:method:: get_definition(account_id, dataset_id, chart_id)

      Retrieve Chart Definition.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param chart_id: ID of the Chart
      :type chart_id: str
      

      
      
      
      :rtype: `ChartDefinition`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          definition = api.Charts.get_definition(chart_id="c314e89cef0947939b6cb4eb22e7a060")

          
    

  

    .. py:method:: put_definition(account_id, dataset_id, chart_id, definition)

      Set Chart Definition.

      

      
      :param account_id: The ID of the Account that owns the Chart whose options will be changed
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to filter for
      :type dataset_id: str
      
      :param chart_id: ID of the Chart to update
      :type chart_id: str
      
      :param definition: Updated chart definition
      :type definition: 
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          definition = api.Charts.get_definition(chart_id="6a4f4b6fb7304f0da6253b931e39dc5c")
          
    

  

    .. py:method:: export(account_id, dataset_id, chart_id, model)

      Chart Export.

      

      
      :param account_id: The ID of the Account that owns the Chart whose options will be changed
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to filter for
      :type dataset_id: str
      
      :param chart_id: ID of the Chart to update
      :type chart_id: str
      
      :param model: Export options
      :type model: 
      

      
      
      

    

  

----------------------------------------------------------------------------



Crosstabs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Crosstabs

    

    Crosstabs are data tables produced by the MarketSight platform from your
    datasets. The data tables produced are calculated based on a robust set of
    options which you can configure within the application or using the
    MarketSight API. These options determine what data gets calculated, how the
    calculation is performed, and what data is returned.

    The data tables produced can be readily visualized using :class:`Charts` and
    :class:`Dashboards` within the MarketSight platform.


  

    .. py:method:: list(account_id, dataset_id, name)

      Retrieve List of Crosstabs.

      

      
      :param account_id: ID of the Account whose Crosstabs should be retrieved
      :type account_id: str
      
      :param dataset_id: ID of the Dataset whose Crosstabs should be retrieved
      :type dataset_id: str
      
      :param name: Name of the Crosstab to search for
      :type name: str
      

      
      
      
      :rtype: `CollectionOfCrosstab`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          Example:
          crosstabs = api.Crosstabs.list(dataset_id="4be55c689bd4464c8e2eb4361e7145ee").items
          for crostab in crosstabs:
              print(crosstab.id)
              print(crosstab.name)
          
    

  

    .. py:method:: create(account_id, dataset_id, model)

      Create a Crosstab.

      

      
      :param account_id: The ID of the Account that will own the Crosstab created
      :type account_id: str
      
      :param dataset_id: ID of the Dataset that will serve as the Crosstab's source
      :type dataset_id: str
      
      :param model: Definition of the Crosstab to create
      :type model: 
      

      
      
      
      :rtype: `Crosstab`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: copy the crosstab

          # copy definition
          definition = api.Crosstabs.get_definition(
              crosstab_id="9ea23f2a2f6449258de56f78c9b01207"
          )
          # build new model
          model =  model = {
              "definition": definition,
              "object": "Crosstab",
              "name": "test crosstab name",
              "description": "test crosstab description",
              "category": "Demographics",
          }
          crosstab = api.Crosstabs.create(
              dataset_id="1fa86130ba594bed938f5b25233d1330",
              model=model
          )

          print(crosstab.id)
          print(crosstab.description)
          
    

  

    .. py:method:: modify(account_id, dataset_id, crosstab_id, model)

      Change an existing crosstab.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param crosstab_id: MarketSight crosstab ID.
      :type crosstab_id: str
      
      :param model: All properties are optional
      :type model: 
      

      
      
      
      :rtype: `Crosstab`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: change crosstab's description
          crosstab = api.Crosstabs.modify(
              crosstab_id="1c7345f3c79a4717b78e7e4f9a0ac614",
              model={"description": "new description"}
          )

          print(crosstab.description)

          
    

  

    .. py:method:: create_or_modify(account_id, dataset_id, crosstab_id, model)

      Set a new or update an existing crosstab.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param crosstab_id: MarketSight crosstab ID.
      :type crosstab_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `Crosstab`_
      
      

    

  

    .. py:method:: delete(account_id, dataset_id, crosstab_id)

      Delete a crosstab.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param crosstab_id: MarketSight crosstab ID.
      :type crosstab_id: str
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          async_job = api.Crosstabs.delete(
              account_id="7e903dd2fea24e1ab9df47edf6366caa",
              dataset_id="bb23b6b5c25d40788bf9a16a838ab52a",
              crosstab_id="20b406c7e3db430ab7cbf82b23de734c"
          )
          # Wait until job will be done (job.result should be "succeeded")
          job = api.helper.wait_until(
              fnc=api.Jobs.retrieve,
              fnc_kwargs={"job_id": async_job.job.id},
              conditions={"result": "succeeded"},
              sleep_time=5,
              max_attempts=5
          )
          
    

  

    .. py:method:: retrieve(account_id, dataset_id, crosstab_id)

      Retrieve a Crosstab's Metadata.

      Crosstab Metadata are properties used to identify and describe the Crosstab,its Crosstab Definition, and its results.

      
      :param account_id: The ID of the Account whose Crosstab Metadata should be retrieved
      :type account_id: str
      
      :param dataset_id: The ID of the Dataset whose Crosstab Metadata should be retrieved
      :type dataset_id: str
      
      :param crosstab_id: The ID of the Crosstab whose Crosstab Metadata should be retrieved
      :type crosstab_id: str
      

      
      
      
      :rtype: `Crosstab`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
           crosstab = api.Crosstabs.retrieve(crosstab_id="2aff331c5f674c3eaa5e42b9f1445e7c")
           print(crosstab.id)
           print(crosstab.name)

          
    

  

    .. py:method:: get_definition(account_id, dataset_id, crosstab_id)

      Retrieve a Crosstab's Definition.

      The Crosstab Definition determines what is included in a given Crosstab, e.g.what calculations get performed, which variables are included, what weightingis applied, etc.            A Crosstab Definition can be copied to another Crosstab as-is, given acompatible dataset.

      
      :param account_id: The ID of the Account whose Crosstab Definition should be retrieved
      :type account_id: str
      
      :param dataset_id: The ID of the Dataset whose Crosstab Definition should be retrieved
      :type dataset_id: str
      
      :param crosstab_id: ID of the Crosstab whose definition should be retrieved
      :type crosstab_id: str
      

      
      
      
      :rtype: `CrosstabDefinition`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example:
          definition = api.Crosstabs.get_definition(
              crosstab_id="97834dbfb2004902a8c26e7432d3d24f"
          )
          print (definition.description)
          
    

  

    .. py:method:: patch_definition(account_id, dataset_id, crosstab_id, model)

      Update a Crosstab Definition.

      Updates the Crosstab Definition specified to a given Crosstab, changing            its properties based on the options provided.

      
      :param account_id: The ID of the Account that owns the Crosstab whose Definition will be changed
      :type account_id: str
      
      :param dataset_id: ID of the Dataset that will serve as the Crosstab's source
      :type dataset_id: str
      
      :param crosstab_id: ID of the Crosstab whose Crosstab Definition will be changed
      :type crosstab_id: str
      
      :param model: The Crosstab Definition to apply to the Crosstab
      :type model: 
      

      
      
      
      :rtype: `CrosstabDefinition`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: Change `includeEmpty` attribute to opposite value

          crosstab_id = "ddcc78dce00b4b62b51db176e6ae5bb2"

          definition = api.Crosstabs.get_definition(
              crosstab_id=crosstab_id
          )

          updated_definition = api.Crosstabs.patch_definition(
              crosstab_id=crosstab_id,
              model={
                  "includeEmpty": not definition.includeEmpty
              }
          )

          print(updated_definition.includeEmpty)
          
    

  

    .. py:method:: put_definition(account_id, dataset_id, crosstab_id, model)

      Apply a Crosstab Definition.

      Applies the Crosstab Definition specified to a given Crosstab, changing            how the Crosstab will be calculated.

      
      :param account_id: The ID of the Account that owns the Crosstab whose Definition will be changed
      :type account_id: str
      
      :param dataset_id: ID of the Dataset that will serve as the Crosstab's source
      :type dataset_id: str
      
      :param crosstab_id: ID of the Crosstab whose Crosstab Definition will be changed
      :type crosstab_id: str
      
      :param model: The Crosstab Definition to apply to the Crosstab
      :type model: 
      

      
      
      
      :rtype: `CrosstabDefinition`_
      
      

    

  

    .. py:method:: get_definition_rows_sections(account_id, dataset_id, crosstab_id)

      Retrieve a Crosstab's Definition rows sections..

      

      
      :param account_id: The ID of the Account whose Crosstab Definition rows sections should be retrieved
      :type account_id: str
      
      :param dataset_id: The ID of the Dataset whose Crosstab Definition rows sections should be retrieved
      :type dataset_id: str
      
      :param crosstab_id: ID of the Crosstab whose definition rows sections should be retrieved
      :type crosstab_id: str
      

      
      
      
      :rtype: `CollectionOfCrosstabDefinitionRowSection`_
      
      

    

  

    .. py:method:: calculate(account_id, dataset_id, crosstab_id, model)

      Calculate a Crosstab.

      Calculates the indicated Crosstab from the Dataset supplied and returns theresulting data table in the format requested. Supported output formats include:Xml, OnlineDisplay, Json, ETabs, Pdf, Ppt and Excel.            Crosstab calculation can either be performed synchronously (i.e. you make therequest, then the API returns the resulting data table) or asynchronously (i.e.you make the request, the API confirms that it got the request, and then sometimelater you check to see if the request has completed and then retrieve the result).            By default, Crosstabs are executed *asynchronously* but this can be overriddenby setting the `preferSync` parameter to `true`.            If you are calculating an *asynchronous* Crosstab, you will receive an HTTPstatus code `202` (Accepted) in response to your request. Then you will use the**Jobs > Retrieve a Job** to check the calculation's status and retrieve theresults when the calculation is complete.

      
      :param account_id: The ID of the Account that owns the Crosstab to calculate
      :type account_id: str
      
      :param dataset_id: The ID of the Dataset that is the source for the Crosstab to calculate
      :type dataset_id: str
      
      :param crosstab_id: The ID of the Crosstab that should be calculated
      :type crosstab_id: str
      
      :param model: Configuration of how to execute the Crosstab and the output format
      :type model: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # example 1: get result asynchronously

          async_job = api.Crosstabs.calculate(
              crosstab_id="bc49e0c2306e49b5bf85ffd470f7dcf8",
              model={
                  "format": "json",
                  "preferSync": False
              }
          )

          job = api.helper.wait_until(
              fnc=api.Jobs.retrieve,
              fnc_kwargs={"job_id": async_job.job.id},
              conditions={"result": "succeeded"},
              sleep_time=5,
              max_attempts=5
          )
          result = None
          for blob in job.blobs:
              result = api.Jobs.get_blob_data(job_id=job.id, blob_id=blob.id)

          assert result
          
    

  

----------------------------------------------------------------------------



Dashboards
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Dashboards

    

    :class:`Dashboards` are interactive digital documents that are used to communicate
    insights from your :class:`Datasets <Dataset>`. They typically contain a
    collection of :class:`Charts`, :class:`Data_Views`, etc. along with
    various other design elements like images and text.

    :class:`Dashboards` can be composed of multiple pages of content,
    and they can feature a variety of interactivity which can be applied to whatever
    elements are displayed on the page.


  

    .. py:method:: list(account_id)

      Retrieve a List of Dashboards.

      

      
      :param account_id: ID of the Account that owns the Dashboards to retrieve
      :type account_id: str
      

      
      
      
      :rtype: `CollectionOfDashboard`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          for dashboard in api.Dashboards.list(account_id="33c701b3daeb484bbde73ede24ff6938").items:
              print(dashboard.name)
          
    

  

    .. py:method:: create(account_id, model)

      Create a new Dashboard.

      

      
      :param account_id: ID of the Account that should own the dashboard
      :type account_id: str
      
      :param model: A definition of the new dashboard
      :type model: 
      

      
      
      
      :rtype: `Dashboard`_
      
      

    

  

    .. py:method:: retrieve(account_id, dashboard_id)

      Retrieve a Dashboard.

      

      
      :param account_id: ID of the Account that owns the Dashboard to retrieve
      :type account_id: str
      
      :param dashboard_id: ID of the Dashboard to retrieve
      :type dashboard_id: str
      

      
      
      
      :rtype: `Dashboard`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          dashboard = api.Dashboards.retrieve(dashboard_id="b0cba3bb241446e6a7672736103494cf")
          print(dashboard.name)
          
    

  

    .. py:method:: create_or_modify(account_id, dashboard_id, model)

      Update an existing Dashboard's metadata.

      

      
      :param account_id: ID of the Account that owns the Dashboard to update
      :type account_id: str
      
      :param dashboard_id: ID of the dashboard to update
      :type dashboard_id: str
      
      :param model: New metadata of the dashboard
      :type model: 
      

      
      
      
      :rtype: `Dashboard`_
      
      

    

  

    .. py:method:: modify(account_id, dashboard_id, model)

      Patch an existing Dashboard.

      

      
      :param account_id: ID of the account the Dashboard belongs to
      :type account_id: str
      
      :param dashboard_id: Dashboard to amend
      :type dashboard_id: str
      
      :param model: A model containing only the data to be changed
      :type model: 
      

      
      
      
      :rtype: `Dashboard`_
      
      

    

  

    .. py:method:: delete(account_id, dashboard_id)

      Delete a Dashboard.

      

      
      :param account_id: ID of the Account that owns the Dashboard to delete
      :type account_id: str
      
      :param dashboard_id: ID of the Dashboard to delete
      :type dashboard_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: delete all dashboards in account
          api.Dashboards.delete(dashboard_id="fb46c3a33fba41db8f095ec34d48fb87")
          
    

  

    .. py:method:: get_definition(account_id, dashboard_id)

      Retrieve detailed definition of a Dashboard.

      

      
      :param account_id: ID of the Account that owns the Dashboard to retrieve
      :type account_id: str
      
      :param dashboard_id: ID of the Dashboard to retrieve
      :type dashboard_id: str
      

      
      
      
      :rtype: `DashboardDefinition`_
      
      

    

  

    .. py:method:: put_definition(account_id, dashboard_id, model)

      Update an existing Dashboard.

      

      
      :param account_id: ID of the Account that owns the Dashboard to update
      :type account_id: str
      
      :param dashboard_id: ID of the dashboard to update
      :type dashboard_id: str
      
      :param model: New definition of the dashboard
      :type model: 
      

      
      
      
      :rtype: `DashboardDefinition`_
      
      

    

  

    .. py:method:: patch_definition(account_id, dashboard_id, model)

      Patch an existing Dashboard.

      

      
      :param account_id: ID of the account the Dashboard belongs to
      :type account_id: str
      
      :param dashboard_id: Dashboard to amend
      :type dashboard_id: str
      
      :param model: A model containing only the data to be changed
      :type model: 
      

      
      
      
      :rtype: `DashboardDefinition`_
      
      

    

  

    .. py:method:: copy(account_id, dashboard_id)

      Duplicate a dashboard.

      Privileged partner authentication is not supported for this method.

      
      :param account_id: ID of the Account that owns the dashboard
      :type account_id: str
      
      :param dashboard_id: ID of the dashboard to duplicate
      :type dashboard_id: str
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          async_res = api.Dashboards.copy(dashboard_id="6df0b8aef1674af1919d19294d55c33d")

          # wait until job will be completed
          job = api.helper.wait_until(
              fnc=api.Jobs.retrieve,
              fnc_kwargs={"job_id": async_res.job.id},
              conditions={"result": "succeeded"},
          )
          # retrieving the dashboard
          dashboard = api.Dashboards.retrieve(dashboard_id=job.target)

          print(dashboard.name)
          
    

  

    .. py:method:: redirect(account_id, dashboard_id, model)

      Redirect dashboard content from one dataset to another.

      Privileged partner authentication is not supported for this method.

      
      :param account_id: ID of the Account that owns the dashboard
      :type account_id: str
      
      :param dashboard_id: ID of the dashboard
      :type dashboard_id: str
      
      :param model: Model describing dashboard redirect options
      :type model: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    

  

----------------------------------------------------------------------------



Data_Sources
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Data_Sources

    

    :class:`Data_Sources` are third-party platforms that are configured as suppliers
    of data to the Dynata Reporting & Analytics platform. Typically, these are
    supported survey platforms where you have connected your MarketSight
    :class:`Account <Accounts>` with your survey platform account so that data is
    automatically synchronized between the two platforms.

    The Dynata Reporting & Analytics platform currently supports the following
    synchronizable :class:`Data_Sources`:

      * Dynata Survey Authoring (Cmix)
      * Decipher
      * Qualtrics
      * Confirmit


  

    .. py:method:: list(account_id)

      Retrieve Data Sources.

      

      
      :param account_id: ID of the Account that owns Data Sources to retrieve
      :type account_id: str
      

      
      
      
      :rtype: `CollectionOfDataSource`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # print all available `DataSource`_ for account
          for ds in api.Data_Sources.list(account_id="b2dd01edb4294985924fd7c737a9b80f").items:
              print(ds.id, ds.description)
          
    

  

    .. py:method:: create(account_id, model)

      Create a new data source.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `DataSource`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          # Note: replace os.environ["DECIPHER_URL"] value with your credential
          ds = api.Data_Sources.create(
              account_id="-",
              model= {
                  "account": "e267fde95329419596657b9318695887",
                  "name": "Example name:Data_Sources.create",
                  "description": "Example description:Data_Sources.create",
                  "enabled": True,
                  "configuration": {
                      "url": os.environ["DECIPHER_URL"],
                      "token": os.environ["DECIPHER_TOKEN"],
                      "type": "decipher",
                  },
              }
          )
          print (ds.id)
          
    

  

    .. py:method:: retrieve(account_id, data_source_id)

      Retrieve a Data Source.

      

      
      :param account_id: ID of the Account that owns the Data Source to retrieve
      :type account_id: str
      
      :param data_source_id: ID of the Data Source to retrieve
      :type data_source_id: str
      

      
      
      
      :rtype: `DataSource`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          ds = api.Data_Sources.retrieve(data_source_id="4bf4142aadcd4145bc9585a608c66e80")
          print(ds.description)
          
    

  

    .. py:method:: modify(account_id, data_source_id, model)

      Change an existing data source.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param data_source_id: MarketSight Data Source ID.
      :type data_source_id: str
      
      :param model: All properties are optional
      :type model: 
      

      
      
      
      :rtype: `DataSource`_
      
      

    

  

    .. py:method:: delete(account_id, data_source_id)

      Delete a Data Source.

      

      
      :param account_id: ID of the Account that owns the Data Source to delete
      :type account_id: str
      
      :param data_source_id: ID of the Data Source to delete
      :type data_source_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          #  Example 1: of deleting
          api.Data_Sources.delete(account_id="-", data_source_id="6278072ecfff410c8cbd58e1e39d9179")

          # Example2: Remove all DataSources from account
          for ds in api.Data_Sources.list("9773cfc24c544423ad529be69fd932f2").items:
              api.Data_Sources.delete(data_source_id=ds.id)
          
    

  

----------------------------------------------------------------------------



Data_Views
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Data_Views

    

    :class:`Data_Views` are data tables that provide respondent-level information
    from a given :class:`Dataset <Datasets>`. They can be configured to show
    particular columns of data, or to filter to particular respondent records based
    on criteria that you define.


  

    .. py:method:: list(account_id, dataset_id)

      Retrieve List of Data views.

      

      
      :param account_id: ID of the Account whose Data views should be retrieved.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset whose Data views should be retrieved.
      :type dataset_id: str
      

      
      
      
      :rtype: `CollectionOfDataView`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: get all `DataViews` available for `DataSet`
          for dv in api.Data_Views.list(dataset_id="b35ad5a376c64f61bac6af7b97e66b0c").items:
              print(dv)
          
    

  

    .. py:method:: create(account_id, dataset_id, model)

      Create a Data view.

      

      
      :param account_id: The ID of the Account that will own the Data view created
      :type account_id: str
      
      :param dataset_id: ID of the Dataset that will serve as the Data view's source
      :type dataset_id: str
      
      :param model: Definition of the Data view to create
      :type model: 
      

      
      
      
      :rtype: `DataView`_
      
      

    

  

    .. py:method:: delete(account_id, dataset_id, dataview_id)

      Delete the Data view.

      

      
      :param account_id: ID of the Account whose Data view should be deleted.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset whose Data view should be deleted.
      :type dataset_id: str
      
      :param dataview_id: Data view which will be deleted.
      :type dataview_id: str
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    

  

    .. py:method:: retrieve(account_id, dataset_id, dataview_id)

      Retrieve Data view info.

      

      
      :param account_id: ID of the Account whose Data view should be retrieved.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset whose Data view should be retrieved.
      :type dataset_id: str
      
      :param dataview_id: Data view which will be retrieved.
      :type dataview_id: str
      

      
      
      
      :rtype: `DataView`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          data_view =  api.Data_Views.retrieve(dataview_id="62c3763d99fb4f779afa605dfa7555af")
          print(data_view)
          
    

  

    .. py:method:: modify(account_id, dataset_id, dataview_id, model)

      Change an existing Data view.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param dataview_id: Data view id to change
      :type dataview_id: str
      
      :param model: Model with only data to change (patch functionality)
      :type model: 
      

      
      
      
      :rtype: `DataView`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          Example: Update description only

          updated_data_view = api.Data_Views.modify(
              dataview_id="ae23156117d74917aa7a48d8b4504b37",
              model: {"description": "TEST_DESCRIPTION"}
          )

          print(updated_data_view.description)

          
    

  

    .. py:method:: create_or_modify(account_id, dataset_id, dataview_id, model)

      Update an existing Data view.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param dataview_id: Data view id to change
      :type dataview_id: str
      
      :param model: New data view data. Full model because will replace existing
      :type model: 
      

      
      
      
      :rtype: `DataView`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: Update the description but send all information about the
          data_view_id="4a50b407f3ce4dc288b30bc6775de984"

          dv = api.Data_Views.retrieve(dataview_id=data_view_id)

          # change the description
          dv.description = "NEW DESCRIPTION"

          api.Data_Views.create_or_modify(
              dataset_id=dv.dataset,
              dataview_id=dv.id,
              model=dv
          )
          
    

  

    .. py:method:: get_definition(account_id, dataset_id, dataview_id)

      Retrieve Data view definition info.

      

      
      :param account_id: ID of the Account whose Data view should be retrieved.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset whose Data view should be retrieved.
      :type dataset_id: str
      
      :param dataview_id: Data view which will be retrieved.
      :type dataview_id: str
      

      
      
      
      :rtype: `DataViewDefinition`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: get all DataDiews definitions for particular DataSet
          for data_view in api.Data_Views.list(dataset_id="392b91da906b4423824c1b7e4d9ba7ec").items:
               definition = api.Data_Views.get_definition(
                  dataview_id=data_view.id
               )
               print(data_view.id, definition)
          
    

  

    .. py:method:: patch_definition(account_id, dataset_id, dataview_id, model)

      Uodate Data view definition info.

      

      
      :param account_id: ID of the Account whose Data view should be retrieved.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset whose Data view should be retrieved.
      :type dataset_id: str
      
      :param dataview_id: Data view which will be retrieved.
      :type dataview_id: str
      
      :param model: All properties are optional
      :type model: 
      

      
      
      
      :rtype: `DataViewDefinition`_
      
      

    

  

    .. py:method:: put_definition(account_id, dataset_id, dataview_id, model)

      Apply Data view definition info.

      

      
      :param account_id: ID of the Account whose Data view should be retrieved.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset whose Data view should be retrieved.
      :type dataset_id: str
      
      :param dataview_id: Data view which will be retrieved.
      :type dataview_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `DataViewDefinition`_
      
      

    

  

    .. py:method:: export(account_id, dataset_id, dataview_id, model)

      Calculate an Data View and return result as excel file.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param dataview_id: Data view id to calculate
      :type dataview_id: str
      
      :param model: Calculation and export options
      :type model: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      
      :rtype: `AsyncResult2`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          example:
          import pathlib

          async_job = api.Data_Views.export(
              dataview_id="30a5b96475c4487fa2a9a715f6b4d6c2",
              model={
                  "format": "excel",
                  "preferSync": False,
                  "exportCodes": False
              }
          )
          # wait until job will be finished successfully
          job = api.helper.wait_until(
              fnc=api.Jobs.retrieve,
              fnc_kwargs={"job_id": async_job.job.id},
              conditions={"result": "succeeded"},
          )

          # download the blob-content
          with open('result.file', 'wb') as fp:
              for blob in api.Jobs.retrieve(job_id=job.id).blobs:
                  fp.write(
                      api.Jobs.get_blob_data(
                          job_id=job.id,
                          blob_id=blob.id
                      )
                  )
          
    

  

----------------------------------------------------------------------------



Datasets
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Datasets

    

    :class:`Datasets` are collections of data that are subject to analysis. The
    MarketSight platform is specifically used to perform statistical analyses on
    Datasets and the information that they contain.

    Datasets can be uploaded to the MarketSight platform in a variety of formats,
    including via file upload and via API.

    Additional resources are derived from Datasets, such as :class:`Crosstabs` (data
    tables) and :class:`Charts`, and changes to the underlying Dataset's data will
    therefore flow through and affect content whose source is a given Dataset.


  

    .. py:method:: create(account_id, model, data, metadata)

      Create a new Dataset.

      This endpoint can be called in a simpler way by sending JSON that corresponds to the `model` parameter.Only external datasets can be created like this. Local datasets must be created using multipart call.            TODO: The above paragraph is not really understandable. What is the difference between an "external dataset" and a "local dataset"? This needs some more clarification so that the documentation can be copyedited.

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param model: Properties when importing a dataset.
      :type model: object
      
      :param data: Dataset data file.
      :type data: file
      
      :param metadata: Dataset metadata (labels) file.
      :type metadata: file
      

      
      
      
      :rtype: `AsyncDatasetResult`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example of usage
           _async_job = api.Datasets.create(
              model=dict(
                  folderPath=["test_folder", "sub_folder"],
                  account="4ff6dce5b93c48a88bfc436de36d37d7",
              ),
              data=open("data_file.csv", "rb"),
           )

          print(_async_job.job.id)
          
    

  

    .. py:method:: list(account_id)

      Retrieve datasets for an account.

      If `account_id` is not specified, user home account is used.

      
      :param account_id: ID of the Account that owns the Datasets to retrieve
      :type account_id: str
      

      
      
      
      :rtype: `CollectionOfDataset`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          for ds in api.Datasets.list(account_id="fcfa1e2b035d4ff596f37c28307bf5bd").items:
              print (ds.id)
              print (ds.name)
          
    

  

    .. py:method:: retrieve(account_id, dataset_id)

      Retrieve a Dataset.

      

      
      :param account_id: ID of the Account that owns the Dataset to retrieve
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to retrieve
      :type dataset_id: str
      

      
      
      
      :rtype: `Dataset`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          data_set = api.Datasets.retrieve(dataset_id="da3ef6318e454a00a6a2b2125dbb100b")
          print(data_set.id)
          print(data_set.name)
          
    

  

    .. py:method:: modify(account_id, dataset_id, model, data, metadata)

      Update a Dataset or Append Data.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to update
      :type dataset_id: str
      
      :param model: Updated Dataset Metadata and Content
      :type model: object
      
      :param data: Data file to append
      :type data: file
      
      :param metadata: Labels file to apply
      :type metadata: file
      

      
      
      
      :rtype: `Dataset`_
      
      
      
      
      :rtype: `Job`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example move `DataSet` into new folder
          dataset_id = "3343635fbd0747e69dce540ea9538677"
          data_set = api.Datasets.modify(
              dataset_id=dataset_id,
              model={"folderPath": ["new folder", ["new sub-folder"]]}
          )
          print(data_set)
          
    

  

    .. py:method:: create_or_modify(account_id, dataset_id, model, data, metadata)

      Replace a Dataset or Its Content.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to replace
      :type dataset_id: str
      
      :param model: Updated Dataset Metadata and Content
      :type model: object
      
      :param data: Data file to apply
      :type data: file
      
      :param metadata: Labels file to apply
      :type metadata: file
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Examples
          dataset_id="368ae4e498df454f9f2455c9e3882c75"

          dataset = api.Datasets.retrieve(dataset_id=dataset_id)
          dataset.description = "modified description"

          api.Datasets.create_or_modify(
              dataset_id=dataset_id,
              model=dataset
          )

          
    

  

    .. py:method:: delete(account_id, dataset_id)

      Delete a Dataset.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to delete
      :type dataset_id: str
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          async_job = api.Datasets.delete(dataset_id="047e2b4e7c604c2c8bc01bf0f7ce00c9")
          
    

  

    .. py:method:: copy(account_id, dataset_id, model)

      Duplicate a Dataset.

      

      
      :param account_id: ID of the source Account
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to duplicate
      :type dataset_id: str
      
      :param model: The destination to which the Dataset should be duplicated
      :type model: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          destination_account_id = "49a5cab2d7404216975d84c55d43891d"

          async_job = api.Datasets.copy(
              account_id="-",
              dataset_id="c0f2a94125e24cf78cda638de252b5d2",
              model={
                  "destinationAccount": destination_account_id,
                  "folderPath": ["test", "dataset_copies"],
              }
          )

          print(async_job.job.id)

          
    

  

    .. py:method:: get_external_link(account_id, dataset_id)

      Retrieve an External Dataset Link.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: MarketSight ID of the Dataset
      :type dataset_id: str
      

      
      
      
      :rtype: `ExternalDatasetLink`_
      
      

    

  

    .. py:method:: patch_external_link(account_id, dataset_id, model)

      Update an External Dataset Link.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: MarketSight ID of the Dataset
      :type dataset_id: str
      
      :param model: Metadata about the dataset on an external provider's platform
      :type model: 
      

      
      
      
      :rtype: `ExternalDatasetLink`_
      
      

    

  

    .. py:method:: get_external_link_configuration(account_id, dataset_id)

      Retrieve Vendor-specific External Dataset Metadata.

      

      
      :param account_id: MarketSight ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: MarketSight ID of the Dataset
      :type dataset_id: str
      

      
      
      
      :rtype: `CriticalMixDatasetConfig`_
      
      

    

  

    .. py:method:: patch_external_link_configuration(account_id, dataset_id, model)

      Update an External Dataset Link.

      

      
      :param account_id: MarketSight ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: MarketSight ID of the Dataset to update
      :type dataset_id: str
      
      :param model: External configuration information about the dataset
      :type model: 
      

      
      
      
      :rtype: `CriticalMixDatasetConfig`_
      
      

    

  

    .. py:method:: get_permissions(account_id, dataset_id)

      Retrieve a Dataset's Permission Configuration.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset for which to retrieve permissions
      :type dataset_id: str
      

      
      
      
      :rtype: `EntityPermissionsOfDatasetReference`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          permissions = api.Datasets.get_permissions(dataset_id="53cb1e1081cd4abb834caac87312315f")
          print(permissions)
          
    

  

    .. py:method:: patch_permissions(account_id, dataset_id, model)

      Update a Dataset's Permission Configuration.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to update
      :type dataset_id: str
      
      :param model: The Permission properties to update
      :type model: 
      

      
      
      
      :rtype: `EntityPermissionsOfDatasetReference`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
           new_owner = "a748fe9c36a94e91a77f7f2f2ddb09ee"
           dataset_id = "a24e423afbeb40da80f451279b4d8b53"

            result = api.Datasets.patch_permissions(
              dataset_id="a24e423afbeb40da80f451279b4d8b53",
              model={"owner": user.id}
          )

          
    

  

    .. py:method:: put_permissions(account_id, dataset_id, model)

      Set a Dataset's Permission Configuration.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to update
      :type dataset_id: str
      
      :param model: The Permissions to apply to the Dataset
      :type model: 
      

      
      
      
      :rtype: `EntityPermissionsOfDatasetReference`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
           new_owner = "4dd4abd30e93466b903c6932feb4a0ea"
           dataset_id = "924e8a51ed8e45f28595374844776320"

           result = api.Datasets.put_permissions(
              account_id=account,
              dataset_id=dataset,
              model={
                    "owner": new_owner,
                    "inherit": True,
                    "parent": permissions.parent,
                    "type": "Dataset",
                    "instanceType": "NavItem",
                    "object": "EntityPermissions",
              }
          )

          
    

  

    .. py:method:: get_group_permissions(account_id, dataset_id, includeInherited)

      Retrieve a Dataset's Group Permissions.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset for which permissions should be retrieved
      :type dataset_id: str
      
      :param includeInherited: If present, inherited permissions should be included in the result.
      :type includeInherited: boolean
      

      
      
      
      :rtype: `CollectionOfGroupPermission`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: print permissions for all datasets
          for dataset in api.Datasets.list(account_id=account).items:
              for group_permission in api.Datasets.get_group_permissions(
                      dataset_id=dataset.id
              ).items:
                  print(group_permission.group, group_permission.permission)
          
    

  

    .. py:method:: put_group_permission(account_id, dataset_id, group_id, model)

      Set a Group's Permission to a Dataset.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to update
      :type dataset_id: str
      
      :param group_id: ID of the Group to update
      :type group_id: str
      
      :param model: Permission Configuration to apply
      :type model: 
      

      
      
      

    

  

    .. py:method:: delete_group_permission(account_id, dataset_id, group_id)

      Revoke a Group's Permission to a Dataset.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset
      :type dataset_id: str
      
      :param group_id: ID of the Group to revoke
      :type group_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          api.Datasets.delete_group_permission(
              dataset_id="2f7115a96ac34a8ea3ffc42f379f1368",
              group_id="3692cfc4c91843299842a3375d83b0eb"
          )
          
    

  

    .. py:method:: get_user_permissions(account_id, dataset_id, includeInherited)

      Retrieve a Dataset's User Permissions.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset for which permissions should be retrieved
      :type dataset_id: str
      
      :param includeInherited: If present, inherited permissions should be included in the result.
      :type includeInherited: boolean
      

      
      
      
      :rtype: `CollectionOfUserPermission`_
      
      

    

  

    .. py:method:: put_user_permission(account_id, dataset_id, user_id, model)

      Set a User's Permission for a Dataset.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset
      :type dataset_id: str
      
      :param user_id: ID of the User
      :type user_id: str
      
      :param model: Permission Configuration to apply
      :type model: 
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          import random

          dataset_id = "0fb9558c4239478fbf9b86b1082959fb"

          # Getting random  user (associated with the current partner)
          assocs = api.Partners.get_user_associations(partner_id=client_id)

          user_id = random.choice(assocs.items).user

          model = {
               "user": user_id,
               "permission": "full",
          }

          api.Datasets.put_user_permission(
              dataset_id=dataset_id,
              user_id=user_id,
              model= {
                  "user": user_id,
                  "permission": "full",
              }
          )
          result = api.Datasets.get_user_permissions(dataset_id=dataset_id)

          print(result.items)
          
    

  

    .. py:method:: delete_user_permission(account_id, dataset_id, user_id)

      Revoke a User's Permission to a Dataset.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset
      :type dataset_id: str
      
      :param user_id: ID of the User to revoke
      :type user_id: str
      

      
      
      

    

  

    .. py:method:: put_sync(account_id, dataset_id, model)

      Sync Dataset.

      Due to a technical limitation, it is impossible to update metadata without updating respondent data.

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to sync
      :type dataset_id: str
      
      :param model: Details for this operation
      :type model: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    

  

    .. py:method:: export(account_id, dataset_id)

      Export dataset to SPSS.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to export
      :type dataset_id: str
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Check that dataset is in status `ready`
          dataset_id = "5011a2bd7e984172ad357b7939b97884"
          if api.Datasets.retrieve(dataset_id=dataset_id).status == 'ready':
              async_job = api.Datasets.export(dataset_id=dataset_id)

          # using helper `wait_until`
          job = api.helper.wait_until(
              fnc=api.Jobs.retrieve,
              fnc_kwargs={"job_id": async_job.job.id},
              conditions={"result": "succeeded"},
          )

          # download the blob-content
          with open('result.csv', 'wb') as fp:
              for blob in api.Jobs.retrieve(job_id=job.id).blobs:
                  fp.write(
                      api.Jobs.get_blob_data(
                          job_id=job.id,
                          blob_id=blob.id
                      )
                  )
          
    

  

    .. py:method:: append(account_id, dataset_id, model, data, metadata)

      Import and then append dataset to requested parent dataset..

      During the append process, a new dataset is created with combined data.Append can be done in 2 ways: Append respondents and Append variables.These approaches require different types of **model** body parameter (Example Value created for Append Variables model).            For Append variables, except of general fields, model should contains:- **CodesToJoinOn** - Codes of variables to join two datasets. Pay attention, that Codes should be the same in both datasets.If empty - records will be matched in order of appearance in the datasets.- **ExcludedVariables** - Variables from original and new dataset which will be excluded from resulted dataset.- **VariablesToRecode** - If in original and new dataset exists variables with the same code they should be recoded. For Append respondents, except of general fields, model should contains:- **MatchedVariables** - Variables to be used for matches.            `For more information about the models check file...`

      
      :param account_id: ID of the Account that owns the Dataset.
      :type account_id: str
      
      :param dataset_id: ID of the parent Dataset
      :type dataset_id: str
      
      :param model: Append dataset settings.
      :type model: object
      
      :param data: Dataset data file.
      :type data: file
      
      :param metadata: Dataset metadata (labels) file.
      :type metadata: file
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    

  

    .. py:method:: put_respondents(account_id, dataset_id, model)

      Update dataset respondents data..

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: ID of the Dataset to export
      :type dataset_id: str
      
      :param model: Dataset respondents update parameters
      :type model: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    

  

    .. py:method:: get_messages(account_id, dataset_id)

      Retrieve dataset messages.

      

      
      :param account_id: ID of the Account that owns the Dataset
      :type account_id: str
      
      :param dataset_id: MarketSight ID of the Dataset
      :type dataset_id: str
      

      
      
      
      :rtype: `CollectionOfDatasetMessage`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          result = api.Datasets.get_messages(dataset_id="27b4b56f04a347c49b90600e7bf8e2c3")
          print(result)
          
    

  

    .. py:method:: get_metadata(account_id, dataset_id)

      Download a dataset labeling workbook.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          metadata= api.Datasets.get_metadata(dataset_id="a5a50ca101ac4b7ea330cfb656733994")
          print(metadata)
          
    

  

    .. py:method:: get_dependent_dashboards(account_id, dataset_id)

      Retrieve dashboards dependent on the specified dataset.

      If `account_id` is not specified, user home account is used.

      
      :param account_id: ID of the Account that owns the Datasets to retrieve
      :type account_id: str
      
      :param dataset_id: Dataset to get dashboards dependent on
      :type dataset_id: str
      

      
      
      
      :rtype: `CollectionOfDashboard`_
      
      

    

  

----------------------------------------------------------------------------



Files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Files

    

    :class:`Files` are just that: files that can be uploaded to the Dynata
    Reporting & Analytics platform, which you can then choose to share with
    :term:`Key Findings Users <Key Findings User>` or which you can then download
    from the platform.

    Typical use cases for using :class:`Files` is to keep information related to
    your :class:`Datasets` alongside your data, such as a copy of the questionnaire
    that produced a given dataset or the final presentation that was delivered based
    on a given dataset.


  

    .. py:method:: create(account_id, model, data)

      Upload a new related file.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param model: New related file metadata
      :type model: object
      
      :param data: New related file data
      :type data: file
      

      
      
      
      :rtype: `File`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: uploading new file

          model = dict(
              folderPath=["uploaded"],
              name="Uploaded file",
              account="2a1b819f1f394cdab9473245b3a769ca",
              description="Some file description",
          )

          file = api.Files.create(
              model=model,
              data=open("~/data_file.csv", "rb")
          )

          print(file.id)
          
    

  

    .. py:method:: retrieve(account_id, file_id)

      Retrieve related file metadata.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param file_id: MarketSight related file ID.
      :type file_id: str
      

      
      
      
      :rtype: `File`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example:

          file =  api.Files.retrieve(file_id="387a2cf34850441aa2025e0c0a8c0c12")

          print(file.id)
          print(file.description)
          
    

  

    .. py:method:: delete(account_id, file_id)

      Delete a related file.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param file_id: MarketSight related file ID.
      :type file_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               

         # Example:
         # Uploading file
          file = api.Files.create(
              model=dict(
                  folderPath=["test"],
                  name="Some file name",
                  account="35b4bb4143b94901b68e2f9afe279c5f",
                  description="Some file description",
              ),
              data=open("some_file.txt", "rb"),
          )

          # delete previously uploaded file

          api.Files.delete(file_id=file.id)
          
    

  

    .. py:method:: modify(account_id, file_id, model, data)

      Change the metadata or data of a related file.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param file_id: MarketSight related file ID.
      :type file_id: str
      
      :param model: Related file metadata to change
      :type model: object
      
      :param data: Related file data to change
      :type data: file
      

      
      
      
      :rtype: `File`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
           file = api.Files.modify(
              file_id="801a3ef388e34aa79a4911768b6cb7af",
              model={"name": "new file name"}
          )

          print(file.name)
          
    

  

    .. py:method:: get_data(account_id, file_id)

      Download a related file.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param file_id: MarketSight related file ID.
      :type file_id: str
      

      
      
      

    

  

    .. py:method:: copy(account_id, file_id)

      Duplicate a File.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File to duplicate
      :type file_id: str
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example:

          async_job = api.Files.copy(file_id="ae4b5eeb388c4c2d9402e24813c693a5")

          # Wait until job.result will return "succeeded"

          job = api.helper.wait_until(
              fnc=api.Jobs.retrieve,
              fnc_kwargs={"job_id": async_job.job.id},
              conditions={"result": "succeeded"},
          )
          copied_file = api.Files.retrieve(file_id=job.target)

          print(copied_file)

          
    

  

    .. py:method:: get_permissions(account_id, file_id)

      Retrieve File's Permission Configuration.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File for which to retrieve permissions
      :type file_id: str
      

      
      
      
      :rtype: `EntityPermissionsOfFileReference`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          permissions = api.Files.get_permissions(file_id="3e923113e4fa4ca2bac8af24c0bd085e")
          print(permissions)
          
    

  

    .. py:method:: patch_permissions(account_id, file_id, model)

      Update File's Permission Configuration.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File to update
      :type file_id: str
      
      :param model: The Permission properties to update
      :type model: 
      

      
      
      
      :rtype: `EntityPermissionsOfFileReference`_
      
      

    

  

    .. py:method:: put_permissions(account_id, file_id, model)

      Set File's Permission Configuration.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File to update
      :type file_id: str
      
      :param model: The Permissions to apply to the File
      :type model: 
      

      
      
      
      :rtype: `EntityPermissionsOfFileReference`_
      
      

    

  

    .. py:method:: get_group_permissions(account_id, file_id, includeInherited)

      Retrieve File's Group Permissions.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File for which permissions should be retrieved
      :type file_id: str
      
      :param includeInherited: If present, inherited permissions should be included in the result.
      :type includeInherited: boolean
      

      
      
      
      :rtype: `CollectionOfGroupPermission`_
      
      

    

  

    .. py:method:: put_group_permission(account_id, file_id, group_id, model)

      Set a Group's Permission to the File.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File to update
      :type file_id: str
      
      :param group_id: ID of the Group to update
      :type group_id: str
      
      :param model: Permission Configuration to apply
      :type model: 
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: Will change the `full` permission to `view` for some group
          file_id = "222e9e1b7def44a2b7012e487e254081"
           group_permissions = api.Files.get_group_permissions(
              file_id=file.id
          )
          some_group_permission = group_permissions.items.pop()

          # assign 'view' permission for current group
          some_group_permission.permission = 'view'

          # apply changes
           api.Files.put_group_permission(
              file_id=file_id,
              group_id=some_group_permission.group,
              model=some_group_permission
          )

          
    

  

    .. py:method:: delete_group_permission(account_id, file_id, group_id)

      Revoke a Group's Permission to the File.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File
      :type file_id: str
      
      :param group_id: ID of the Group to revoke
      :type group_id: str
      

      
      
      

    

  

    .. py:method:: get_user_permissions(account_id, file_id, includeInherited)

      Retrieve File's User Permissions.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File for which permissions should be retrieved
      :type file_id: str
      
      :param includeInherited: If present, inherited permissions should be included in the result.
      :type includeInherited: boolean
      

      
      
      
      :rtype: `CollectionOfUserPermission`_
      
      

    

  

    .. py:method:: put_user_permission(account_id, file_id, user_id, model)

      Set a User's Permission for the File.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File
      :type file_id: str
      
      :param user_id: ID of the User
      :type user_id: str
      
      :param model: Permission Configuration to apply
      :type model: 
      

      
      
      

    

  

    .. py:method:: delete_user_permission(account_id, file_id, user_id)

      Revoke a User's Permission to the File.

      

      
      :param account_id: ID of the Account that owns the File
      :type account_id: str
      
      :param file_id: ID of the File
      :type file_id: str
      
      :param user_id: ID of the User to revoke
      :type user_id: str
      

      
      
      

    

  

----------------------------------------------------------------------------



Injected_Scripts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Injected_Scripts

    

    :class:`Injected_Scripts` are pieces of JavaScript code that can be injected
    into :class:`Dashboards`. These scripts are used to extend the interactive
    functionality of :class:`Dashboards` to provide for broader functionality than
    is natively available within the platform.


  

    .. py:method:: list(account_id)

      Retrieve Injected Scripts for an account.

      If `account_id` is '-', current user home account is used.

      
      :param account_id: ID of the Account that owns the Injected Scripts to retrieve
      :type account_id: str
      

      
      
      
      :rtype: `CollectionOfInjectedScript`_
      
      

    

  

    .. py:method:: create(account_id, model)

      Create an Injected Script.

      If privileged field is true, then script should have signature. Otherwise system will not be able to use this script in future.Private key should be used on client side to sign the script and public key should be stored on api side.Contact MarketSight administrator for more information about script signing

      
      :param account_id: ID of the Account that owns the Injected Script to create
      :type account_id: str
      
      :param model: Model of the Injected Script to create
      :type model: 
      

      
      
      
      :rtype: `InjectedScript`_
      
      

    

  

    .. py:method:: retrieve(account_id, injected_script_id, includeJavaScriptCode, includeTypeScriptCode, includeSignature)

      Retrieve the Injected Script by its Id.

      If `account_id` is '-', current user home account is used.

      
      :param account_id: ID of the Account that owns the Injected Script to retrieve
      :type account_id: str
      
      :param injected_script_id: ID of the Injected Script to retrieve
      :type injected_script_id: str
      
      :param includeJavaScriptCode: Include JS code into the result
      :type includeJavaScriptCode: boolean
      
      :param includeTypeScriptCode: Include TS code into the result
      :type includeTypeScriptCode: boolean
      
      :param includeSignature: Include signature information into the result
      :type includeSignature: boolean
      

      
      
      
      :rtype: `InjectedScript`_
      
      

    

  

    .. py:method:: modify(account_id, injected_script_id, model)

      Update an InjectedScript.

      Updates script or signature using patch rules. I.e. only need to define properties which should be updated.

      
      :param account_id: ID of the Account that owns the Injected Script
      :type account_id: str
      
      :param injected_script_id: ID of the Injected Script to update
      :type injected_script_id: str
      
      :param model: New or Changed Properties
      :type model: 
      

      
      
      
      :rtype: `InjectedScript`_
      
      

    

  

    .. py:method:: delete(account_id, injected_script_id)

      Delete an Injected Script.

      Warning: there are no checks for script dependencies and this operation cannot be reverted back.

      
      :param account_id: ID of the Account that owns the Injected Script to delete
      :type account_id: str
      
      :param injected_script_id: ID of the Injected Script to delete
      :type injected_script_id: str
      

      
      
      

    

  

    .. py:method:: get_public_keys(account_id, injected_script_id)

      Export Injected Script Public Keys in JWK Set format.

      This method is used by client UI to get list of all public keys which are valid for script signing.`account_id` and `injected_script_id` parameters should be always = '-'

      
      :param account_id: This must be wildcard (-).
      :type account_id: str
      
      :param injected_script_id: This must be wildcard (-).
      :type injected_script_id: str
      

      
      
      
      :rtype: `JwkSet`_
      
      

    

  

----------------------------------------------------------------------------



Jobs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Jobs

    

    :class:`Jobs` are tasks executed by MarketSight without blocking User or API
    access. When a :class:`Job <Jobs>` is created for a task, it can be monitored
    for completion and its results retrieved upon completion.

    .. seealso::

      * :class:`Helper`


  

    .. py:method:: list(account_id, user_id)

      Retrieve List of User's Jobs.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: Job Owner's User ID
      :type user_id: str
      

      
      
      
      :rtype: `CollectionOfJob`_
      
      

    

  

    .. py:method:: retrieve(account_id, user_id, job_id)

      Retrieve a Job.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: MarketSight user ID or email username.
      :type user_id: str
      
      :param job_id: ID of the Job to retrieve
      :type job_id: str
      

      
      
      
      :rtype: `Job`_
      
      

    

  

    .. py:method:: delete(account_id, user_id, job_id)

      Delete a Job.

      TODO: Does this cancel a Job if it is still running?

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: MarketSight user ID or email username.
      :type user_id: str
      
      :param job_id: ID of the Job to delete
      :type job_id: str
      

      
      
      

    

  

    .. py:method:: get_logs(account_id, user_id, job_id)

      Retrieve Job Logs.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: MarketSight user ID or email username.
      :type user_id: str
      
      :param job_id: ID of the Job
      :type job_id: str
      

      
      
      
      :rtype: `CollectionOfJobLog`_
      
      

    

  

    .. py:method:: get_blobs(account_id, user_id, job_id)

      List job BLOBs..

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: MarketSight user ID or email username.
      :type user_id: str
      
      :param job_id: Parent job.
      :type job_id: str
      

      
      
      
      :rtype: `CollectionOfJobBlob`_
      
      

    

  

    .. py:method:: get_blob(account_id, user_id, job_id, blob_id)

      Retrieve a blob.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: MarketSight user ID or email username.
      :type user_id: str
      
      :param job_id: MarketSight job ID.
      :type job_id: str
      
      :param blob_id: MarketSight blob ID.
      :type blob_id: str
      

      
      
      
      :rtype: `JobBlob`_
      
      

    

  

    .. py:method:: get_blob_data(account_id, user_id, job_id, blob_id)

      Retrieve a blob data.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: MarketSight user ID or email username.
      :type user_id: str
      
      :param job_id: MarketSight job ID.
      :type job_id: str
      
      :param blob_id: MarketSight blob ID.
      :type blob_id: str
      

      
      
      

    

  

----------------------------------------------------------------------------



Key_Findings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Key_Findings

    

    :class:`Key_Findings` are resources that have been shared with
    :term:`Key Findings Users <Key Findings User>` and are therefore available to be
    viewed or downloaded by those individuals who have received access.

    Typically, :class:`Key_Findings` are either :class:`Dashboards`, :class:`Charts`,
    :class:`Crosstabs`, :class:`Data_Views`, or :class:`Files` that you have chosen
    to make available. They can be organized and structured in folders, and are
    only visible to those individuals or groups that you have authorized.


  

    .. py:method:: create(account_id, model)

      Create a new Key Finding.

      The `item` property of the model must be set to a GUID or Packed GUID of the underlying item.If unspecified, a Key Findings folder is created.            The `parent` or `folderPath` properties can be used to specify the parent folder.If both are specified, the two values must resolve to the same folder.If none are specified, the new Key Finding is added to the account root.

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `KeyFindingWithItem`_
      
      

    

  

    .. py:method:: list(account_id, parent, root, entity)

      Retrieve List of Key Findings.

      If root is defined as true then parent must be null. If parent is defined it must be a folder.

      
      :param account_id: ID of the Account whose Key Findings should be retrieved
      :type account_id: str
      
      :param parent: Parent folder for the Key Findings retrieved
      :type parent: str
      
      :param root: Specifies whether Key Findings should be returned from the root folder
      :type root: boolean
      
      :param entity: An associated entity for which Key Findings should be retrieved
      :type entity: str
      

      
      
      
      :rtype: `CollectionOfKeyFinding`_
      
      

    

  

    .. py:method:: retrieve(account_id, key_finding_id)

      Retrieve a Key Finding.

      Additionally returns key finding entity definition - crosstab, chart, file etc, based on the keyfinding entity type.

      
      :param account_id: ID of the Account that owns the Key Finding to retrieve
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding to retrieve
      :type key_finding_id: str
      

      
      
      
      :rtype: `KeyFindingWithItem`_
      
      

    

  

    .. py:method:: delete(account_id, key_finding_id)

      Removes entity from the key findings list.

      The linked item will not be deleted from its origin dataset or from the Datasets list.

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding to delete
      :type key_finding_id: str
      

      
      
      

    

  

    .. py:method:: get_permissions(account_id, key_finding_id)

      Retrieve Key Finding's Permission Configuration.

      Only applicable to the folders. If link type it not a folder then error will be returned.

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding for which to retrieve permissions
      :type key_finding_id: str
      

      
      
      
      :rtype: `EntityPermissionsOfKeyFindingReference`_
      
      

    

  

    .. py:method:: patch_permissions(account_id, key_finding_id, model)

      Update Key Finding's Permission Configuration.

      Updates current permissions with patch logic i.e. should only define chnages in the model

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding to update
      :type key_finding_id: str
      
      :param model: The Permission properties to update
      :type model: 
      

      
      
      
      :rtype: `EntityPermissionsOfKeyFindingReference`_
      
      

    

  

    .. py:method:: put_permissions(account_id, key_finding_id, model)

      Set Key Finding's Permission Configuration.

      Updates current permissions or creates new set of permissions for the specified key finding

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding to update
      :type key_finding_id: str
      
      :param model: The Permissions to apply to the Key Finding
      :type model: 
      

      
      
      
      :rtype: `EntityPermissionsOfKeyFindingReference`_
      
      

    

  

    .. py:method:: get_group_permissions(account_id, key_finding_id)

      Retrieve Key Finding's Group Permissions.

      

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding for which permissions should be retrieved
      :type key_finding_id: str
      

      
      
      
      :rtype: `CollectionOfGroupPermission`_
      
      

    

  

    .. py:method:: put_group_permission(account_id, key_finding_id, group_id, model)

      Set a Group's Permission to the Key Finding.

      

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding to update
      :type key_finding_id: str
      
      :param group_id: ID of the Group to update
      :type group_id: str
      
      :param model: Permission Configuration to apply
      :type model: 
      

      
      
      

    

  

    .. py:method:: delete_group_permission(account_id, key_finding_id, group_id)

      Revoke a Group's Permission to the Key Finding.

      

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding
      :type key_finding_id: str
      
      :param group_id: ID of the Group to revoke
      :type group_id: str
      

      
      
      

    

  

    .. py:method:: get_user_permissions(account_id, key_finding_id)

      Retrieve Key Finding's User Permissions.

      

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding for which permissions should be retrieved
      :type key_finding_id: str
      

      
      
      
      :rtype: `CollectionOfUserPermission`_
      
      

    

  

    .. py:method:: put_user_permission(account_id, key_finding_id, user_id, model)

      Set a User's Permission for the Key Finding.

      

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding
      :type key_finding_id: str
      
      :param user_id: ID of the User
      :type user_id: str
      
      :param model: Permission Configuration to apply
      :type model: 
      

      
      
      

    

  

    .. py:method:: delete_user_permission(account_id, key_finding_id, user_id)

      Revoke a User's Permission to the Key Finding.

      

      
      :param account_id: ID of the Account that owns the Key Finding
      :type account_id: str
      
      :param key_finding_id: ID of the Key Finding
      :type key_finding_id: str
      
      :param user_id: ID of the User to revoke
      :type user_id: str
      

      
      
      

    

  

----------------------------------------------------------------------------



OAuth
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: OAuth

    

    OAuth (Open Authorization) operations can be used to generate access
    credentials.

    .. todo::

      Provide more in-depth documentation on the :class:`OAuth` endpoints in the
      OpenAPI documentation.


  

    .. py:method:: authorize(client_id, response_type, redirect_uri, response_mode, scope, state)

      Authorize Credentials.

      This endpoint is not versioned, and the version part of the path can be omitted.Endpoints `/oauth/authorize` and `/v1/oauth/authorize` are equivalent andwill always invoke the latest version of the OAuth Authorize endpoint

      
      :param client_id: Partner ID
      :type client_id: str
      
      :param response_type: Must be "code"
      :type response_type: str
      
      :param redirect_uri: 
      :type redirect_uri: str
      
      :param response_mode: "query" or "fragment". Defaults to ``query``. 
      :type response_mode: str
      
      :param scope: 
      :type scope: str
      
      :param state: 
      :type state: str
      

      

    

  

    .. py:method:: token(grant_type, code, client_id, client_secret, redirect_uri, refresh_token, scope)

      Retrieve an OAuth Access Token.

      This endpoint is not versioned, and the version part of the path can be omitted.Endpoints `/oauth/token` and `/v1/oauth/token` are equivalent andwill always invoke the latest version of the OAuth Token endpoint

      
      :param grant_type: "authorization_code" or "client_credentials" or "refresh_token"
      :type grant_type: str
      
      :param code: 
      :type code: str
      
      :param client_id: 
      :type client_id: str
      
      :param client_secret: 
      :type client_secret: str
      
      :param redirect_uri: 
      :type redirect_uri: str
      
      :param refresh_token: 
      :type refresh_token: str
      
      :param scope: 
      :type scope: str
      

      
      
      
      :rtype: `OAuthTokenResponse`_
      
      

    

  

----------------------------------------------------------------------------



Objects
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Objects

    

    .. todo::

      Add a description for the :class:`Objects` resource.


  

    .. py:method:: navigate(object_id, partner, code, resource, action, locale)

      Redirect to an Object.

      This operation redirects the user-agent to a generic or object-specific pagein the main MarketSight web application.            If `partner` and `code` parameters are specified, an OAuth Single Sign-Onoperation is performed.            If `object_id` is specified, the user-agent is redirected to the defaultobject-specific page for that object.            If `resource` and `action` parameters are specified, the user-agent isredirected to the corresponding object-specific or generic object-agnostic page.This only works if `object_id` is a dataset, wildcard (`-`), or omitted.            If `object_id` is a dataset, it is set to be the user's current context dataset.            

      
      :param object_id: GUID or Packed GUID of the object to navigate, or wildcard (-).
      :type object_id: str
      
      :param partner: Partner ID for Single Sign-On.
      :type partner: str
      
      :param code: One-time authorization code for Single Sign-On.
      :type code: str
      
      :param resource: Resource to navigate.
      :type resource: str
      
      :param action: Resource Action to navigate.
      :type action: str
      
      :param locale: The page language to use. Sets the user option when authenticated.
      :type locale: str
      

      
      
      

    

  

----------------------------------------------------------------------------



Partners
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Partners

    

    Partners are management entities to administer items such as :class:`Accounts`,
    :class:`Users`, and :class:`Datasets`.

    Partners can perform actions on behalf of users including data management and
    login (SSO).


  

    .. py:method:: get_account_associations(partner_id, account, externalAccount, owned)

      Retrieve List of Account Relationships.

      Account Relationships are associations between a Partner and an Account withinthe MarketSight platform, used to map between Account identifiers within theMarketSight platform and the corresponding Account identifiers within thePartner's platform.When `externalAccount` is specified or `account` is specified, `partner_id` can be omitted.In this case, only associations visible to the current security context are returned

      
      :param partner_id: ID of the MarketSight Partner
      :type partner_id: str
      
      :param account: ID of the MarketSight Account
      :type account: str
      
      :param externalAccount: ID of the Account in the Partner's platform
      :type externalAccount: str
      
      :param owned: If present, will only return relationships where the Partner owns the Account
      :type owned: boolean
      

      
      
      
      :rtype: `CollectionOfPartnerAccountAssociation`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example 1: get partner to account associations by  external_id of account
          result = api.Partners.get_account_associations(
              partner_id="-",
              externalAccount="dcfb30a5e40449479c79b02df3b4a774"
          )
          print (result.items)

          # Example 2: get all accounts associated with the partner
          result = api.Partners.get_account_associations(partner_id="cdbf0a44cbcc4ff281be9ab6cb273573")
          for item in result.items:
              print (item.account)
          
    

  

    .. py:method:: get_account_association(partner_id, account_id)

      Retrieve an Account Relationship.

      Account Relationships are associations between a Partner and an Account withinthe MarketSight platform, used to map between Account identifiers within theMarketSight platform and the corresponding Account identifiers within thePartner's platform

      
      :param partner_id: ID of the MarketSight Partner
      :type partner_id: str
      
      :param account_id: ID of the MarketSight Account
      :type account_id: str
      

      
      
      
      :rtype: `PartnerAccountAssociation`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example:

          assoc = api.Partners.get_account_association(
              partner_id="a0ba0fcdd5664c9a96093db2e52dbc3a",
              account_id="84f707e562964128a8123bd6a2fe1120"
          )
          print (assoc)
          
    

  

    .. py:method:: get_dataset_associations(partner_id, user, externalUser, dataset, externalDataset, datasetAccount)

      Retrieve List of Dataset Relationships.

      Dataset Relationships are associations between a Partner and a Dataset withinthe MarketSight platform. They are used to map MarketSight's Dataset identifiersto the unique identifiers used for those Datasets within the Partner's platform.            When `externalUser` is specified, `partner_id` needs to be specified as well.            When `externalDataset` is specified or `dataset` is specified, `partner_id` can be omitted.In this case, only associations visible to the current security context are returned

      
      :param partner_id: ID of the MarketSight Partner
      :type partner_id: str
      
      :param user: ID of a MarketSight User to search for
      :type user: str
      
      :param externalUser: ID of the User in the Partner's platform
      :type externalUser: str
      
      :param dataset: ID of the Dataset to search for
      :type dataset: str
      
      :param externalDataset: ID of the Dataset in the Partner's platform
      :type externalDataset: str
      
      :param datasetAccount: ID of the MarketSight Account to search
      :type datasetAccount: str
      

      
      
      
      :rtype: `CollectionOfPartnerDatasetAssociation`_
      
      

    

  

    .. py:method:: post_dataset_association(partner_id, model)

      Create a new dataset association.

      

      
      :param partner_id: MarketSight partner ID for this new dataset association.
      :type partner_id: str
      
      :param model: Information to create a dataset association.
      :type model: 
      

      
      
      
      :rtype: `PartnerDatasetAssociation`_
      
      

    

  

    .. py:method:: get_user_associations(partner_id, user, externalUser, owned)

      Retrieve List of User Relationships.

      User Relationships are associations between a Partner and a User record withinthe MarketSight platform, used to map between a unique User within the MarketSightplatform and the corresponding unique User within the Partner's platform.When `externalUser` is specified or `user` is specified, `partner_id` can be omitted.In this case, only associations visible to the current security context are returned

      
      :param partner_id: ID of the MarketSight Partner
      :type partner_id: str
      
      :param user: ID of the MarketSight User
      :type user: str
      
      :param externalUser: ID of the User in the Partner's platform
      :type externalUser: str
      
      :param owned: If present, will only return relationships where the Partner owns the User
      :type owned: boolean
      

      
      
      
      :rtype: `CollectionOfPartnerUserAssociation`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: print all users' id associated with the partner
          for assoc in api.Partners.get_user_associations(
                  partner_id=partner.id
          ).items:
              print(assoc.user)
          
    

  

    .. py:method:: post_user_association(partner_id, model)

      Create a User Relationship.

      User Relationships are associations between a Partner and a User record withinthe MarketSight platform, used to map between a unique User within the MarketSightplatform and the corresponding unique User within the Partner's platform

      
      :param partner_id: ID of the Partner that owns the relationship
      :type partner_id: str
      
      :param model: Identifiers for the User from both MarketSight and the Partner's platform
      :type model: 
      

      
      
      
      :rtype: `PartnerUserAssociation`_
      
      

    

  

    .. py:method:: delete_user_associations(partner_id, user, externalUser, owned)

      Delete user associations.

      

      
      :param partner_id: MarketSight partner ID that owns this association of user records.
      :type partner_id: str
      
      :param user: MarketSight user ID to delete from association.
      :type user: str
      
      :param externalUser: Id of associated user in the partner's platform.
      :type externalUser: str
      
      :param owned: Additional qualifier for this action.
      :type owned: boolean
      

      
      
      

    

  

    .. py:method:: get_user_association(partner_id, user_id)

      Retrieve a User Relationship.

      User Relationships are associations between a Partner and a User record withinthe MarketSight platform, used to map between a unique User within the MarketSightplatform and the corresponding unique User within the Partner's platform

      
      :param partner_id: ID of the MarketSight Partner
      :type partner_id: str
      
      :param user_id: ID of the MarketSight User
      :type user_id: str
      

      
      
      
      :rtype: `PartnerUserAssociation`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: print all user's id and  externalId for specific partner

          for user in api.Users.list(account_id=account).items:
              association = api.Partners.get_user_association(
                  partner_id="813f220d21424683b1451c0eea8e55a8",
                  user_id=user.id
              )
              print(f"user_id={user.id}, externalId={association.externalId}")
          
    

  

    .. py:method:: patch_user_association(partner_id, user_id, model)

      Update a User Relationship.

      User Relationships are associations between a Partner and a User record withinthe MarketSight platform, used to map between a unique User within the MarketSightplatform and the corresponding unique User within the Partner's platform

      
      :param partner_id: ID of the Partner that owns this relationship
      :type partner_id: str
      
      :param user_id: ID of the User within the relationship
      :type user_id: str
      
      :param model: Properties that describe the relationship
      :type model: 
      

      
      
      
      :rtype: `PartnerUserAssociation`_
      
      

    

  

    .. py:method:: put_user_association(partner_id, user_id, model)

      Configure a User Relationship.

      User Relationships are associations between a Partner and a User record withinthe MarketSight platform, used to map between a unique User within the MarketSightplatform and the corresponding unique User within the Partner's platform

      
      :param partner_id: ID of the Partner that owns this relationship
      :type partner_id: str
      
      :param user_id: ID of the User within the relationship
      :type user_id: str
      
      :param model: Properties that describe the relationship
      :type model: 
      

      
      
      
      :rtype: `PartnerUserAssociation`_
      
      

    

  

    .. py:method:: delete_user_association(partner_id, user_id)

      Remove a User Relationship.

      User Relationships are associations between a Partner and a User record withinthe MarketSight platform, used to map between a unique User within the MarketSightplatform and the corresponding unique User within the Partner's platform

      
      :param partner_id: ID of the Partner that owns this relationship
      :type partner_id: str
      
      :param user_id: ID of the User whose relationship should be removed
      :type user_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          api.Partners.delete_user_association(
              partner_id="5dbadbe1f9604af5b3a5d896a302f93e",
              user_id="a897c4527ffa439c9b4e27559a316034"
          )
          
    

  

    .. py:method:: get_consents(partner_id)

      Retrieve consents.

      

      
      :param partner_id: MarketSight partner ID to query.
      :type partner_id: str
      

      
      
      
      :rtype: `CollectionOfPartnerUserConsent`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          for consent in api.Partners.get_consents(partner_id=partner.id).items:
              print(consent.user, consent.timestamp
          
    

  

    .. py:method:: get_consent(partner_id, user_id)

      Retrieve a consent.

      

      
      :param partner_id: MarketSight partner ID to query.
      :type partner_id: str
      
      :param user_id: MarketSight user ID to query.
      :type user_id: str
      

      
      
      
      :rtype: `PartnerUserConsent`_
      
      

    

  

    .. py:method:: get_user_token(partner_id, user_id, scope, autoConsent, lifetime)

      Generate a user Bearer authentication (OAuth) token.

      

      
      :param partner_id: MarketSight partner ID making the request on behalf of a managed user.
      :type partner_id: str
      
      :param user_id: MarketSight user ID.
      :type user_id: str
      
      :param scope: OAuth scope for the token, space separated.
      :type scope: str
      
      :param autoConsent: Indicated whether to automatically add user consent if required.
      :type autoConsent: boolean
      
      :param lifetime: Token lifetime in d.hh:mm:ss format.
      :type lifetime: str
      

      
      
      

    

  

    .. py:method:: get_user_refresh_token(partner_id, user_id, scope, autoConsent, lifetime)

      Generate a user refresh token.

      

      
      :param partner_id: MarketSight partner ID making the request on behalf of a managed user.
      :type partner_id: str
      
      :param user_id: MarketSight user ID.
      :type user_id: str
      
      :param scope: OAuth scope for the token, space separated.
      :type scope: str
      
      :param autoConsent: Indicated whether to automatically add user consent if required.
      :type autoConsent: boolean
      
      :param lifetime: Token lifetime in d.hh:mm:ss format.
      :type lifetime: str
      

      
      
      

    

  

----------------------------------------------------------------------------



Users
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Users

    

    :class:`Users` are people who are associated with an :class:`Account <Accounts>`.
    Throughout the MarketSight platform, they are uniquely identified by their
    username, which may or may not be an email address and is used to authenticate
    the User both within the MarketSight application and the MarketSight API.

    Users are allowed to perform various actions depending on their Account's type
    and the permissions assigned to them.

    Users may also have access to multiple :class:`Accounts`.


  

    .. py:method:: list(account_id, username, email, usernameOrEmail, role, status, homeAccount.status)

      Retrieve List of Users.

      This endpoint is available for Partners, System Administrators and Account Administrators.            When called by a user who is not a System Administrator, the `account_id`must be supplied in the URL and the user making the request must be an administratorof that account.            When called by a Partner, only Users the Partner can access are returned.

      
      :param account_id: ID of the Account whose Users should be returned
      :type account_id: str
      
      :param username: Username to retrieve
      :type username: str
      
      :param email: Email address to retrieve
      :type email: str
      
      :param usernameOrEmail: Retrieves Users with an email or username that matches
      :type usernameOrEmail: str
      
      :param role: Filters results to Users with the User Role supplied
      :type role: str
      
      :param status: Filters results to Users with the User Status indicated
      :type status: str
      
      :param homeAccount.status: Filters results to Users whose Account has a matching status
      :type homeAccount.status: str
      

      
      
      
      :rtype: `UsersCollectionOut`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          for user in api.Users.list(account_id="f370b20c63534141960c0f3e977ac756").items:
              print(user.firstName, user.email)

          # Find user by username or Email
          result = api.Users.list(
              account_id="f370b20c63534141960c0f3e977ac756",
              usernameOrEmail="some@email.com",
          )
          if result.count > 0:
              user = result.items[0]
              print(user.firstName, user.email)

          # find user by `username` only
            result = api.Users.list(
              account_id="f370b20c63534141960c0f3e977ac756", username="test_username"
          )
          if result.items:
              user = result.items[0]
              print(user.username, user.email)

          
    

  

    .. py:method:: create(account_id, model, sendEmail)

      Create a User.

      

      
      :param account_id: ID of the Account with which the newly-created User should be associated
      :type account_id: str
      
      :param model: Properties of the newly-created User
      :type model: 
      
      :param sendEmail: 
      :type sendEmail: boolean
      

      
      
      
      :rtype: `UserOut`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # example
          user = api.Users.create(
              account_id="a2d9c4d7f6c244a399794595b7f53ee5",
              model={
                  "password": "d868701d-6c26-422d-a2cf-ae9a9af1905e",
                  "userName": "username_test",
                  "firstName": "FirstName_test",
                  "lastName": "ln-b8b38ec7-2134-401a-8281-83dfe0658a7c",
                  "email": "f4e01fa2-fc4e-4e24-ab7f-e7d3a24eb248@example.com",
                  "role": "fullAccess",
                  "status": "active",
                  "partnerUserId": "8cabc4f5-5633-4eb0-8f43-7586c8a851a6"
              }
          )
          print(user.id, user.firstName, user.email)
          
    

  

    .. py:method:: retrieve(account_id, user_id)

      Retrieve a User.

      

      
      :param account_id: ID of the Account whose User should be retrieved
      :type account_id: str
      
      :param user_id: ID of the User to retrieve
      :type user_id: str
      

      
      
      
      :rtype: `User`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          user = api.Users.retrieve(user_id="6a8cd823c9704113ab5a434ccf51524c")
          print(user.id, user.firstName, user.email)
          
    

  

    .. py:method:: modify(account_id, user_id, model)

      Update a User.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: ID of the User to update
      :type user_id: str
      
      :param model: Updated properties for the User
      :type model: 
      

      
      
      
      :rtype: `User`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example 1: update first name for user
          user_id = "2c22eeec5fcd45398c1bb5a3f931e61d"
          user = api.Users.retrieve(user_id=user_id)
          user.firstName = "NewFistName"

          api.Users.modify(user_id=user.id, model=user)

          # the same result can be done more simpler:

          api.Users.modify(user_id, model={"firstName": "NewFirstName"})
          
    

  

    .. py:method:: get_administered_accounts(account_id, user_id)

      Retrieve Accounts Administered by a User.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: ID of the User whose administered Accounts should be retrieved
      :type user_id: str
      

      
      
      
      :rtype: `CollectionOfAccountAdministrator`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Getting all administered account(s) for some user
          print (
              api.Users.get_administered_accounts(
                  user_id="98a0c97c66f54e0f9e0989ba1bd76284"
              ).items
          )
          
    

  

    .. py:method:: post_account_administrator(account_id, user_id, administered_account_id)

      Promote User to Account Administrator for an Account.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: ID of the User to make an Account Administrator
      :type user_id: str
      
      :param administered_account_id: ID of the Account where the User should be made an Administrator
      :type administered_account_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Getting all administered account(s) for some user
          print (
              api.Users.get_administered_accounts(
                  user_id="b6e8f88bb63047d686a20568f8d0622b"
              ).items
          )
          
    

  

    .. py:method:: put_account_administrator(account_id, user_id, administered_account_id)

      Promote User to Account Administrator for an Account.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: ID of the User to make an Account Administrator
      :type user_id: str
      
      :param administered_account_id: ID of the Account where the User should be made an Administrator
      :type administered_account_id: str
      

      
      
      

    

  

    .. py:method:: delete_account_administrator(account_id, user_id, administered_account_id)

      Revoke a User's Account Administrator rights on an Account.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: ID of the User whose Account Administration rights should be revoked
      :type user_id: str
      
      :param administered_account_id: ID of the Account where the User's Account Administrator rights should be revoked
      :type administered_account_id: str
      

      
      
      
      
      

    

  

    .. py:method:: get_locale(account_id, user_id)

      Retrieve a User's Locale.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: ID of the User whose localization will be retrieved
      :type user_id: str
      

      
      
      
      :rtype: `Locale`_
      
      

    

  

    .. py:method:: set_locale(account_id, user_id, locale)

      Set a User's Locale.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param user_id: ID of the User whose locale should be set
      :type user_id: str
      
      :param locale: Locale to apply
      :type locale: 
      

      
      
      

    

  

----------------------------------------------------------------------------



Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  .. py:class:: Variables

    

    :class:`Variables` are columns of data in your respondent-level
    :class:`Datasets`. You can think of them as properties that apply to each of
    your respondents, and they typically represent the answer that the respondent
    gave to one of your survey questions.

    The Dynata Reporting & Analytics platform supports a variety of
    :class:`Variables`, including:

      * :term:`Discrete Variables <Discrete Variable>`
      * :term:`Continuous Variables <Continuous Variable>`
      * :term:`Text Variables <Text Variable>`
      * :term:`Date / Time Variables <Date / Time Variable>`
      * :term:`Multiple Response Variables <Multiple Response Variable>`
      * :term:`Weight Variables <Weight Variable>`
      * :term:`User-defined Variables <User-defined Variable>`

        * :term:`Regrouping Variables <Regrouping Variable>`
        * :term:`Conditional Variables <Conditional Variable>`
        * :term:`Mathematical Variables <Mathematical Variable>`
        * :term:`Filter Variables <Filter Variable>`

    Typically, :class:`Variables` will be automatically created for you when you
    upload your :class:`Datasets` into the platform. However, you can also
    manipulate them:

      * programmatically using the :class:`Variables` resource
      * using the :term:`Dataset Labeling Workbook` for your dataset
      * inside the MarketSight user interface


  

    .. py:method:: get_categories(account_id, dataset_id)

      Retrieve List of Dataset Variable Categories.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset
      :type dataset_id: str
      

      
      
      
      :rtype: `CollectionOfVariableCategory`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          for category in api.Variables.get_categories(
              dataset_id="20f91f0333a544b1b713cb1a19341178"
          ).items:
              print(
                  category.id,
                  category.dataset,
                  category.name,
                  category.description
              )
          
    

  

    .. py:method:: post_category(account_id, dataset_id, model)

      Create a Dataset Variable Category.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset
      :type dataset_id: str
      
      :param model: Category Properties
      :type model: 
      

      
      
      
      :rtype: `VariableCategory`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          dataset_id="be434121d59a467783a67571903a06f5"
          result = api.Variables.post_category(
              model=dict(
                  dataset=dataset_id,
                  name="NAME OF NEW CATEGORY",
              )
          )
          print(result)
          
    

  

    .. py:method:: delete_categories(account_id, dataset_id, categories)

      Delete Dataset Variable Categories.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param categories: List of categories to delete.
      :type categories: 
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example:
          # Creating new category checking that it exists and

          import secrets

          dataset = "e9be8029fc644210b579076359973a47"

          category = api.Variables.post_category(
              model=dict(
                  dataset=dataset,
                  name=secrets.token_urlsafe(16),  # make random name for category
              )
          )
          # checking that category is available for dataset

          assert category in api.Variables.get_categories(
              dataset_id=dataset
          ).items

          # deleting the new category
          api.Variables.delete_categories(
              dataset_id=dataset,
              categories=[category.name]
          )
          # checking that category doesn't exist in dataset
          assert category not in api.Variables.get_categories(
              dataset_id=dataset
          ).items
          
    

  

    .. py:method:: get_category(account_id, dataset_id, category_name)

      Retrieve a Variable Category.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param category_name: Name of the Category to retrieve
      :type category_name: str
      

      
      
      
      :rtype: `VariableCategory`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: check if category exists with the name "test_category"
          result = api.Variables.get_category(
              dataset_id="d45f865976bd4acf89fa780c8f68c2da",
              category_name="test_category"
          )
          if result:
              # do something here
              pass
          
    

  

    .. py:method:: patch_category(account_id, dataset_id, category_name, model)

      Update a Variable Category.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param category_name: Name of the Category to update
      :type category_name: str
      
      :param model: Updated Category Properties
      :type model: 
      

      
      
      
      :rtype: `VariableCategory`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: change the description and name in `test_category`
          updated_category = api.Variables.patch_category(
              dataset_id="0add50c38ce44a3393302d681f5731de",
              category_name="test_category",
              model=dict(
                  description="new description for category",
                  name="new_test_category",
              )
          )
          print (updated_category.name)
          print (updated_category.description)

          
    

  

    .. py:method:: put_category(account_id, dataset_id, category_name, model)

      Create or Update a Variable Category.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param category_name: Name of the category to update.
      :type category_name: str
      
      :param model: Category Properties
      :type model: 
      

      
      
      
      :rtype: `VariableCategory`_
      
      

    

  

    .. py:method:: delete_category(account_id, dataset_id, category_name)

      Delete a Variable Category.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param category_name: Name of the Category to delete
      :type category_name: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          api.Variables.delete_category(
              dataset_id="6e3617f3d5224fbf924b05fb44ca3735",
              category_name="some_test_category_name"
          )
          
    

  

    .. py:method:: get_category_variables(account_id, dataset_id, category_name)

      Retrieve List of Variables in Category.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param category_name: Name of the category to search
      :type category_name: str
      

      
      
      
      :rtype: `CollectionOfVariable`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: get list of variables related to `category_name`
          result = api.Variables.get_category_variables(
              dataset_id="9cc5c069abd14d5bb133572ed053dbc1",
              category_name="category_name"
          )
          for variable in result.items:
              print(variable.code)

          
    

  

    .. py:method:: list(account_id, dataset_id)

      Retrieve List of Dataset Variables.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset
      :type dataset_id: str
      

      
      
      
      :rtype: `CollectionOfVariable`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
           for variable in api.Variables.list(dataset_id="7478eb6782304f65892a4756f58417e0").items:
              print(variable.code)
          
    

  

    .. py:method:: create(account_id, dataset_id, model)

      Create a new variable.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `Variable`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: create variable

          dataset_id="3eff6ebe614d46a2bc429c51d736da6a"
          variable = api.Variables.create(
              dataset_id=dataset_id,
              model=dict(
                  category="category_name",
                  code=f"ExampleVariable",
                  dataset=dataset_id,
                  type='numeric',
                  label="some label for ExampleVariable"
              )
          )
          print(variable.id)
          print(variable.code)
          
    

  

    .. py:method:: delete_variables(account_id, dataset_id, model)

      Delete Multiple Dataset Variables.

      *ALL* variables must belong to the same dataset.

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: ID of the Dataset
      :type dataset_id: str
      
      :param model: List of variables to delete.
      :type model: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: remove variables with code "CSLTestVariable1", "CSLTestVariable2"
          api.Variables.delete_variables(
              dataset_id="933620698d87419190a0334f51576487",
              model=dict(variables=["CSLTestVariable1", "CSLTestVariable2"])
          )
          
    

  

    .. py:method:: retrieve(account_id, dataset_id, variable_code)

      Retrieve a Dataset Variable.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: Reference to variable to retrieve.
      :type variable_code: str
      

      
      
      
      :rtype: `Variable`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
           variable = api.Variables.retrieve(
               dataset_id=<_od>,
               variable_code="ExampleVariable"
          )
          print(variable.id)
          
    

  

    .. py:method:: delete(account_id, dataset_id, variable_code)

      Delete a variable.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: MarketSight variable Code or ID.
      :type variable_code: str
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example
          api.Variables.delete(
              dataset_id="0229a23ef0e9495fbebcc8a6ba009e84",
              variable_code="ExampleVariable"
          )
          
    

  

    .. py:method:: modify(account_id, dataset_id, variable_code, model)

      Change an existing variable.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: MarketSight variable Code or ID.
      :type variable_code: str
      
      :param model: All properties are optional
      :type model: 
      

      
      
      
      :rtype: `Variable`_
      
      
      
      
      :rtype: `Job`_
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          # Example: change the `label` of variable
          variable = api.Variables.modify(
              dataset_id="55c8bf2c7bda4f8faf15d9fd56ffae69",
              variable_code="CSLTestVariable",
              model={
                  'label': "new label text"
              }
          )
          print(variable.label)
          
    

  

    .. py:method:: get_templates(account_id, dataset_id, variable_code, kind)

      Retrieve a crosstab design variable templates.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: Reference to variable to retrieve.
      :type variable_code: str
      
      :param kind: Type of crosstab design variable.
      :type kind: str
      

      
      
      
      :rtype: `CrosstabVariableTemplate`_
      
      

    

  

    .. py:method:: get_grid_set_templates(account_id, dataset_id, variable_code, kind)

      Retrieve a crosstab design variable templates for grid set.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: Grid set variable code.
      :type variable_code: str
      
      :param kind: Type of crosstab design variable.
      :type kind: str
      

      
      
      
      :rtype: `CrosstabVariableTemplate`_
      
      

    

  

    .. py:method:: get_precedent_variables(account_id, dataset_id, variable_code)

      Retrieve precedent variables.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: MarketSight variable Code or ID.
      :type variable_code: str
      

      
      
      
      :rtype: `CollectionOfVariable`_
      
      

    

  

    .. py:method:: get_values(account_id, dataset_id, variable_code)

      Retrieve list of Variable Values.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: Reference to variable
      :type variable_code: str
      

      
      
      

    

  

    .. py:method:: put_values(account_id, dataset_id, variable_code, values)

      Set variable values.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: MarketSight variable Code or ID.
      :type variable_code: str
      
      :param values: 
      :type values: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    

  

    .. py:method:: format_values(account_id, dataset_id, variable_code, values)

      Retrieve list of Variable Values formatted with user settings.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: Reference to variable
      :type variable_code: str
      
      :param values: Values to format
      :type values: 
      

      
      
      

    

  

    .. py:method:: get_grid_set(account_id, dataset_id, variable_code)

      Try to create a Numeric Grid Set from a variable.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: Reference to variable
      :type variable_code: str
      

      
      
      

    

  

    .. py:method:: get_weight_values(account_id, dataset_id, variable_code)

      Retrieve list of Variable Values (Weight Variables only).

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: Reference to variable
      :type variable_code: str
      

      
      
      
      :rtype: `WeightVariableValues`_
      
      

    

  

    .. py:method:: put_weight_values(account_id, dataset_id, variable_code, model)

      Set weight variable values.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID.
      :type dataset_id: str
      
      :param variable_code: MarketSight variable Code or ID.
      :type variable_code: str
      
      :param model: 
      :type model: 
      

      
      
      
      :rtype: `AsyncResult`_
      
      
      
      

    

  

    .. py:method:: get_grid_sets(account_id, dataset_id)

      Retrieve grid sets.

      

      
      :param account_id: MarketSight account ID.
      :type account_id: str
      
      :param dataset_id: MarketSight dataset ID for this inquiry.
      :type dataset_id: str
      

      
      
      

    
      :example:

      .. code-block:: python
         :linenos:

               
          #Example:
          print (api.Variables.get_grid_sets(dataset_id="d295f9397d50434c87fd32a33af1aee1"))
          
    

  

----------------------------------------------------------------------------



.. _object_models:

Object Models
-----------------

The Dynata Reporting & Analytics API relies on a wide variety of object models,
each of which is used as either a payload or a response object returned by the
**MarketSight API Client**. Below you will find a list of those object models,
along with documentation about their properties and their meaning.



.. _OAuthTokenResponse:

OAuthTokenResponse
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - userId 
       - string  
       - 
       - ID of the Authenticated MarketSight User 
     
     * - partnerId 
       - string  
       - 
       - MarketSight Partner ID (equivalent to `client_id`) 
     
     * - partnerUserId 
       - string  
       - 
       - Authenticated Partner User ID, if any 
     
     * - token_type 
       - string  
       - 
       - 
     
     * - access_token 
       - string  
       - 
       - 
     
     * - refresh_token 
       - string  
       - 
       - 
     
     * - expires_in 
       - integer  
       - 
       - 
     

  



.. _OAuthTokens:

OAuthTokens
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - token_type 
       - string  
       - 
       - 
     
     * - access_token 
       - string  
       - 
       - 
     
     * - refresh_token 
       - string  
       - 
       - 
     
     * - expires_in 
       - integer  
       - 
       - 
     

  



.. _CollectionOfAccountAdministrator:

CollectionOfAccountAdministrator
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `AccountAdministrator`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _AccountAdministrator:

AccountAdministrator
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Declares a User as an Account Administrator. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - user 
       - string  
       - 
       - ID of the User 
     
     * - account 
       - string  
       - 
       - ID of the Account where the User has Administrator rights 
     

  



.. _Account:

Account
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A business entity with a relationship to the MarketSight platform. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       -  required 
       - Name of the Account used for display and reporting 
     
     * - deleted 
       - boolean  
       -  required 
       - 
     
     * - shortName 
       - string  
       - 
       - Alternate name of the Account 
     
     * - stage 
       -   
       - 
       - Billing relationship for the Account 
     
     * - startDate 
       - string  
       - 
       - Date the Account was activated 
     
     * - endDate 
       - string  
       - 
       - Scheduled date the Account will become inactive 
     
     * - status 
       -   
       -  read only 
       - Activation status of the Account. Only `active` Accounts can be used 
     
     * - maxFullAccessUsers 
       - integer  
       - 
       - The permitted number of active Users with access to all configured features 
     
     * - maxReadOnlyUsers 
       - integer  
       - 
       - The permitted number of active Users with read-only access 
     
     * - maxKeyFindingsOnlyUsers 
       - integer  
       - 
       - The permitted number of active Users with access limited to the Key Findings portal 
     
     * - datasetLimits 
       -   
       - 
       - Configuration for Dataset limits placed on the Account 
     
     * - subscription 
       -   
       - 
       - The subscription purchased for the Account 
     
     * - capacityTier 
       - integer  
       - 
       - The high capacity tier (1-5) purchased for the Account 
     
     * - primaryContact 
       - string  
       - 
       - User ID of the primary contact for the Account 
     
     * - customizationPath 
       - string  
       - 
       - Profile name of a pre-configured branding and color scheme to override MarketSight default branding 
     
     * - executive 
       - string  
       - 
       - User ID of the account executive assigned to the Account 
     
     * - features 
       -   
       -  read only 
       - 
     
     * - options 
       -   
       -  read only 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Account") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _AccountStageType:

AccountStageType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to describe the billing relationship for the account. 

  



.. _AccountStageStatus:

AccountStageStatus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to describe the activation status of an account. Only `active` accounts can be used. 

  



.. _DatasetLimits:

DatasetLimits
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration for Dataset limits placed on an Account 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - overriden 
       - boolean  
       -  required 
       - If `true`, the Account is configured to use custom dataset limits. If `false`,the Account will use default dataset limits. 
     
     * - dataSizeLimit 
       - integer  
       -  read only 
       - Maximum size of an allowed dataset in bytes. 
     
     * - respondentsLimit 
       - integer  
       - 
       - Maximum number of respondents allowed in a dataset.Null value indicates no limit. 
     
     * - variablesLimit 
       - integer  
       - 
       - Maximum number of variables allowed in a dataset.Null value indicates no limit. 
     
     * - dataPointsLimit 
       - integer  
       - 
       - Maximum number of data points (variables * respondents) allowed in a dataset.Null value indicates no limit. 
     

  



.. _SubscriptionLevel:

SubscriptionLevel
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to define bundled application features and dataset properties. 

  



.. _Features:

Features
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Feature Configuration 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - allowDatasetUpload 
       - boolean  
       -  required 
       - If `true`, users within the Account are allowed to upload datasetsusing the web application. If `false`, users can only have datasetspopulated via API. 
     

  



.. _Options:

Options
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Additional settings. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - accountLocale 
       -   
       - 
       - Default localization for the Account. 
     

  



.. _Locale:

Locale
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Supported localizations 

  



.. _AccountIn:

AccountIn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Properties to supply when creating a new Account. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - sendEmail 
       - boolean  
       -  required 
       - If `true`, sends an automatic email to welcome new users. If `false`, nowelcome email is sent. 
     
     * - copyDataFromModelAccount 
       - boolean  
       -  required 
       - If `true`, will populate the newly-created Account with items from atemplate account. 
     
     * - externalId 
       - string  
       - 
       - Unique identifier for the Account in the Partner's own platform. 
     
     * - users 
       - list  of  `UserIn`_
       - 
       - Collection of Users to register when creating the new Account. 
     
     * - contactInfo 
       -   
       - 
       - Contact details for the newly created Account. 
     
     * - trialInfo 
       -   
       - 
       - Additional information for a newly-created free trial Account. *Deprecated* 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Account") 
     
     * - name 
       - string  
       - 
       - Name of the Account used for display and reporting 
     
     * - shortName 
       - string  
       - 
       - Alternate name of the Account 
     
     * - stage 
       -   
       - 
       - Billing relationship for the Account 
     
     * - startDate 
       - string  
       - 
       - Date the Account was activated 
     
     * - endDate 
       - string  
       - 
       - Scheduled date the Account will become inactive 
     
     * - status 
       -   
       -  read only 
       - Activation status of the Account. Only `active` Accounts can be used 
     
     * - maxFullAccessUsers 
       - integer  
       - 
       - The permitted number of active Users with access to all configured features 
     
     * - maxReadOnlyUsers 
       - integer  
       - 
       - The permitted number of active Users with read-only access 
     
     * - maxKeyFindingsOnlyUsers 
       - integer  
       - 
       - The permitted number of active Users with access limited to the Key Findings portal 
     
     * - datasetLimits 
       -   
       - 
       - Configuration for Dataset limits placed on the Account 
     
     * - subscription 
       -   
       - 
       - The subscription purchased for the Account 
     
     * - capacityTier 
       - integer  
       - 
       - The high capacity tier (1-5) purchased for the Account 
     
     * - primaryContact 
       - string  
       - 
       - User ID of the primary contact for the Account 
     
     * - customizationPath 
       - string  
       - 
       - Profile name of a pre-configured branding and color scheme to override MarketSight default branding 
     
     * - executive 
       - string  
       - 
       - User ID of the account executive assigned to the Account 
     
     * - features 
       -   
       -  read only 
       - 
     
     * - options 
       -   
       -  read only 
       - 
     
     * - deleted 
       - boolean  
       - 
       - 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _UserIn:

UserIn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Properties to supply when creating a new User. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - partnerUserId 
       - string  
       - 
       - Unique identifier for the User in the Partner's own platform. 
     
     * - partnerUserName 
       - string  
       - 
       - Username for the User in the Partner's own platform. 
     
     * - password 
       - string  
       - 
       - This property is ignored as a newly-created User will receive an automaticemail asking them to set their password. 
     
     * - tokens 
       -   
       - 
       - Tokens used to grant the Partner's platform access to the newly-created User's data. 
     
     * - contactInfo 
       -   
       - 
       - Contact information for the newly-created User 
     
     * - currentAccount 
       - string  
       -  read only 
       - ID of the Account last accessed by the User            For non-administrator users, this will always be the same as `HomeAccount`. 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("User") 
     
     * - userName 
       - string  
       - 
       - Username that the User can use to log into the MarketSight platformMust be unique across all Accounts and Partners. 
     
     * - firstName 
       - string  
       - 
       - The User's given name 
     
     * - middleName 
       - string  
       - 
       - The User's middle name or initial 
     
     * - lastName 
       - string  
       - 
       - The User's family name 
     
     * - email 
       - string  
       - 
       - The User's email address.Used to reset passwords and other correspondence. 
     
     * - company 
       - string  
       - 
       - The name of the User's company or organization. 
     
     * - title 
       - string  
       - 
       - The User's job title 
     
     * - expirationDate 
       - string  
       - 
       - If set, represents the last day on which the User can access MarketSight 
     
     * - role 
       -   
       - 
       - The level of permissions granted to the User 
     
     * - status 
       -   
       - 
       - Indication of the User account's condition.            Only `active` users can be used. 
     
     * - comped 
       - boolean  
       - 
       - If `true`, the User does not count against the `maxFullAccessUsers` limit.If `false`, the User does count towards the `maxFullAccessUsers` limit. 
     
     * - homeAccount 
       - string  
       -  read only 
       - ID of the Account that owns the User record 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _ContactInfo:

ContactInfo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Contact and address information 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - isAccountInfo 
       - boolean  
       -  required 
       - If `true`, record applies to an Account. 
     
     * - city 
       - string  
       - 
       - City 
     
     * - country 
       - string  
       - 
       - Country 
     
     * - fax 
       - string  
       - 
       - Fax number 
     
     * - phone 
       - string  
       - 
       - Phone number 
     
     * - postalCode 
       - string  
       - 
       - Zip code 
     
     * - state 
       - string  
       - 
       - State 
     
     * - street1 
       - string  
       - 
       - Street address (line 1) 
     
     * - street2 
       - string  
       - 
       - Street address (line 2) 
     

  



.. _User:

User
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - userName 
       - string  
       -  required 
       - Username that the User can use to log into the MarketSight platformMust be unique across all Accounts and Partners. 
     
     * - firstName 
       - string  
       -  required 
       - The User's given name 
     
     * - lastName 
       - string  
       -  required 
       - The User's family name 
     
     * - email 
       - string  
       -  required 
       - The User's email address.Used to reset passwords and other correspondence. 
     
     * - middleName 
       - string  
       - 
       - The User's middle name or initial 
     
     * - company 
       - string  
       - 
       - The name of the User's company or organization. 
     
     * - title 
       - string  
       - 
       - The User's job title 
     
     * - expirationDate 
       - string  
       - 
       - If set, represents the last day on which the User can access MarketSight 
     
     * - role 
       -   
       - 
       - The level of permissions granted to the User 
     
     * - status 
       -   
       - 
       - Indication of the User account's condition.            Only `active` users can be used. 
     
     * - comped 
       - boolean  
       - 
       - If `true`, the User does not count against the `maxFullAccessUsers` limit.If `false`, the User does count towards the `maxFullAccessUsers` limit. 
     
     * - homeAccount 
       - string  
       -  read only 
       - ID of the Account that owns the User record 
     
     * - currentAccount 
       - string  
       -  read only 
       - ID of the Account last accessed by the User            For non-administrator users, this will always be the same as `HomeAccount`. 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("User") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _UserRole:

UserRole
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of user types that define application and administrative access. 

  



.. _UserStatus:

UserStatus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to define the active/inactive state of user accounts. 

  



.. _TrialInfo:

TrialInfo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Additional information for a newly-created free trial Account. *Deprecated* 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - browser 
       - string  
       - 
       - Browser used when requesting a trial Account. *Deprecated* 
     
     * - companyType 
       - string  
       - 
       - Type of company requesting a free trial Account. *Deprecated* 
     
     * - researchersCount 
       - string  
       - 
       - Number of researchers at the trial Account. *Deprecated* 
     
     * - surveySolution 
       - string  
       - 
       - Preferred survey platform used by the trial Account. *Deprecated* 
     
     * - dataAnalysisSolution 
       - string  
       - 
       - Other analytics products used by the trial Account. *Deprecated* 
     
     * - informationSource 
       - string  
       - 
       - Where the trial Account heard about MarketSight. *Deprecated* 
     
     * - interests 
       - list  
       - 
       - Collection of additional interests for the trial Account. *Deprecated* 
     

  



.. _CollectionOfAdvancedAnalysis:

CollectionOfAdvancedAnalysis
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `AdvancedAnalysis`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _AdvancedAnalysis:

AdvancedAnalysis
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - name 
       - string  
       - 
       - Title of the Advanced Analysis 
     
     * - description 
       - string  
       - 
       - Additional notes about the Advanced Analysis 
     
     * - dataset 
       - string  
       -  read only 
       - The ID of the Dataset from which the Advanced Analysis is running 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("AdvancedAnalysis") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _AnalysisType:

AnalysisType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _AdvancedAnalysisDefinition:

AdvancedAnalysisDefinition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - command 
       -   
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("AdvancedAnalysis") 
     
     * - name 
       - string  
       - 
       - Title of the Advanced Analysis 
     
     * - description 
       - string  
       - 
       - Additional notes about the Advanced Analysis 
     
     * - type 
       -   
       - 
       - 
     
     * - dataset 
       - string  
       -  read only 
       - The ID of the Dataset from which the Advanced Analysis is running 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _CorrelationCommand:

CorrelationCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - method 
       -   
       -  required 
       - 
     
     * - commandType 
       -   
       -  required 
       - 
     
     * - correlationVariables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - weightVariables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("ColumnNamingStyleCommand") 
     
     * - namingStyle 
       -   
       - 
       - 
     

  



.. _CorrelationMethod:

CorrelationMethod
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _RCommandVariable:

RCommandVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - code 
       - string  
       -  required 
       - 
     
     * - isDiscrete 
       - boolean  
       -  required 
       - 
     
     * - isOrdinal 
       - boolean  
       -  required 
       - 
     
     * - label 
       - string  
       - 
       - 
     
     * - values 
       - list  of  `RCommandVariableValue`_
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommandVariable") 
     

  



.. _RCommandVariableValue:

RCommandVariableValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - value 
       - number  
       -  required 
       - 
     
     * - label 
       - string  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommandVariableValue") 
     

  



.. _ColumnNamingStyleCommand:

ColumnNamingStyleCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - namingStyle 
       -   
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommand") 
     
     * - code 
       - string  
       - 
       - 
     
     * - filterVariable 
       -   
       - 
       - 
     
     * - precedentVariableCodes 
       - list  
       - 
       - 
     
     * - commandType 
       -   
       - 
       - 
     

  



.. _RColumnNamingStyle:

RColumnNamingStyle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - useCodesAsColumsNames 
       - boolean  
       -  required 
       - 
     

  



.. _RCommand:

RCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - commandType 
       -   
       -  required 
       - 
     
     * - code 
       - string  
       - 
       - 
     
     * - filterVariable 
       -   
       - 
       - 
     
     * - precedentVariableCodes 
       - list  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommand") 
     

  



.. _FactorAnalysisCommand:

FactorAnalysisCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - method 
       -   
       -  required 
       - 
     
     * - rotation 
       -   
       -  required 
       - 
     
     * - extract 
       -   
       -  required 
       - 
     
     * - extractValue 
       - integer  
       -  required 
       - 
     
     * - commandType 
       -   
       -  required 
       - 
     
     * - selectedVariables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommand") 
     
     * - code 
       - string  
       - 
       - 
     
     * - filterVariable 
       -   
       - 
       - 
     
     * - precedentVariableCodes 
       - list  
       - 
       - 
     

  



.. _FactorAnalysisMethod:

FactorAnalysisMethod
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _FactorAnalysisRotation:

FactorAnalysisRotation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _FactorAnalysisExtract:

FactorAnalysisExtract
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _KMeansClusteringCommand:

KMeansClusteringCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - numberOfClusters 
       - integer  
       -  required 
       - 
     
     * - numberOfIterations 
       - integer  
       -  required 
       - 
     
     * - commandType 
       -   
       -  required 
       - 
     
     * - variables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - clusterVariableCode 
       - string  
       - 
       - 
     
     * - clusterVariableName 
       - string  
       - 
       - 
     
     * - distanceVariableCode 
       - string  
       - 
       - 
     
     * - distanceVariableName 
       - string  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("TwoModeFactorCommand") 
     
     * - twoModeFactor 
       -   
       - 
       - 
     

  



.. _TwoModeFactorCommand:

TwoModeFactorCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - twoModeFactor 
       -   
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommand") 
     
     * - code 
       - string  
       - 
       - 
     
     * - filterVariable 
       -   
       - 
       - 
     
     * - precedentVariableCodes 
       - list  
       - 
       - 
     
     * - commandType 
       -   
       - 
       - 
     

  



.. _TwoModeFactor:

TwoModeFactor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - factorizeOrdinals 
       - boolean  
       -  required 
       - 
     
     * - factorizeNominals 
       - boolean  
       -  required 
       - 
     
     * - nominalFactorization 
       -   
       -  required 
       - 
     

  



.. _NominalFactorizationType:

NominalFactorizationType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _LogisticRegressionCommand:

LogisticRegressionCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - method 
       -   
       -  required 
       - 
     
     * - commandType 
       -   
       -  required 
       - 
     
     * - independentVariables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - dependentVariable 
       -   
       - 
       - 
     
     * - weightVariables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommand") 
     
     * - twoModeFactor 
       -   
       - 
       - 
     
     * - code 
       - string  
       - 
       - 
     
     * - filterVariable 
       -   
       - 
       - 
     
     * - precedentVariableCodes 
       - list  
       - 
       - 
     

  



.. _LogisticRegression:

LogisticRegression
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _LogisticRegressionOrderedCommand:

LogisticRegressionOrderedCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - commandType 
       -   
       -  required 
       - 
     
     * - independentVariables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - dependentVariable 
       -   
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommand") 
     
     * - twoModeFactor 
       -   
       - 
       - 
     
     * - code 
       - string  
       - 
       - 
     
     * - filterVariable 
       -   
       - 
       - 
     
     * - precedentVariableCodes 
       - list  
       - 
       - 
     

  



.. _LRCommand:

LRCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - method 
       -   
       -  required 
       - 
     
     * - commandType 
       -   
       -  required 
       - 
     
     * - independentVariables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - dependentVariable 
       -   
       - 
       - 
     
     * - weightVariables 
       - list  of  `RCommandVariable`_
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("RCommand") 
     
     * - code 
       - string  
       - 
       - 
     
     * - filterVariable 
       -   
       - 
       - 
     
     * - precedentVariableCodes 
       - list  
       - 
       - 
     

  



.. _LRMethod:

LRMethod
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _DefinitionAnyRCommand:

DefinitionAnyRCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - CorrelationCommand 
       -   
       - 
       - 
     
     * - FactorAnalysisCommand 
       -   
       - 
       - 
     
     * - KMeansClusteringCommand 
       -   
       - 
       - 
     
     * - LogisticRegressionCommand 
       -   
       - 
       - 
     
     * - LogisticRegressionOrderedCommand 
       -   
       - 
       - 
     
     * - LRCommand 
       -   
       - 
       - 
     
     * - RCommand 
       -   
       - 
       - 
     

  



.. _CollectionOfChart:

CollectionOfChart
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `Chart`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _Chart:

Chart
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Reference to a MarketSight chart. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - description 
       - string  
       -  required 
       - Additional notes about the Chart 
     
     * - name 
       - string  
       - 
       - Title of the Chart 
     
     * - category 
       - string  
       - 
       - The name of the Category to which the Chart has been assigned 
     
     * - dataset 
       - string  
       -  read only 
       - The ID of the Dataset which the Chart belongs to 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Chart") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _ChartIn:

ChartIn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Definition of a Chart 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - definition 
       -   
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Chart") 
     
     * - name 
       - string  
       - 
       - Title of the Chart 
     
     * - description 
       - string  
       - 
       - Additional notes about the Chart 
     
     * - category 
       - string  
       - 
       - The name of the Category to which the Chart has been assigned 
     
     * - dataset 
       - string  
       -  read only 
       - The ID of the Dataset which the Chart belongs to 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _ChartDefinition:

ChartDefinition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   The configuration of a given Chart 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - crosstabData 
       -   
       -  required 
       - 
     
     * - dataViewData 
       -   
       -  required 
       - 
     
     * - design 
       -   
       -  required 
       - 
     
     * - sampleSizeThreshold 
       -   
       -  required 
       - 
     
     * - effects 
       -   
       -  required 
       - 
     
     * - axis 
       -   
       -  required 
       - 
     
     * - colors 
       -   
       -  required 
       - 
     
     * - interactivity 
       -   
       -  required 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("ChartDefinition") 
     

  



.. _ChartType:

ChartType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionCrosstabData:

ChartDefinitionCrosstabData
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - crosstab 
       - string  
       -  required 
       - 
     
     * - rowSectionIds 
       - list  
       -  required 
       - 
     
     * - columnSectionId 
       - string  
       -  required 
       - 
     
     * - calculation 
       -   
       -  required 
       - 
     
     * - useCrosstabRounding 
       - boolean  
       -  required 
       - 
     
     * - showTotals 
       - boolean  
       -  required 
       - 
     
     * - useDataSeriesFromRowValues 
       - boolean  
       -  required 
       - 
     
     * - seriesOrientation 
       -   
       -  required 
       - 
     
     * - showOnly 
       -   
       -  required 
       - 
     
     * - customization 
       -   
       -  required 
       - 
     
     * - series 
       -   
       -  required 
       - 
     

  



.. _ChartDataToPlot:

ChartDataToPlot
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartSeriesOrientation:

ChartSeriesOrientation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionCrosstabDataShowOnly:

ChartDefinitionCrosstabDataShowOnly
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - enabled 
       - boolean  
       -  required 
       - 
     
     * - type 
       -   
       -  required 
       - 
     
     * - length 
       - integer  
       -  required 
       - 
     

  



.. _ChartThresholdType:

ChartThresholdType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionCustomization:

ChartDefinitionCustomization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - labels 
       -   
       -  required 
       - 
     
     * - colors 
       - list  of  `ChartDefinitionCustomColor`_
       -  required 
       - 
     
     * - positions 
       - list  of  `ChartDefinitionCustomPosition`_
       -  required 
       - 
     
     * - lineTypes 
       - list  of  `ChartDefinitionCustomLineType`_
       -  required 
       - 
     
     * - markerTypes 
       - list  of  `ChartDefinitionCustomMarkerType`_
       -  required 
       - 
     

  



.. _ChartDefinitionCustomLabels:

ChartDefinitionCustomLabels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - variables 
       - list  of  `ChartDefinitionCustomVariableLabel`_
       -  required 
       - 
     

  



.. _ChartDefinitionCustomVariableLabel:

ChartDefinitionCustomVariableLabel
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - values 
       - list  of  `ChartDefinitionCustomVariableValueLabel`_
       -  required 
       - 
     
     * - variable 
       - string  
       -  required 
       - 
     
     * - code 
       - number  
       -  required 
       - 
     
     * - label 
       - string  
       - 
       - 
     

  



.. _ChartDefinitionCustomVariableValueLabel:

ChartDefinitionCustomVariableValueLabel
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - code 
       - number  
       -  required 
       - 
     
     * - label 
       - string  
       - 
       - 
     

  



.. _ChartDefinitionCustomColor:

ChartDefinitionCustomColor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - label 
       - string  
       - 
       - 
     
     * - color 
       - string  
       - 
       - 
     

  



.. _ChartDefinitionCustomPosition:

ChartDefinitionCustomPosition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - key 
       - string  
       -  required 
       - 
     
     * - position 
       -   
       -  required 
       - 
     

  



.. _ItemPosition:

ItemPosition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionCustomLineType:

ChartDefinitionCustomLineType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - key 
       - string  
       -  required 
       - 
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _LineType:

LineType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionCustomMarkerType:

ChartDefinitionCustomMarkerType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - key 
       - string  
       -  required 
       - 
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _MarkerType:

MarkerType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Line chart marker type 

  



.. _ChartDefinitionCrosstabDataSeries:

ChartDefinitionCrosstabDataSeries
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - series 
       - list  of  `ChartDefinitionSerie`_
       -  required 
       - 
     

  



.. _ChartDefinitionSerie:

ChartDefinitionSerie
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - sectionId 
       - string  
       -  required 
       - 
     
     * - variables 
       - list  
       -  required 
       - 
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _SeriesType:

SeriesType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionDataViewData:

ChartDefinitionDataViewData
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - dataView 
       - string  
       -  required 
       - 
     
     * - dataVariables 
       - list  
       -  required 
       - 
     
     * - stopWords 
       -   
       -  required 
       - 
     
     * - wordData 
       -   
       -  required 
       - 
     

  



.. _ChartDefinitionStopWords:

ChartDefinitionStopWords
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - enabled 
       - boolean  
       -  required 
       - 
     
     * - culture 
       - string  
       - 
       - 
     

  



.. _ChartDefinitionWordData:

ChartDefinitionWordData
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - enableWordCloudChartStatisticsSwitch 
       - boolean  
       -  required 
       - 
     
     * - enableWordsLimit 
       - boolean  
       -  required 
       - 
     
     * - wordsLimitKind 
       -   
       -  required 
       - 
     
     * - wordsCustomLimit 
       - integer  
       -  required 
       - 
     
     * - wordCloudView 
       -   
       -  required 
       - 
     
     * - selectedWordsCount 
       - integer  
       - 
       - 
     

  



.. _WordsLimitKind:

WordsLimitKind
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _WordCloudView:

WordCloudView
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionDesign:

ChartDefinitionDesign
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - reverse 
       -   
       -  required 
       - 
     
     * - useTemplate 
       - boolean  
       -  required 
       - 
     
     * - area 
       -   
       -  required 
       - 
     
     * - title 
       -   
       -  required 
       - 
     
     * - valueLabels 
       -   
       -  required 
       - 
     
     * - legend 
       -   
       -  required 
       - 
     
     * - footnote 
       -   
       -  required 
       - 
     
     * - settings 
       -   
       -  required 
       - 
     
     * - plotArea 
       -   
       -  required 
       - 
     
     * - gaugeSettings 
       -   
       -  required 
       - 
     
     * - scatterPlot 
       -   
       -  required 
       - 
     

  



.. _ChartDefinitionReverse:

ChartDefinitionReverse
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - reverseLegend 
       - boolean  
       -  required 
       - 
     
     * - reverseCategory 
       - boolean  
       -  required 
       - 
     

  



.. _ChartDefinitionArea:

ChartDefinitionArea
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - position 
       -   
       -  required 
       - 
     
     * - theme 
       -   
       -  required 
       - 
     
     * - backgroundColor 
       - string  
       -  required 
       - 
     
     * - hasBackgroundColor 
       - boolean  
       -  required 
       - 
     
     * - border 
       -   
       -  required 
       - 
     

  



.. _DefinitionPosition:

DefinitionPosition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - width 
       -   
       -  required 
       - 
     
     * - height 
       -   
       -  required 
       - 
     
     * - top 
       -   
       -  required 
       - 
     
     * - bottom 
       -   
       -  required 
       - 
     
     * - left 
       -   
       -  required 
       - 
     
     * - right 
       -   
       -  required 
       - 
     

  



.. _DefinitionDistance:

DefinitionDistance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - value 
       - number  
       -  required 
       - 
     
     * - unit 
       -   
       -  required 
       - 
     

  



.. _Units:

Units
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Represents a unit of measure for . 

  



.. _ColorTheme:

ColorTheme
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionBorder:

ChartDefinitionBorder
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - width 
       - number  
       -  required 
       - 
     
     * - color 
       - string  
       -  required 
       - 
     
     * - hasColor 
       - boolean  
       -  required 
       - 
     

  



.. _ChartDefinitionTitle:

ChartDefinitionTitle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - placement 
       -   
       -  required 
       - 
     
     * - position 
       -   
       -  required 
       - 
     
     * - font 
       -   
       -  required 
       - 
     

  



.. _ChartTitlePlacement:

ChartTitlePlacement
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionTitlePosition:

ChartDefinitionTitlePosition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - top 
       -   
       -  required 
       - 
     
     * - left 
       -   
       -  required 
       - 
     

  



.. _DefinitionFont:

DefinitionFont
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - style 
       -   
       -  required 
       - 
     
     * - size 
       - number  
       -  required 
       - 
     
     * - name 
       - string  
       - 
       - 
     

  



.. _FontStyle:

FontStyle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionValueLabels:

ChartDefinitionValueLabels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - placement 
       -   
       -  required 
       - 
     
     * - font 
       -   
       -  required 
       - 
     

  



.. _ValueLabelPlacement:

ValueLabelPlacement
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionLegend:

ChartDefinitionLegend
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - font 
       -   
       -  required 
       - 
     
     * - placement 
       -   
       -  required 
       - 
     
     * - showBorder 
       - boolean  
       -  required 
       - 
     

  



.. _ChartLegendPlacement:

ChartLegendPlacement
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionFootnote:

ChartDefinitionFootnote
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - font 
       -   
       -  required 
       - 
     
     * - content 
       -   
       -  required 
       - 
     

  



.. _ChartDefinitionFootnoteContent:

ChartDefinitionFootnoteContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - sampleSize 
       - boolean  
       -  required 
       - 
     
     * - unweightedSampleSize 
       - boolean  
       -  required 
       - 
     
     * - description 
       - boolean  
       -  required 
       - 
     
     * - tooltips 
       - boolean  
       -  required 
       - 
     
     * - filterFootnote 
       - boolean  
       -  required 
       - 
     
     * - filterDetails 
       - boolean  
       -  required 
       - 
     
     * - sampleBase 
       - boolean  
       -  required 
       - 
     
     * - unweightedSampleBase 
       - boolean  
       -  required 
       - 
     
     * - validCases 
       - boolean  
       -  required 
       - 
     
     * - totalMentions 
       - boolean  
       -  required 
       - 
     
     * - unweightedTotalMentions 
       - boolean  
       -  required 
       - 
     
     * - totalUniqueMentions 
       - boolean  
       -  required 
       - 
     
     * - statisticalSignificance 
       -   
       -  required 
       - 
     

  



.. _ChartDefinitionFootnoteDisplayStatisticalSignificance:

ChartDefinitionFootnoteDisplayStatisticalSignificance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - enabled 
       - boolean  
       -  required 
       - 
     
     * - strategy 
       -   
       -  required 
       - 
     

  



.. _ChartStatisticalResultsLabelingStrategy:

ChartStatisticalResultsLabelingStrategy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionSettings:

ChartDefinitionSettings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - showTooltips 
       - boolean  
       -  required 
       - 
     
     * - autoResize 
       - boolean  
       -  required 
       - 
     
     * - autoScaleFonts 
       - boolean  
       -  required 
       - 
     

  



.. _ChartDefinitionPlotArea:

ChartDefinitionPlotArea
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - border 
       -   
       -  required 
       - 
     
     * - showHorizontalGridLines 
       - boolean  
       -  required 
       - 
     
     * - showVerticalGridLines 
       - boolean  
       -  required 
       - 
     

  



.. _ChartDefinitionGaugeSettings:

ChartDefinitionGaugeSettings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - label 
       -   
       -  required 
       - 
     
     * - radialSettings 
       -   
       -  required 
       - 
     
     * - kpiSettings 
       -   
       -  required 
       - 
     
     * - ranges 
       -   
       -  required 
       - 
     
     * - tickMarks 
       -   
       -  required 
       - 
     
     * - indicatorColor 
       - string  
       -  required 
       - 
     

  



.. _ChartDefinitionGaugeLabel:

ChartDefinitionGaugeLabel
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - font 
       -   
       -  required 
       - 
     
     * - color 
       - string  
       -  required 
       - 
     

  



.. _ChartDefinitionGaugeRadialSettings:

ChartDefinitionGaugeRadialSettings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - labelsInside 
       - boolean  
       -  required 
       - 
     
     * - beginAngle 
       - number  
       -  required 
       - 
     
     * - endAngle 
       - number  
       -  required 
       - 
     

  



.. _ChartDefinitionGaugeKpiSettings:

ChartDefinitionGaugeKpiSettings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - decimalPlaces 
       - number  
       -  required 
       - 
     
     * - showPercentSign 
       - boolean  
       -  required 
       - 
     

  



.. _ChartDefinitionGaugeRanges:

ChartDefinitionGaugeRanges
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - full 
       -   
       -  required 
       - 
     
     * - ranges 
       - list  of  `ChartDefinitionGaugeRange`_
       -  required 
       - 
     

  



.. _ChartDefinitionGaugeRange:

ChartDefinitionGaugeRange
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - active 
       - boolean  
       -  required 
       - 
     
     * - begin 
       - number  
       -  required 
       - 
     
     * - end 
       - number  
       -  required 
       - 
     
     * - color 
       - string  
       -  required 
       - 
     

  



.. _ChartDefinitionGaugeTickMarks:

ChartDefinitionGaugeTickMarks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - major 
       -   
       -  required 
       - 
     
     * - minor 
       -   
       -  required 
       - 
     

  



.. _ChartDefinitionGaugeTickMark:

ChartDefinitionGaugeTickMark
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - active 
       - boolean  
       -  required 
       - 
     
     * - size 
       - integer  
       -  required 
       - 
     
     * - color 
       - string  
       -  required 
       - 
     

  



.. _ChartDefinitionScatterPlot:

ChartDefinitionScatterPlot
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - styles 
       -   
       -  required 
       - 
     
     * - applyJittering 
       - boolean  
       -  required 
       - 
     

  



.. _ChartDefinitionScatterStyles:

ChartDefinitionScatterStyles
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - color 
       -   
       -  required 
       - 
     
     * - size 
       -   
       -  required 
       - 
     
     * - shape 
       -   
       -  required 
       - 
     
     * - markerStyle 
       -   
       -  required 
       - 
     

  



.. _ChartDefinitionScatterColor:

ChartDefinitionScatterColor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - mode 
       -   
       -  required 
       - 
     
     * - colorVariable 
       - string  
       -  required 
       - 
     
     * - color 
       - string  
       -  required 
       - 
     

  



.. _MarkerColoringMode:

MarkerColoringMode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionScatterSize:

ChartDefinitionScatterSize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - sizeVariable 
       - string  
       -  required 
       - 
     
     * - minimum 
       - integer  
       -  required 
       - 
     
     * - maximum 
       - integer  
       -  required 
       - 
     

  



.. _ChartDefinitionScatterShape:

ChartDefinitionScatterShape
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - shapeVariable 
       - string  
       -  required 
       - 
     
     * - minimum 
       - integer  
       -  required 
       - 
     
     * - maximum 
       - integer  
       -  required 
       - 
     

  



.. _ScatterMarkerStyle:

ScatterMarkerStyle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Scatterplot chart marker style 

  



.. _ChartDefinitionSampleSizeThreshold:

ChartDefinitionSampleSizeThreshold
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - enabled 
       - boolean  
       -  required 
       - 
     
     * - calculation 
       -   
       -  required 
       - 
     
     * - threshold 
       - number  
       -  required 
       - 
     
     * - showWarningAs 
       -   
       -  required 
       - 
     
     * - showCurrentSampleSize 
       - boolean  
       -  required 
       - 
     
     * - showRequiredSampleSize 
       - boolean  
       -  required 
       - 
     
     * - watermarkOpacity 
       - number  
       -  required 
       - 
     
     * - watermarkPosition 
       -   
       -  required 
       - 
     
     * - warningMessage 
       - string  
       - 
       - 
     

  



.. _LowBaseSizeCalculation:

LowBaseSizeCalculation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _LowBaseSizeWarning:

LowBaseSizeWarning
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionEffects:

ChartDefinitionEffects
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - columnGraph 
       -   
       -  required 
       - 
     
     * - opacity 
       - number  
       -  required 
       - 
     
     * - gridAboveGraphs 
       - boolean  
       -  required 
       - 
     
     * - animation 
       -   
       -  required 
       - 
     
     * - settings3D 
       -   
       -  required 
       - 
     
     * - innerRadius 
       - integer  
       -  required 
       - 
     

  



.. _ColumnGraphType:

ColumnGraphType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _DefinitionAnimation:

DefinitionAnimation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - enabled 
       - boolean  
       -  required 
       - 
     
     * - duration 
       - number  
       -  required 
       - 
     

  



.. _DefinitionSettings3D:

DefinitionSettings3D
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - depth 
       - integer  
       -  required 
       - 
     
     * - angle 
       - integer  
       -  required 
       - 
     

  



.. _ChartDefinitionAxis:

ChartDefinitionAxis
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - categoryAxis 
       -   
       -  required 
       - 
     
     * - numericAxis 
       -   
       -  required 
       - 
     

  



.. _ChartDefinitionCategoryAxis:

ChartDefinitionCategoryAxis
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - title 
       -   
       -  required 
       - 
     
     * - intervalBetweenLabels 
       -   
       -  required 
       - 
     
     * - labelAreaSize 
       -   
       -  required 
       - 
     
     * - maximumLabelLength 
       - integer  
       -  required 
       - 
     
     * - wrapLabels 
       - boolean  
       -  required 
       - 
     
     * - labelDistanceFromAxis 
       - number  
       -  required 
       - 
     
     * - labelRotation 
       - number  
       -  required 
       - 
     
     * - showZoom 
       - boolean  
       -  required 
       - 
     
     * - visible 
       - boolean  
       - 
       - 
     
     * - axisLabels 
       -   
       - 
       - 
     
     * - labels 
       -   
       - 
       - 
     
     * - majorTickMarkType 
       -   
       - 
       - 
     
     * - minorTickMarkType 
       -   
       - 
       - 
     

  



.. _ChartDefinitionCategoryAxisTitle:

ChartDefinitionCategoryAxisTitle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - font 
       -   
       -  required 
       - 
     
     * - text 
       - string  
       - 
       - 
     

  



.. _DefinitionAutoFixedOption:

DefinitionAutoFixedOption
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - auto 
       - boolean  
       -  required 
       - 
     
     * - fixedValue 
       - number  
       -  required 
       - 
     

  



.. _BaseChartDefinitionAxis:

BaseChartDefinitionAxis
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - visible 
       - boolean  
       -  required 
       - 
     
     * - labels 
       -   
       -  required 
       - 
     
     * - majorTickMarkType 
       -   
       -  required 
       - 
     
     * - minorTickMarkType 
       -   
       -  required 
       - 
     
     * - axisLabels 
       -   
       - 
       - 
     

  



.. _AxisLabelOptions:

AxisLabelOptions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - placement 
       -   
       -  required 
       - 
     
     * - visible 
       - boolean  
       -  required 
       - 
     
     * - font 
       -   
       - 
       - 
     

  



.. _AxisLabelPlacement:

AxisLabelPlacement
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _LabelOptions:

LabelOptions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - visible 
       - boolean  
       -  required 
       - 
     
     * - font 
       -   
       - 
       - 
     

  



.. _Font:

Font
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - style 
       -   
       -  required 
       - 
     
     * - size 
       - number  
       -  required 
       - 
     
     * - isBold 
       - boolean  
       -  required 
       - 
     
     * - isItalic 
       - boolean  
       -  required 
       - 
     
     * - name 
       - string  
       - 
       - 
     

  



.. _ChartDefinitionAxisLabels:

ChartDefinitionAxisLabels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - font 
       -   
       -  required 
       - 
     
     * - placement 
       -   
       -  required 
       - 
     

  



.. _TickMarkType:

TickMarkType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ChartDefinitionNumericAxis:

ChartDefinitionNumericAxis
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - title 
       -   
       -  required 
       - 
     
     * - maximum 
       -   
       -  required 
       - 
     
     * - minimum 
       -   
       -  required 
       - 
     
     * - showZoom 
       - boolean  
       -  required 
       - 
     
     * - visible 
       - boolean  
       - 
       - 
     
     * - axisLabels 
       -   
       - 
       - 
     
     * - labels 
       -   
       - 
       - 
     
     * - majorTickMarkType 
       -   
       - 
       - 
     
     * - minorTickMarkType 
       -   
       - 
       - 
     

  



.. _ChartDefinitionNumericAxisTitle:

ChartDefinitionNumericAxisTitle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - font 
       -   
       -  required 
       - 
     
     * - text 
       - string  
       - 
       - 
     

  



.. _ChartDefinitionColors:

ChartDefinitionColors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - useTemplate 
       - boolean  
       -  required 
       - 
     
     * - predefinedTheme 
       - integer  
       -  required 
       - 
     
     * - colorPalette 
       -   
       -  required 
       - 
     

  



.. _ChartDefinitionColorPalette:

ChartDefinitionColorPalette
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - colors 
       - list  
       - 
       - 
     

  



.. _ChartDefinitionInteractivity:

ChartDefinitionInteractivity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - isFilterInteractive 
       - boolean  
       -  required 
       - Configures visibility of the filter interactivity 
     
     * - isRowVariableInteractive 
       - boolean  
       -  required 
       - Configures visibility of the row interactivity 
     
     * - isCutVariableInteractive 
       - boolean  
       -  required 
       - Configures visibility of the column interactivity 
     
     * - lockChartTitleOnInteractiveChanging 
       - boolean  
       -  required 
       - 
     
     * - display 
       -   
       -  required 
       - Configures the display options for Interactive Controls 
     
     * - filters 
       -   
       -  required 
       - Configures the Interactive Filters Manager. 
     
     * - sorting 
       -   
       -  required 
       - Configures the Interactive Sort Manager. 
     
     * - autoApply 
       - boolean  
       -  required 
       - 
     
     * - rows 
       -   
       - 
       - Configures the Interactive Rows Manager. 
     
     * - columns 
       -   
       - 
       - Configures the Interactive Columns Manager. 
     

  



.. _ChartDefinitionInteractivityDisplay:

ChartDefinitionInteractivityDisplay
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration for the Chart's Interactive Controls 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - theme 
       -   
       -  required 
       - The display theme for interactive controls. 
     
     * - accentColor 
       - string  
       - 
       - The accent color used in interactive controls. Defaults to `#0099ff`. 
     
     * - selectionColor 
       - string  
       - 
       - The selection color used in interactive controls. Defaults to white. 
     

  



.. _PanelTheme:

PanelTheme
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to specify the display themes for interactive controls. 

  



.. _ChartDefinitionVariableSelectorsValue:

ChartDefinitionVariableSelectorsValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - selectedSectionId 
       - string  
       -  required 
       - 
     
     * - variables 
       - list  of  `DefinitionInteractiveVariable`_
       - 
       - Included section variables 
     
     * - label 
       - string  
       - 
       - The label for the interactive control. 
     

  



.. _DefinitionInteractiveVariable:

DefinitionInteractiveVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - variable 
       - string  
       -  required 
       - 
     
     * - interactiveControlType 
       -   
       -  required 
       - 
     
     * - values 
       -   
       -  required 
       - 
     

  



.. _InteractiveControlType:

InteractiveControlType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _DefinitionInteractiveVariableValues:

DefinitionInteractiveVariableValues
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - codes 
       - list  
       -  required 
       - 
     
     * - ranges 
       - list  of  `DefinitionInteractiveRange`_
       -  required 
       - 
     

  



.. _DefinitionInteractiveRange:

DefinitionInteractiveRange
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - min 
       - number  
       -  required 
       - For range values, the minimum lower bound value code of the range. 
     
     * - max 
       - number  
       -  required 
       - For range values, the maximum upper bound value code of the range. 
     
     * - lower 
       - number  
       -  required 
       - For range values, the current lower bound value code of the range. 
     
     * - upper 
       - number  
       -  required 
       - For range values, the current upper bound value code of the range. 
     
     * - lowerInclusive 
       - boolean  
       -  required 
       - For range values, if `true` the lower bound of the range is inclusive and if `false` is exclusive 
     
     * - upperInclusive 
       - boolean  
       -  required 
       - For range values, if `true` the upper bound of the range is inclusive and if `false` is exclusive 
     

  



.. _ChartDefinitionVariableSelectorsValueBase:

ChartDefinitionVariableSelectorsValueBase
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Common options for row, column, and filter crosstab interactive controls. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - label 
       - string  
       - 
       - The label for the interactive control. 
     

  



.. _ChartDefinitionInteractivityFilters:

ChartDefinitionInteractivityFilters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - controlTypeLocked 
       - boolean  
       -  required 
       - If `true`, prevents the viewer from changing the filter's control type. 
     
     * - panelPosition 
       -   
       -  required 
       - The position of the interactive filter panel. Defaults to `top`. 
     
     * - panelExpanded 
       - boolean  
       -  required 
       - If `true`, the filter panel is expanded on load. If `false`, the filter panelis collapsed on load. Defaults to `false` (collapsed). 
     
     * - variablesExpanded 
       - boolean  
       -  required 
       - If `true`, the variables in the filter panel are expanded on load. If `false`,the variables are collapsed on load. Defaults to `false` (collapsed). 
     
     * - variables 
       - list  of  `DefinitionInteractiveVariable`_
       - 
       - Included filter variables 
     
     * - label 
       - string  
       - 
       - The label for the interactive control. 
     

  



.. _PanelPosition:

PanelPosition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of value to specify the position of the interactive filter panel. 

  



.. _ChartDefinitionInteractivitySort:

ChartDefinitionInteractivitySort
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - Indicates the type of sorting applied to the chart. 
     
     * - order 
       -   
       -  required 
       - Indicates the order in which to sort the chart. 
     
     * - seriesIndex 
       - integer  
       -  required 
       - When the SortType is Series, this is the indexof the series used to sort the chart. 
     

  



.. _ChartSortType:

ChartSortType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enumerates the various options for sorting charts within MarketSight. 

  



.. _ChartSortOrder:

ChartSortOrder
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enumerates the possible chart sort orders. 

  



.. _ChartCalculationRequest:

ChartCalculationRequest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - format 
       -   
       -  required 
       - 
     
     * - preferSync 
       - boolean  
       -  required 
       - 
     
     * - templateOptionsOnly 
       - boolean  
       -  required 
       - 
     
     * - includeFootnotes 
       - boolean  
       -  required 
       - 
     
     * - fontScaling 
       - number  
       -  required 
       - 
     
     * - customTemplate 
       - string  
       - 
       - Custom template name 
     

  



.. _ChartCalculationResultFormat:

ChartCalculationResultFormat
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _CollectionOfCrosstab:

CollectionOfCrosstab
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `Crosstab`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _Crosstab:

Crosstab
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Crosstab Metadata 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       -  required 
       - Title of the Crosstab 
     
     * - description 
       - string  
       -  required 
       - Additional notes about the Crosstab 
     
     * - category 
       - string  
       - 
       - The name of the Category to which the Crosstab has been assigned 
     
     * - dataset 
       - string  
       -  read only 
       - The ID of the Dataset from which the Crosstab is calculated 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Crosstab") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _AsyncResult:

AsyncResult
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - job 
       -   
       - 
       - 
     

  



.. _Job:

Job
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Information about execution of a task. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - result 
       -   
       -  required 
       - The result of long job execution. 
     
     * - blobs 
       - list  of  `JobBlob`_
       - 
       - 
     
     * - name 
       - string  
       - 
       - Name of the Job 
     
     * - type 
       - string  
       - 
       - Internal job type name 
     
     * - target 
       - string  
       - 
       - MarketSight ID of the Target Entity 
     
     * - submitted 
       - string  
       - 
       - Timestamp when the Job was submitted to the queue 
     
     * - started 
       - string  
       - 
       - Timestamp when the Job was selected from the queue and processing began. 
     
     * - stopped 
       - string  
       - 
       - Timestamp when execution of the Job ended. 
     
     * - progress 
       - number  
       -  read only 
       - Execution Progress 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Job") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _JobBlob:

JobBlob
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Information about execution of a task. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - Declares the blob content type. 
     
     * - created 
       - string  
       -  required 
       - Timestamp when the blob was created. 
     
     * - job 
       - string  
       - 
       - MarketSight job ID. 
     
     * - mimeType 
       - string  
       - 
       - Value to declare how an object is serialized and encoded. 
     
     * - name 
       - string  
       - 
       - Preferable name. 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("JobBlob") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _BlobType:

BlobType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to define the blob content type. 

  



.. _LongJobResult:

LongJobResult
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to define the result of long job execution. 

  



.. _CrosstabDefinition:

CrosstabDefinition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   The configuration of a given Crosstab 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - rows 
       -   
       -  required 
       - Configuration of Rows within the Crosstab 
     
     * - columns 
       -   
       -  required 
       - Configuration of Columns within the Crosstab 
     
     * - filters 
       -   
       -  required 
       - Configuration of Filters applied to the Crosstab 
     
     * - display 
       -   
       -  required 
       - Configuration of display options applied to the Crosstab 
     
     * - index 
       -   
       -  required 
       - Configuration of Indexing as applied to the Crosstab 
     
     * - statistics 
       -   
       -  required 
       - Configuration of Statistical Significance Testing applied to the Crosstab 
     
     * - interactivity 
       -   
       -  required 
       - Configuration of the Crosstab's interactivity 
     
     * - highlight 
       -   
       -  required 
       - Configuration of the Crosstab's automatic highlighting 
     
     * - isComposite 
       - boolean  
       -  required 
       - Should every row variable be drawn independently 
     
     * - weightVariable 
       - string  
       - 
       - The Weight Variable to use for the Crosstab 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("CrosstabDefinition") 
     

  



.. _CrosstabDefinitionRows:

CrosstabDefinitionRows
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of Rows within the Crosstab 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - includeAllVariables 
       - boolean  
       -  required 
       - If `true`, the Crosstab will include all variables within the Dataset in theCrosstab's rows. If `false`, the Crosstab will have selected variables withinthe `sections` property. 
     
     * - includeMissingValues 
       - boolean  
       -  required 
       - If `true`, custom values for missing data will be displayed in the Crosstab's rows.If `false`, custom values for missing data will *not* be displayed in the Crosstab's rows. 
     
     * - includeEmpty 
       - boolean  
       -  required 
       - If `true`, rows that consist solely of zero values or that are empty will still beincluded in the Crosstab. If `false`, they will *not* be included. 
     
     * - hideExcludedValueRows 
       - boolean  
       -  required 
       - If `true`, Crosstab can be saved with not checked values rowsIf `false` each row should have at least one value checkedImitates Hide or Omit unchecked Values from UI 
     
     * - sections 
       - list  of  `CrosstabDefinitionRowSection`_
       - 
       - Collection of variable sections that are displayed in rows within the Crosstab. 
     

  



.. _CrosstabDefinitionRowSection:

CrosstabDefinitionRowSection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Data for a particular variable that is displayed in a row within a Crosstab. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - id 
       - string  
       -  required 
       - A unique identifier for the Section. 
     
     * - variables 
       - list  of  `DefinitionAnyRowVariable`_
       -  required 
       - Collection of variables included in the Section. 
     
     * - nets 
       - list  of  `CrosstabDefinitionNet`_
       -  required 
       - Configuration of Net value groupings within the Section 
     
     * - calculations 
       -   
       -  required 
       - Collection of calculations to display in this Section.            *When this property is modified via PATCH, all existing calculations are removed.Even in PATCH, you always have to supply the full set of calculations.* 
     
     * - weightVariable 
       - string  
       - 
       - Variable Code for the Weight Variable to use for this Section.If not set, the Crosstab Definition's default weight variable will be used. 
     

  



.. _DefinitionRowVariable:

DefinitionRowVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A dataset variable projection in crosstab rows 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - values 
       - list  of  `OneOfOfDefinitionRegularValueAndDefinitionPrecedentVariable`_
       - 
       - The Definition Values included within this row variable. 
     
     * - kind 
       -   
       - 
       - The type of Crosstab Definition variable ("variable") 
     
     * - code 
       - string  
       - 
       - The Code (natural identifier) of a dataset variable that corresponds to thisdefinition variable. 
     
     * - includeAllValues 
       - boolean  
       - 
       - When true, values property is ignored, all variable values will be added. 
     

  



.. _DefinitionRegularValue:

DefinitionRegularValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A dataset value projection in Crosstab Definition 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - kind 
       -   
       -  required 
       - The type of Crosstab Definition value ("value") 
     
     * - code 
       - number  
       -  required 
       - The Code (natural identifier) of a dataset variable valuethat corresponds to this definition value. 
     
     * - enabled 
       - boolean  
       - 
       - If `true`, will include this definition value in calculations and/or crosstaboutput. If `false`, will *not* include this definition value in calculationsor output. 
     

  



.. _DefinitionValueKind:

DefinitionValueKind
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to represent different types of crosstab definition values. 

  



.. _DefinitionValue:

DefinitionValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A value-like element used in Crosstab Definition. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - enabled 
       - boolean  
       -  required 
       - If `true`, will include this definition value in calculations and/or crosstaboutput. If `false`, will *not* include this definition value in calculationsor output. 
     

  



.. _DefinitionPrecedentVariable:

DefinitionPrecedentVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Represents a Dichotomous MRV value (precedent variable) in a Crosstab Definition 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - code 
       - string  
       -  required 
       - The precedent variable code 
     
     * - kind 
       -   
       -  required 
       - The type of Crosstab Definition value ("precedentVariable") 
     
     * - enabled 
       - boolean  
       - 
       - If `true`, will include this definition value in calculations and/or crosstaboutput. If `false`, will *not* include this definition value in calculationsor output. 
     

  



.. _OneOfOfDefinitionRegularValueAndDefinitionPrecedentVariable:

OneOfOfDefinitionRegularValueAndDefinitionPrecedentVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - DefinitionRegularValue 
       -   
       - 
       - 
     
     * - DefinitionPrecedentVariable 
       -   
       - 
       - 
     

  



.. _DefinitionRegularVariable:

DefinitionRegularVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A dataset variable projection in Crosstab Definition 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - kind 
       -   
       -  required 
       - The type of Crosstab Definition variable ("variable") 
     
     * - code 
       - string  
       -  required 
       - The Code (natural identifier) of a dataset variable that corresponds to thisdefinition variable. 
     
     * - includeAllValues 
       - boolean  
       -  required 
       - When true, values property is ignored, all variable values will be added. 
     

  



.. _DefinitionVariableKind:

DefinitionVariableKind
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to represent different types of Crosstab Definition variables. 

  



.. _DefinitionVariable:

DefinitionVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A variable-like element in a Crosstab Definition. 

  



.. _DefinitionGridVariable:

DefinitionGridVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A grid variable in crosstab rows 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - code 
       - string  
       -  required 
       - Unique ID of this grid within the crosstab. 
     
     * - kind 
       -   
       -  required 
       - The type of Crosstab Definition variable ("grid") 
     
     * - displayName 
       - string  
       - 
       - Display name of Design Grid Set. 
     
     * - values 
       - list  of  `DefinitionGridValue`_
       - 
       - The Definition Values included within this grid variable. 
     

  



.. _DefinitionGridValue:

DefinitionGridValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Grid variable value (value row) in crosstab grid variable 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - kind 
       -   
       -  required 
       - The type of Crosstab Definition value ("gridValue") 
     
     * - code 
       - number  
       -  required 
       - Definition Grid Value Code populated from variable value. 
     
     * - name 
       - string  
       - 
       - Definition Grid Value Name populated from variable value. 
     
     * - enabled 
       - boolean  
       - 
       - If `true`, will include this definition value in calculations and/or crosstaboutput. If `false`, will *not* include this definition value in calculationsor output. 
     

  



.. _DefinitionAnyRowVariable:

DefinitionAnyRowVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - DefinitionRowVariable 
       -   
       - 
       - 
     
     * - DefinitionGridVariable 
       -   
       - 
       - 
     

  



.. _CrosstabDefinitionNet:

CrosstabDefinitionNet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A Net value grouping, e.g. Top, Bottom, Middle, and Net Promoter Score. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - placement 
       -   
       -  required 
       - The placement of automatic net values relative to non-net definition values. 
     
     * - count 
       - integer  
       -  required 
       - For an aggregate Net, indicates the number of non-Net definition values to aggregate. 
     
     * - top 
       - integer  
       -  required 
       - 
     
     * - bottom 
       - integer  
       -  required 
       - 
     

  



.. _AutoNetValueType:

AutoNetValueType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _AutoNetValuePlacement:

AutoNetValuePlacement
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to indicate placement of Auto-Net values relative to other values. 

  



.. _CrosstabCalculations:

CrosstabCalculations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A set of Calculations.            *When this object is modified via PATCH, all existing calculations are removed.**Even in PATCH, you always have to supply the full set of enabled calculations.* 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - sampleSize 
       - boolean  
       -  required 
       - If `true`, calculates Sample Size. If `false`, does not calculate Sample Size.Applies to both Numeric and Multi-Response Variables. 
     
     * - unweightedSampleSize 
       - boolean  
       -  required 
       - If `true`, calculates the Unweighted Sample Size. If `false`, does not calculate Unweighted Sample Size.Applies to Numeric and Multi-Response Variables.Only supported when a Weight Variable is present. 
     
     * - effectiveBase 
       - boolean  
       -  required 
       - If `True`, calculates the Effective Base. If `false`, does not calculate Effective Base.Applies to Numeric and Multi-Response Variables.Only supported when a Weight Variable is present. 
     
     * - validCases 
       - boolean  
       -  required 
       - If `True`, calculates Valid Cases. If `false`, does not calculate Valid Cases.Applies to Multi-Response Variables. 
     
     * - totalMentions 
       - boolean  
       -  required 
       - If `true`, calculates Total Mentions. If `false`, does not calcualte Total Mentions.Applies to Multi-Response Variables. 
     
     * - unweightedTotalMentions 
       - boolean  
       -  required 
       - If `true`, calcualtes Unweighted Total Mentions. If `false`, does not calculate Unweighted Total Mentions.Applies to Multi-Response Variables.Only supported when a Weight Variable is present. 
     
     * - totalUniqueMentions 
       - boolean  
       -  required 
       - If `true`, calculates Total Unique Mentions. If `false`, does not calcualte Total Unique Mentions.Applies to Multi-Response Variables. 
     
     * - count 
       - boolean  
       -  required 
       - If `true`, calculates the Count for a given variable. If `false`, does not calculate the Count.Applies to Numeric Variables. 
     
     * - unweightedCount 
       - boolean  
       -  required 
       - If `true`, calculates the Unweighted Count for a given variable. If `false`, does not calculate the Unweighted Count.Applies to Numeric Variables.Only supported when a Weight Variable is present. 
     
     * - columnPercent 
       - boolean  
       -  required 
       - If `true`, calculates the Column %. If `false`, does not calculate the Column %.Applies to Numeric Variables. 
     
     * - rowPercent 
       - boolean  
       -  required 
       - If `true`, calculates the Row %. If `false`, does not calculate the Row %.Applies to Numeric Variables. 
     
     * - mentions 
       - boolean  
       -  required 
       - If `true`, calculates Mentions. If `false`, does not calculate Mentions.Applies to Multi-Response Variables. 
     
     * - unweightedMentions 
       - boolean  
       -  required 
       - If `true`, calculates Unweighted Mentions. If `false`, does not calculate Unweighted Mentions.Applies to Multi-Response Variables.Only supported when a Weight Variable is present. 
     
     * - percentSampleSize 
       - boolean  
       -  required 
       - If `true`, calculates the % Sample Size (`Mentions / Sample Size`). If `false`, does not calculate the % Sample Size.Applies to Multi-Response Variables. 
     
     * - rowPercentMentions 
       - boolean  
       -  required 
       - If `true`, calculates the Row % Mentions. If `false`, does not calculate the Row % Mentions.Applies to Multi-Response Variables. 
     
     * - percentValidCases 
       - boolean  
       -  required 
       - If `true`, calculates the % Valid Cases (`Mentions / Valid Cases`). If `false`, does not calculate the % Valid Cases.Applies to Multi-Response Variables. 
     
     * - percentTotalMentions 
       - boolean  
       -  required 
       - If `true`, calculates the % Total Mentions (`Mentions / Total Mentions`) calculation.Applies to Multi-Response Variables. 
     
     * - uniqueMentions 
       - boolean  
       -  required 
       - If `true`, calculates Unique Mentions. If `false`, does not calculate Unique Mentions.Applies to Multi-Response Variables. 
     
     * - uniquePercentSampleSize 
       - boolean  
       -  required 
       - If `true`, calculates Unique % Sample Size (`Unique Mentions / Sample Size`). If `false`, does not.Applies to Multi-Response Variables. 
     
     * - uniquePercentValidCases 
       - boolean  
       -  required 
       - If `true`, calculates the Unique % Valid Cases (`Unique Mentions / Valid Cases`). If `false`, does not.Applies to Multi-Response Variables. 
     
     * - mean 
       - boolean  
       -  required 
       - If `true`, calculates the Mean. If `false`, does not calculate the Mean.Applies to Numeric Variables.Not supported when variables are nested in the row section. 
     
     * - median 
       - boolean  
       -  required 
       - If `true`, calculates the Median. If `false`, does not calculate the Median.Applies to Numeric Variables.Not supported when variables are nested in the row section. 
     
     * - standardDeviation 
       - boolean  
       -  required 
       - If `true`, calculates the Standard Deviation. If `false`, does not calculate the Standard Deviation.Applies to Numeric Variables.Not supported when variables are nested in the row section. 
     
     * - standardError 
       - boolean  
       -  required 
       - If `true`, calculates the Standard Error. If `false`, does not calculate the Standard Error.Applies to Numeric Variables.Not supported when variables are nested in the row section. 
     
     * - min 
       - boolean  
       -  required 
       - If `true`, calculates the minimum value. If `false`, does not calculate the minimum value.Applies to Numeric Variables.Not supported when variables are nested in the row section. 
     
     * - max 
       - boolean  
       -  required 
       - If `true`, calculates the maximum value. If `false`, does not calculate the maximum value.Applies to Numeric Variables.Not supported when variables are nested in the row section. 
     
     * - sum 
       - boolean  
       -  required 
       - If `true`, calculates the sum of values. If `false`, does not calculate the sum.Applies to Numeric Variables.Not supported when variables are nested in the row section. 
     

  



.. _CrosstabDefinitionColumns:

CrosstabDefinitionColumns
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of Columns within the Crosstab 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - includeTotals 
       - boolean  
       -  required 
       - If `true`, each column section will have have an overall Total column. 
     
     * - includeMissingValues 
       - boolean  
       -  required 
       - If `true`, custom values for missing data will be displayed in the Crosstab's columns.If `false`, custom values for missing data will *not* be displayed in the Crosstab's columns. 
     
     * - includeEmpty 
       - boolean  
       -  required 
       - If `true`, columns that consist solely of zero values or that are empty will still beincluded in the Crosstab. If `false`, they will *not* be included. 
     
     * - sections 
       - list  of  `CrosstabDefinitionColumnSection`_
       - 
       - Collection of variable sections that are displayed in columns within the Crosstab. 
     

  



.. _CrosstabDefinitionColumnSection:

CrosstabDefinitionColumnSection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Data for a particular variable that is displayed in a column within a Crosstab. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - id 
       - string  
       -  required 
       - A unique identifier for the Section. 
     
     * - variables 
       - list  of  `DefinitionAnyColumnVariable`_
       -  required 
       - Collection of variables included in the Section. 
     
     * - columns 
       - list  of  `DefinitionSectionColumn`_
       - 
       - Collection of columns included in the Section. 
     

  



.. _DefinitionColumnVariable:

DefinitionColumnVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A dataset variable projection in crosstab columns 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - reverseOrderOfValues 
       - boolean  
       -  required 
       - 
     
     * - name 
       - string  
       - 
       - Display text for column variable. Specify null or empty string to reset to default value. 
     
     * - values 
       - list  of  `OneOfOfDefinitionRegularValueWithNameAndDefinitionPrecedentVariableWithNameAndDefinitionRangeValue`_
       - 
       - The Definition Values included within this column variable. 
     
     * - kind 
       -   
       - 
       - The type of Crosstab Definition variable ("variable") 
     
     * - code 
       - string  
       - 
       - The Code (natural identifier) of a dataset variable that corresponds to thisdefinition variable. 
     
     * - includeAllValues 
       - boolean  
       - 
       - When true, values property is ignored, all variable values will be added. 
     

  



.. _DefinitionRegularValueWithName:

DefinitionRegularValueWithName
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A dataset value projection in Crosstab Definition with changeable name 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       - 
       - Display text for this value. Specify null or empty string to reset to default value. 
     
     * - kind 
       -   
       - 
       - The type of Crosstab Definition value ("value") 
     
     * - code 
       - number  
       - 
       - The Code (natural identifier) of a dataset variable valuethat corresponds to this definition value. 
     
     * - enabled 
       - boolean  
       - 
       - If `true`, will include this definition value in calculations and/or crosstaboutput. If `false`, will *not* include this definition value in calculationsor output. 
     

  



.. _DefinitionPrecedentVariableWithName:

DefinitionPrecedentVariableWithName
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Represents a Dichotomous MRV value (precedent variable) in a Crosstab Definition with changeable name 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       - 
       - Display text for this value. Specify null or empty string to reset to default value. 
     
     * - code 
       - string  
       - 
       - The precedent variable code 
     
     * - kind 
       -   
       - 
       - The type of Crosstab Definition value ("precedentVariable") 
     
     * - enabled 
       - boolean  
       - 
       - If `true`, will include this definition value in calculations and/or crosstaboutput. If `false`, will *not* include this definition value in calculationsor output. 
     

  



.. _DefinitionRangeValue:

DefinitionRangeValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Represents a range in Crosstab Definition 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - kind 
       -   
       -  required 
       - The type of Crosstab Definition value ("range") 
     
     * - min 
       - number  
       -  required 
       - For range values, the minimum lower bound value code of the range. 
     
     * - max 
       - number  
       -  required 
       - For range values, the maximum upper bound value code of the range. 
     
     * - lower 
       - number  
       -  required 
       - For range values, the current lower bound value code of the range. 
     
     * - upper 
       - number  
       -  required 
       - For range values, the current upper bound value code of the range. 
     
     * - lowerInclusive 
       - boolean  
       -  required 
       - For range values, if `true` the lower bound of the range is inclusive and if `false` is exclusive 
     
     * - upperInclusive 
       - boolean  
       -  required 
       - For range values, if `true` the upper bound of the range is inclusive and if `false` is exclusive 
     
     * - name 
       - string  
       - 
       - 
     
     * - enabled 
       - boolean  
       - 
       - If `true`, will include this definition value in calculations and/or crosstaboutput. If `false`, will *not* include this definition value in calculationsor output. 
     

  



.. _OneOfOfDefinitionRegularValueWithNameAndDefinitionPrecedentVariableWithNameAndDefinitionRangeValue:

OneOfOfDefinitionRegularValueWithNameAndDefinitionPrecedentVariableWithNameAndDefinitionRangeValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - DefinitionRegularValueWithName 
       -   
       - 
       - 
     
     * - DefinitionPrecedentVariableWithName 
       -   
       - 
       - 
     
     * - DefinitionRangeValue 
       -   
       - 
       - 
     

  



.. _DefinitionGridColumns:

DefinitionGridColumns
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Grid variable columns in crosstab columns 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - kind 
       -   
       -  required 
       - The type of Crosstab Definition variable ("gridColumns") 
     
     * - name 
       - string  
       - 
       - Display text for grid columns 
     
     * - values 
       - list  of  `DefinitionGridColumn`_
       - 
       - The grid column values included within this grid columns variable. 
     

  



.. _DefinitionGridColumn:

DefinitionGridColumn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Grid variable column (value column) in crosstab grid columns 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - kind 
       -   
       -  required 
       - The type of Crosstab Definition value ("gridColumn") 
     
     * - code 
       - number  
       -  required 
       - Definition Grid Value Code populated from variable value. 
     
     * - name 
       - string  
       - 
       - Definition Grid Value Name populated from variable value. 
     
     * - enabled 
       - boolean  
       - 
       - If `true`, will include this definition value in calculations and/or crosstaboutput. If `false`, will *not* include this definition value in calculationsor output. 
     

  



.. _DefinitionAnyColumnVariable:

DefinitionAnyColumnVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - DefinitionColumnVariable 
       -   
       - 
       - 
     
     * - DefinitionGridColumns 
       -   
       - 
       - 
     

  



.. _DefinitionSectionColumn:

DefinitionSectionColumn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - id 
       - string  
       -  required 
       - A unique identifier for the Column. 
     
     * - columnHeaderPath 
       - list  of  `DefinitionColumnHeaderCell`_
       - 
       - The Definition Values included within this column variable. 
     

  



.. _DefinitionColumnHeaderCell:

DefinitionColumnHeaderCell
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - valueCode 
       - number  
       -  required 
       - Value Code 
     
     * - variableCode 
       - string  
       - 
       - Variable Code 
     

  



.. _CrosstabDefinitionFilters:

CrosstabDefinitionFilters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of Filters applied to a Crosstab 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - variables 
       - list  of  `DefinitionFilterVariable`_
       -  required 
       - Variables to be used as filters 
     

  



.. _DefinitionFilterVariable:

DefinitionFilterVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A dataset variable projection in crosstab filters 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - interactiveControlType 
       -   
       -  required 
       - 
     
     * - values 
       - list  of  `OneOfOfDefinitionRegularValueAndDefinitionPrecedentVariableAndDefinitionRangeValue`_
       - 
       - The Definition Values included within this filter variable. 
     
     * - kind 
       -   
       - 
       - The type of Crosstab Definition variable ("variable") 
     
     * - code 
       - string  
       - 
       - The Code (natural identifier) of a dataset variable that corresponds to thisdefinition variable. 
     
     * - includeAllValues 
       - boolean  
       - 
       - When true, values property is ignored, all variable values will be added. 
     

  



.. _OneOfOfDefinitionRegularValueAndDefinitionPrecedentVariableAndDefinitionRangeValue:

OneOfOfDefinitionRegularValueAndDefinitionPrecedentVariableAndDefinitionRangeValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - DefinitionRegularValue 
       -   
       - 
       - 
     
     * - DefinitionPrecedentVariable 
       -   
       - 
       - 
     
     * - DefinitionRangeValue 
       -   
       - 
       - 
     

  



.. _CrosstabDefinitionDisplay:

CrosstabDefinitionDisplay
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of display options applied to the Crosstab 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - combineCalculations 
       - boolean  
       -  required 
       - If `true`, combines similar Numeric and MRV calculations 
     
     * - showCalculationNames 
       - boolean  
       -  required 
       - If `true`, displays calculation names in crosstab result headers 
     
     * - showVariableDescriptions 
       - boolean  
       -  required 
       - If `true`, displays variable descriptions in crosstab result headers.Only supported when variable names are displayed in rows. 
     
     * - showVariableNames 
       -   
       -  required 
       - Where to display variable names in crosstab results. 
     
     * - showTitleOnDashboards 
       - boolean  
       -  required 
       - If `true`, displays the crosstab's title on Dashboards. 
     
     * - formats 
       -   
       -  required 
       - Configuration of number formats 
     
     * - showCalculationsIn 
       -   
       -  required 
       - 
     
     * - showRowValuesIn 
       -   
       -  required 
       - 
     
     * - freezeColumnHeader 
       - boolean  
       -  required 
       - 
     

  



.. _VariableNameLayout:

VariableNameLayout
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to describe how variable names are displayed in crosstabs. 

  



.. _CrosstabDefinitionFormats:

CrosstabDefinitionFormats
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of number formats 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - percent 
       - integer  
       -  required 
       - The number of fractional digits to display for percentages. The default is 0. 
     
     * - percentSign 
       - boolean  
       -  required 
       - If `true`, displays the percent sign (%) for percentage values. 
     
     * - count 
       - integer  
       -  required 
       - The number of fractional digits to display for counts and sample sizes. The default is 0. 
     
     * - other 
       - integer  
       -  required 
       - The number of fractional digits to display for descriptive statistics. The default is 2. 
     

  



.. _CalculationsPosition:

CalculationsPosition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _RowValuesPosition:

RowValuesPosition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _CrosstabDefinitionIndex:

CrosstabDefinitionIndex
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of Indexing as applied to the Crosstab 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - column 
       - boolean  
       -  required 
       - If `true`, Index will be shown on Column %. 
     
     * - row 
       - boolean  
       -  required 
       - If `true`, Index will be shown on Row %. 
     
     * - value 
       - number  
       -  required 
       - When the index variable is set, the Code of the index variable value. 
     
     * - applyFilter 
       - boolean  
       -  required 
       - If `true`, applies the crosstab filter to the index section in columns whenthe index variable is set. 
     
     * - variable 
       - string  
       - 
       - When set, indicates the Code of a variable to be used as index instead of the Total column.This creates a new variable section in columns. 
     

  



.. _CrosstabDefinitionStatistics:

CrosstabDefinitionStatistics
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of Statistical Significance Testing applied to the Crosstab 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - confidenceLevel1 
       - number  
       -  required 
       - The confidence level to use for statistical tests. Defaults to 0.95. 
     
     * - showColors 
       - boolean  
       -  required 
       - If `true`, significant results will be indicated using colors. 
     
     * - showInsignificant 
       - boolean  
       -  required 
       - If `true`, insignificant results will be shown. 
     
     * - showTooltips 
       - boolean  
       -  required 
       - If `true`, significance tooltips will be shown. 
     
     * - showTestDetails 
       -   
       -  required 
       - Configuration of which test results to show in significance tooltips. 
     
     * - showLetters 
       - boolean  
       -  required 
       - If `true`, uses column letters to indicate Pairwise test results. 
     
     * - test 
       -   
       -  required 
       - The significance test mode to use. Defaults to Pairwise. 
     
     * - sampleBase 
       -   
       -  required 
       - Specifies sample base for statistical calculations when a weight variable is used. 
     
     * - enableCorrection 
       - boolean  
       -  required 
       - If `true`, corrects for Type I errors in all comparisons (more conservative).If `false`, does not correct for Type I errors (more relaxed). 
     
     * - enableStrictRules 
       - boolean  
       -  required 
       - If `true`, enforce strict data sufficiency rules (more conservative). If `false`,does not enforce strict data sufficiency (more relaxed). 
     
     * - exclusiveColumns 
       - boolean  
       -  required 
       - If `true`, treats each pair of columns as a self-contained independent sample. 
     
     * - enabled 
       - boolean  
       -  required 
       - 
     
     * - enableInnermostColumnsRowPercent 
       - boolean  
       -  required 
       - 
     
     * - enableInnermostColumnsStatistics 
       - boolean  
       -  required 
       - 
     
     * - confidenceLevel2 
       - number  
       - 
       - The second confidence level to use when running Pairwise tests. 
     

  



.. _TestDetailsDisplayMode:

TestDetailsDisplayMode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to specify which test results to show in significance tooltips. 

  



.. _TestMode:

TestMode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to specify available test modes. 

  



.. _StatisticsBase:

StatisticsBase
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to specify sample base for statistical calculations when a weight variable is used. 

  



.. _CrosstabDefinitionInteractivity:

CrosstabDefinitionInteractivity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of the Crosstab's interactivity 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - display 
       -   
       -  required 
       - Configures the display options for Interactive Controls 
     
     * - autoApply 
       - boolean  
       -  required 
       - 
     
     * - rows 
       -   
       - 
       - Configures the Interactive Rows Manager. 
     
     * - columns 
       -   
       - 
       - Configures the Interactive Columns Manager. 
     
     * - filters 
       -   
       - 
       - Configures the Interactive Filters Manager. 
     

  



.. _CrosstabDefinitionInteractivityDisplay:

CrosstabDefinitionInteractivityDisplay
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration for the Crosstab's Interactive Controls 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - theme 
       -   
       -  required 
       - The display theme for interactive controls. 
     
     * - accentColor 
       - string  
       - 
       - The accent color used in interactive controls. Defaults to `#0099ff`. 
     
     * - selectionColor 
       - string  
       - 
       - The selection color used in interactive controls. Defaults to white. 
     

  



.. _CrosstabDefinitionVariableSelectorsValue:

CrosstabDefinitionVariableSelectorsValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - selectedSectionId 
       - string  
       - 
       - Id of selected section 
     
     * - label 
       - string  
       - 
       - The label for the interactive control. 
     

  



.. _CrosstabDefinitionVariableSelectorsValueBase:

CrosstabDefinitionVariableSelectorsValueBase
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Common options for row, column, and filter crosstab interactive controls. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - label 
       - string  
       - 
       - The label for the interactive control. 
     

  



.. _CrosstabDefinitionInteractivityFilters:

CrosstabDefinitionInteractivityFilters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - controlTypeLocked 
       - boolean  
       -  required 
       - If `true`, prevents the viewer from changing the filter's control type. 
     
     * - panelPosition 
       -   
       -  required 
       - The position of the interactive filter panel. Defaults to `top`. 
     
     * - panelExpanded 
       - boolean  
       -  required 
       - If `true`, the filter panel is expanded on load. If `false`, the filter panelis collapsed on load. Defaults to `false` (collapsed). 
     
     * - variablesExpanded 
       - boolean  
       -  required 
       - If `true`, the variables in the filter panel are expanded on load. If `false`,the variables are collapsed on load. Defaults to `false` (collapsed). 
     
     * - label 
       - string  
       - 
       - The label for the interactive control. 
     

  



.. _CrosstabDefinitionHighlight:

CrosstabDefinitionHighlight
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of the Crosstab's automatic highlighting 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - count 
       -   
       -  required 
       - Configures highlighting rules for cells with small Counts. 
     
     * - sampleSize 
       -   
       -  required 
       - Configures highlighting rules for cells with small Sample Sizes. 
     
     * - index 
       -   
       -  required 
       - Configures highlighting for cells with Index value above or below a certain threshold. 
     

  



.. _CrosstabDefinitionHighlightCount:

CrosstabDefinitionHighlightCount
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configures highlighting rules for cells with small Counts. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - low 
       -   
       - 
       - Configures highlighting for cells with Counts below a certain threshold. 
     
     * - middle 
       -   
       - 
       - Configures highlighting for cells with Counts below a certain threshold, but above the `Low` threshold. 
     

  



.. _CrosstabDefinitionHighlightCountValue:

CrosstabDefinitionHighlightCountValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configures highlighting rules for cells with small Counts. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - count 
       - integer  
       -  required 
       - The count threshold. 
     
     * - weighted 
       - boolean  
       -  required 
       - If `true`, uses the weighted count. If `false`, uses the unweighted count. 
     

  



.. _CrosstabDefinitionHighlightSampleSize:

CrosstabDefinitionHighlightSampleSize
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configures highlighting for cells with Sample Sizes below a certain threshold. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - low 
       -   
       - 
       - Configures highlighting for cells with Sample Sizes below a certain threshold. 
     

  



.. _CrosstabDefinitionHighlightSampleSizeValue:

CrosstabDefinitionHighlightSampleSizeValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configures highlighting for cells with Sample Sizes below a certain threshold. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - entireColumn 
       - boolean  
       -  required 
       - If `true`, highlights the entire column within each variable. If `false`,highlights individual cells. Defaults to `false`. 
     
     * - count 
       - integer  
       - 
       - The count threshold. 
     
     * - weighted 
       - boolean  
       - 
       - If `true`, uses the weighted count. If `false`, uses the unweighted count. 
     

  



.. _CrosstabDefinitionHighlightIndex:

CrosstabDefinitionHighlightIndex
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configures highlighting for cells with Index value above or below a certain threshold. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - above 
       - number  
       - 
       - Index value above which to highlight. Example: 1.20 
     
     * - below 
       - number  
       - 
       - Index value below which to highlight. Example: 0.80 
     
     * - ignore 
       - number  
       - 
       - Do not highlight cells with indexed percent below the specified value. Example: 0.02 
     

  



.. _CollectionOfCrosstabDefinitionRowSection:

CollectionOfCrosstabDefinitionRowSection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `CrosstabDefinitionRowSection`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _CrosstabIn:

CrosstabIn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Definition of a Crosstab 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - definition 
       -   
       -  required 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Crosstab") 
     
     * - name 
       - string  
       - 
       - Title of the Crosstab 
     
     * - description 
       - string  
       - 
       - Additional notes about the Crosstab 
     
     * - category 
       - string  
       - 
       - The name of the Category to which the Crosstab has been assigned 
     
     * - dataset 
       - string  
       -  read only 
       - The ID of the Dataset from which the Crosstab is calculated 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _CrosstabCalculationRequest:

CrosstabCalculationRequest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - format 
       -   
       -  required 
       - The format in which to return the Crosstab's result. Defaults to `"json"`. 
     
     * - preferSync 
       - boolean  
       -  required 
       - If `true`, the request will return the calculated data table in the format specified, if possible.If `false`, the request will return immediately with Job Metadata that can be usedto check calculation status and retrieve the result when complete. Defaults to `false`. 
     

  



.. _CrosstabCalculationResultFormat:

CrosstabCalculationResultFormat
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _CollectionOfDataSource:

CollectionOfDataSource
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `DataSource`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _DataSource:

DataSource
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       -  required 
       - 
     
     * - enabled 
       - boolean  
       -  required 
       - 
     
     * - configuration 
       -   
       -  required 
       - 
     
     * - account 
       - string  
       -  read only 
       - 
     
     * - description 
       - string  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("DataSource") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _KinesisConfiguration:

KinesisConfiguration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - username 
       - string  
       -  required 
       - 
     
     * - password 
       - string  
       -  required 
       - 
     
     * - url 
       - string  
       -  required 
       - 
     

  



.. _DataSourceType:

DataSourceType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _ConfirmitConfiguration:

ConfirmitConfiguration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - username 
       - string  
       -  required 
       - 
     
     * - password 
       - string  
       -  required 
       - 
     
     * - url 
       - string  
       -  required 
       - 
     

  



.. _QualtricsConfiguration:

QualtricsConfiguration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - token 
       - string  
       -  required 
       - 
     
     * - url 
       - string  
       -  required 
       - 
     
     * - username 
       - string  
       -  required 
       - 
     

  



.. _CmixConfiguration:

CmixConfiguration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _DecipherConfiguration:

DecipherConfiguration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - token 
       - string  
       -  required 
       - 
     
     * - url 
       - string  
       -  required 
       - 
     

  



.. _DataSourceConfiguration:

DataSourceConfiguration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - KinesisConfiguration 
       -   
       - 
       - 
     
     * - ConfirmitConfiguration 
       -   
       - 
       - 
     
     * - QualtricsConfiguration 
       -   
       - 
       - 
     
     * - CmixConfiguration 
       -   
       - 
       - 
     
     * - DecipherConfiguration 
       -   
       - 
       - 
     

  



.. _CrosstabVariableTemplate:

CrosstabVariableTemplate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - row 
       -   
       - 
       - 
     
     * - column 
       -   
       - 
       - 
     
     * - filter 
       -   
       - 
       - 
     

  



.. _CrosstabVariableTemplateKind:

CrosstabVariableTemplateKind
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _CollectionOfDashboard:

CollectionOfDashboard
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `Dashboard`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _Dashboard:

Dashboard
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - account 
       - string  
       -  read only 
       - 
     
     * - name 
       - string  
       - 
       - 
     
     * - description 
       - string  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Dashboard") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _EntityPermissionsOfKeyFindingReference:

EntityPermissionsOfKeyFindingReference
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - inherit 
       - boolean  
       -  required 
       - 
     
     * - owner 
       - string  
       - 
       - 
     
     * - parent 
       - string  
       -  read only 
       - 
     
     * - type 
       - string  
       -  read only 
       - 
     
     * - instanceType 
       - string  
       -  read only 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("EntityPermissions") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _CollectionOfUserPermission:

CollectionOfUserPermission
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `UserPermission`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _UserPermission:

UserPermission
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - permission 
       -   
       -  required 
       - Permission Configuration for the User 
     
     * - user 
       - string  
       - 
       - MarketSight User ID 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("UserPermission") 
     

  



.. _PermissionValue:

PermissionValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to define the scope of access permission. 

  



.. _CollectionOfGroupPermission:

CollectionOfGroupPermission
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `GroupPermission`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _GroupPermission:

GroupPermission
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - permission 
       -   
       -  required 
       - Permission Configuration for the Group 
     
     * - group 
       - string  
       - 
       - MarketSight Group ID 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("GroupPermission") 
     

  



.. _DashboardRedirect:

DashboardRedirect
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Model describing dashboard redirect options 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - origin 
       - string  
       -  required 
       - ID of the dataset to redirect dashboard content from 
     
     * - destination 
       - string  
       -  required 
       - ID of the dataset to redirect dashboard content to 
     

  



.. _DashboardDefinition:

DashboardDefinition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - activeScripts 
       -   
       -  required 
       - 
     
     * - items 
       - list  of  `DashboardItem`_
       -  required 
       - 
     
     * - design 
       -   
       -  required 
       - 
     
     * - interactivity 
       -   
       -  required 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("DashboardDefinition") 
     

  



.. _DashboardInjectedScripts:

DashboardInjectedScripts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - scripts 
       - list  of  `DashboardInjectedScript`_
       -  required 
       - 
     

  



.. _DashboardInjectedScript:

DashboardInjectedScript
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       -  required 
       - 
     

  



.. _DashboardItem:

DashboardItem
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - tabIndex 
       - integer  
       -  required 
       - 
     
     * - orderInGroup 
       - integer  
       -  required 
       - 
     
     * - content 
       -   
       - 
       - 
     
     * - options 
       -   
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("DashboardItem") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _DashboardCrosstabContent:

DashboardCrosstabContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - crosstabId 
       - string  
       - 
       - 
     
     * - layoutInfo 
       -   
       - 
       - 
     

  



.. _DashboardItemType:

DashboardItemType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _TableLayoutInfo:

TableLayoutInfo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - columnsWidths 
       - list  
       - 
       - 
     
     * - rowsHeights 
       - list  
       - 
       - 
     

  



.. _DashboardItemContent:

DashboardItemContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _DashboardChartContent:

DashboardChartContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - chartId 
       - string  
       - 
       - 
     
     * - legendPosition 
       -   
       - 
       - 
     

  



.. _DashboardDataViewContent:

DashboardDataViewContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - dataViewId 
       - string  
       - 
       - 
     
     * - layoutInfo 
       -   
       - 
       - 
     

  



.. _DashboardRichTextContext:

DashboardRichTextContext
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - xmlMarkup 
       - string  
       - 
       - 
     

  



.. _DashboardImageContext:

DashboardImageContext
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - image 
       - string  
       - 
       - 
     
     * - name 
       - string  
       - 
       - 
     

  



.. _DashboardPlaceholderContent:

DashboardPlaceholderContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _DashboardGroupContent:

DashboardGroupContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - elements 
       - list  of  `DashboardItemContent`_
       - 
       - 
     

  



.. _DashboardUrlContent:

DashboardUrlContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - showInSeparateTab 
       - boolean  
       -  required 
       - 
     
     * - type 
       -   
       -  required 
       - 
     
     * - url 
       - string  
       - 
       - 
     
     * - description 
       - string  
       - 
       - 
     

  



.. _DashboardVideoContent:

DashboardVideoContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - video 
       - string  
       - 
       - 
     

  



.. _DashboardLogoContent:

DashboardLogoContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _DashboardRectangleContent:

DashboardRectangleContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _DashboardExternalContent:

DashboardExternalContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     

  



.. _OneOfOfDashboardCrosstabContentAndDashboardChartContentAndDashboardDataViewContentAndDashboardRichTextContextAndDashboardImageContextAndDashboardPlaceholderContentAndDashboardGroupContentAndDashboardUrlContentAndDashboardVideoContentAndDashboardLogoContentAndDashboardRectangleContentAndDashboardExternalContent:

OneOfOfDashboardCrosstabContentAndDashboardChartContentAndDashboardDataViewContentAndDashboardRichTextContextAndDashboardImageContextAndDashboardPlaceholderContentAndDashboardGroupContentAndDashboardUrlContentAndDashboardVideoContentAndDashboardLogoContentAndDashboardRectangleContentAndDashboardExternalContent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - DashboardCrosstabContent 
       -   
       - 
       - 
     
     * - DashboardChartContent 
       -   
       - 
       - 
     
     * - DashboardDataViewContent 
       -   
       - 
       - 
     
     * - DashboardRichTextContext 
       -   
       - 
       - 
     
     * - DashboardImageContext 
       -   
       - 
       - 
     
     * - DashboardPlaceholderContent 
       -   
       - 
       - 
     
     * - DashboardGroupContent 
       -   
       - 
       - 
     
     * - DashboardUrlContent 
       -   
       - 
       - 
     
     * - DashboardVideoContent 
       -   
       - 
       - 
     
     * - DashboardLogoContent 
       -   
       - 
       - 
     
     * - DashboardRectangleContent 
       -   
       - 
       - 
     
     * - DashboardExternalContent 
       -   
       - 
       - 
     

  



.. _DashboardItemOptionsDefinition:

DashboardItemOptionsDefinition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - autoSize 
       - boolean  
       -  required 
       - 
     
     * - backgroundColor 
       - string  
       -  required 
       - 
     
     * - borderColor 
       - string  
       -  required 
       - 
     
     * - borderThickness 
       - number  
       -  required 
       - 
     
     * - hideScrollbars 
       - boolean  
       -  required 
       - 
     
     * - lockAspectRatio 
       - boolean  
       -  required 
       - 
     
     * - margin 
       - number  
       -  required 
       - 
     
     * - position 
       -   
       -  required 
       - 
     
     * - tag 
       - string  
       - 
       - 
     

  



.. _DashboardDesign:

DashboardDesign
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - width 
       - number  
       -  required 
       - 
     
     * - height 
       - number  
       -  required 
       - 
     
     * - backgroundColor 
       - string  
       -  required 
       - 
     
     * - borderColor 
       - string  
       -  required 
       - 
     
     * - showZoomInButtons 
       - boolean  
       -  required 
       - 
     
     * - showExportButtons 
       - boolean  
       -  required 
       - 
     
     * - borderThickness 
       - number  
       -  required 
       - 
     
     * - tabsBackgroundColor 
       - string  
       -  required 
       - 
     
     * - tabsHoverBackgroundColor 
       - string  
       -  required 
       - 
     
     * - tabsFontColor 
       - string  
       -  required 
       - 
     
     * - tabsBorderColor 
       - string  
       -  required 
       - 
     
     * - tabsSelectedBackgroundColor 
       - string  
       -  required 
       - 
     
     * - tabsBorderThickness 
       - number  
       -  required 
       - 
     
     * - tabsPosition 
       -   
       -  required 
       - 
     
     * - tabsAreaWidth 
       - number  
       -  required 
       - 
     
     * - tabsEnabled 
       - boolean  
       -  required 
       - 
     
     * - autoApply 
       - boolean  
       -  required 
       - 
     
     * - variableDisplayMode 
       -   
       -  required 
       - 
     
     * - logosEdited 
       - boolean  
       -  required 
       - 
     
     * - allowDimmingOnDragOver 
       - boolean  
       -  required 
       - 
     
     * - allowDimmingOnHover 
       - boolean  
       -  required 
       - 
     
     * - filtersMigrated 
       - boolean  
       -  required 
       - Identify whether dashboard has been created before Filters persistence 
     
     * - tabsLazyLoadingEnabled 
       - boolean  
       -  required 
       - 
     
     * - position 
       -   
       - 
       - 
     
     * - tabs 
       - list  of  `DashboardTab`_
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("DashboardDesign") 
     

  



.. _TabsPosition:

TabsPosition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _VariableDisplayMode:

VariableDisplayMode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _DashboardTab:

DashboardTab
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - index 
       - integer  
       -  required 
       - 
     
     * - isHidden 
       - boolean  
       -  required 
       - 
     
     * - name 
       - string  
       - 
       - 
     

  



.. _DashboardInteractivity:

DashboardInteractivity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - filters 
       -   
       -  required 
       - 
     
     * - design 
       -   
       -  required 
       - 
     

  



.. _DashboardDefinitionInteractivityFilters:

DashboardDefinitionInteractivityFilters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - controlTypeLocked 
       - boolean  
       -  required 
       - If `true`, prevents the viewer from changing the filter's control type. 
     
     * - panelPosition 
       -   
       -  required 
       - The position of the interactive filter panel. Defaults to `top`. 
     
     * - panelExpanded 
       - boolean  
       -  required 
       - If `true`, the filter panel is expanded on load. If `false`, the filter panelis collapsed on load. Defaults to `false` (collapsed). 
     
     * - variablesExpanded 
       - boolean  
       -  required 
       - If `true`, the variables in the filter panel are expanded on load. If `false`,the variables are collapsed on load. Defaults to `false` (collapsed). 
     
     * - variables 
       - list  of  `DashboardInteractiveVariableDefinition`_
       - 
       - Included filter variables 
     

  



.. _DashboardInteractiveVariableDefinition:

DashboardInteractiveVariableDefinition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - datasetId 
       - integer  
       -  required 
       - 
     
     * - variable 
       - string  
       - 
       - 
     
     * - interactiveControlType 
       -   
       - 
       - 
     
     * - values 
       -   
       - 
       - 
     

  



.. _DashboardInteractivityDesign:

DashboardInteractivityDesign
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - currentSelectionControl 
       - string  
       -  required 
       - 
     
     * - isFilterControlsUnlocked 
       - boolean  
       -  required 
       - 
     
     * - panelAccentColor 
       - string  
       -  required 
       - 
     
     * - panelTheme 
       -   
       -  required 
       - 
     
     * - showDashboardLevelFilterControls 
       - boolean  
       -  required 
       - 
     
     * - showFilterControls 
       - boolean  
       -  required 
       - 
     
     * - showRowVariableControls 
       - boolean  
       -  required 
       - 
     
     * - showColumnVariableControls 
       - boolean  
       -  required 
       - 
     
     * - controlsExpanded 
       - boolean  
       -  required 
       - 
     
     * - panelPosition 
       -   
       -  required 
       - 
     
     * - label 
       - string  
       - 
       - 
     

  



.. _DashboardIn:

DashboardIn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - definition 
       -   
       -  required 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Dashboard") 
     
     * - account 
       - string  
       -  read only 
       - 
     
     * - name 
       - string  
       - 
       - 
     
     * - description 
       - string  
       - 
       - 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _CollectionOfDataView:

CollectionOfDataView
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `DataView`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _DataView:

DataView
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Data veiw model definition 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       -  required 
       - 
     
     * - dataset 
       - string  
       -  read only 
       - 
     
     * - description 
       - string  
       - 
       - 
     
     * - category 
       - string  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("DataView") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _DataViewIn:

DataViewIn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - definition 
       -   
       -  required 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("DataView") 
     
     * - dataset 
       - string  
       -  read only 
       - 
     
     * - name 
       - string  
       - 
       - 
     
     * - description 
       - string  
       - 
       - 
     
     * - category 
       - string  
       - 
       - 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _DataViewDefinition:

DataViewDefinition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - options 
       -   
       - 
       - 
     
     * - columns 
       -   
       - 
       - 
     
     * - filters 
       -   
       - 
       - 
     

  



.. _DataViewOptions:

DataViewOptions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - autoApply 
       - boolean  
       -  required 
       - 
     
     * - numericFormat 
       - string  
       - 
       - 
     
     * - interactivityOptions 
       -   
       - 
       - 
     

  



.. _DataViewDefinitionInteractivity:

DataViewDefinitionInteractivity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - filterPanelPosition 
       -   
       -  required 
       - The position of the interactive filter panel. Defaults to `left`. 
     
     * - filterPanelVisible 
       - boolean  
       -  required 
       - If `true`, the filter panel is visible on load. If `false`, the filter panelis not visible on load. Defaults to `true` 
     
     * - filterPanelExpanded 
       - boolean  
       -  required 
       - If `true`, the filter panel is expanded on load. If `false`, the filter panelis collapsed on load. Defaults to `true` (expanded). 
     
     * - filterVariablesExpanded 
       - boolean  
       -  required 
       - If `true`, the variables in the filter panel are expanded on load. If `false`,the variables are collapsed on load. Defaults to `true` (expanded). 
     
     * - allowViewerToChangeControlType 
       - boolean  
       -  required 
       - 
     
     * - theme 
       -   
       -  required 
       - The display theme for interactive controls. 
     
     * - accentColor 
       - string  
       -  required 
       - The accent color used in interactive controls. Defaults to `#0099ff`. 
     
     * - selectionColor 
       - string  
       -  required 
       - The selection color used in interactive controls. Defaults to white. 
     
     * - filterPanelLabel 
       - string  
       - 
       - 
     

  



.. _DataViewDefinitionColumns:

DataViewDefinitionColumns
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - hideEmptyResponses 
       - boolean  
       -  required 
       - 
     
     * - variables 
       - list  of  `DataViewVariable`_
       - 
       - 
     

  



.. _DataViewVariable:

DataViewVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - code 
       - string  
       - 
       - 
     
     * - displayName 
       - string  
       - 
       - 
     

  



.. _DataViewDefinitionFilters:

DataViewDefinitionFilters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - variables 
       - list  of  `DataViewFilterVariable`_
       - 
       - 
     

  



.. _DataViewFilterVariable:

DataViewFilterVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - includeMissingResponses 
       - boolean  
       -  required 
       - 
     
     * - filteredValues 
       - list  of  `DataViewFilterValue`_
       - 
       - 
     
     * - code 
       - string  
       - 
       - 
     
     * - displayName 
       - string  
       - 
       - 
     

  



.. _DataViewFilterValue:

DataViewFilterValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - code 
       - number  
       -  required 
       - 
     
     * - displayName 
       - string  
       - 
       - 
     

  



.. _AsyncResult2:

AsyncResult2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _DataViewCalculationRequest:

DataViewCalculationRequest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - format 
       -   
       -  required 
       - 
     
     * - preferSync 
       - boolean  
       -  required 
       - 
     
     * - exportCodes 
       - boolean  
       -  required 
       - 
     

  



.. _DataViewCalculationResultFormat:

DataViewCalculationResultFormat
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _EntityPermissionsOfFileReference:

EntityPermissionsOfFileReference
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - inherit 
       - boolean  
       -  required 
       - 
     
     * - owner 
       - string  
       - 
       - 
     
     * - parent 
       - string  
       -  read only 
       - 
     
     * - type 
       - string  
       -  read only 
       - 
     
     * - instanceType 
       - string  
       -  read only 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("EntityPermissions") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _InjectedScript:

InjectedScript
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Injected Script Metadata 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - javaScriptCode 
       - string  
       -  required 
       - Injected Script JavaScript Code 
     
     * - privileged 
       - boolean  
       -  required 
       - Privileged scripts run unsandboxed. Requires signatureValid to be true. 
     
     * - account 
       - string  
       -  read only 
       - Account of the Injected Script 
     
     * - name 
       - string  
       - 
       - Title of the Injected Script 
     
     * - typeScriptCode 
       - string  
       - 
       - Injected Script TypeScript Code 
     
     * - signatureValid 
       - boolean  
       -  read only 
       - Indicates whether the signature for this script is valid. This property is not returned by default. 
     
     * - signature 
       -   
       - 
       - Contains cryptographic signature properties. This property is not returned by default. 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("InjectedScript") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _InjectedScriptSignature:

InjectedScriptSignature
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - publicKeyName 
       - string  
       -  required 
       - The name of the public key used to sign this script. This property is not returned by default. 
     
     * - blob 
       - string  
       -  required 
       - The signature blob for this script. This property is not returned by default. 
     
     * - time 
       - string  
       -  read only 
       - The time when the signature was uploaded to the server. This property is not returned by default. 
     
     * - signee 
       - string  
       -  read only 
       - The user that produced this signature. This property is not returned by default. 
     

  



.. _CollectionOfInjectedScript:

CollectionOfInjectedScript
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `InjectedScript`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _JwkSet:

JwkSet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - keys 
       - list  of  `Jwk`_
       - 
       - 
     

  



.. _Jwk:

Jwk
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - kty 
       - string  
       -  required 
       - Key Type 
     
     * - crv 
       - string  
       -  required 
       - Curve Name 
     
     * - kid 
       - string  
       -  required 
       - Key ID 
     
     * - x 
       - string  
       -  required 
       - Base64URL-encoded Qx 
     
     * - y 
       - string  
       -  required 
       - Base64URL-encoded Qy 
     

  



.. _KeyFindingWithItem:

KeyFindingWithItem
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - crosstab 
       -   
       - 
       - 
     
     * - chart 
       -   
       - 
       - 
     
     * - file 
       -   
       - 
       - 
     
     * - dataView 
       -   
       - 
       - 
     
     * - dashBoard 
       -   
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("KeyFinding") 
     
     * - account 
       - string  
       -  read only 
       - ID of the Account that owns the Key Finding 
     
     * - item 
       - string  
       -  read only 
       - ID of the item associated with the Key Finding. 
     
     * - type 
       -   
       -  read only 
       - Type of this key finding.Value of this property is the name of the property that contains the full underlying item model. 
     
     * - name 
       - string  
       - 
       - Name of the Key Finding 
     
     * - description 
       - string  
       - 
       - Description of the Key Finding 
     
     * - parent 
       - string  
       -  read only 
       - ID of the parent Key Finding folder 
     
     * - folderPath 
       - list  
       - 
       - Full path to the folder where the Key Finding resides 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _File:

File
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - folderPath 
       - list  
       -  required 
       - 
     
     * - account 
       - string  
       -  read only 
       - 
     
     * - name 
       - string  
       - 
       - Related file name. Must be unique within the target folder. 
     
     * - description 
       - string  
       - 
       - A human-readable description of the related file. 
     
     * - mimeType 
       - string  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("File") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _KeyFinding:

KeyFinding
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       -  required 
       - Name of the Key Finding 
     
     * - account 
       - string  
       -  read only 
       - ID of the Account that owns the Key Finding 
     
     * - item 
       - string  
       -  read only 
       - ID of the item associated with the Key Finding. 
     
     * - type 
       -   
       -  read only 
       - Type of this key finding.Value of this property is the name of the property that contains the full underlying item model. 
     
     * - description 
       - string  
       - 
       - Description of the Key Finding 
     
     * - parent 
       - string  
       -  read only 
       - ID of the parent Key Finding folder 
     
     * - folderPath 
       - list  
       - 
       - Full path to the folder where the Key Finding resides 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("KeyFinding") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _KeyFindingType:

KeyFindingType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _CollectionOfKeyFinding:

CollectionOfKeyFinding
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `KeyFinding`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _EntityPermissionsOfDatasetReference:

EntityPermissionsOfDatasetReference
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - inherit 
       - boolean  
       -  required 
       - 
     
     * - owner 
       - string  
       - 
       - 
     
     * - parent 
       - string  
       -  read only 
       - 
     
     * - type 
       - string  
       -  read only 
       - 
     
     * - instanceType 
       - string  
       -  read only 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("EntityPermissions") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _CollectionOfJobBlob:

CollectionOfJobBlob
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `JobBlob`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _CollectionOfDataset:

CollectionOfDataset
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `Dataset`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _Dataset:

Dataset
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Dataset Metadata 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       - 
       - Title of the Dataset 
     
     * - description 
       - string  
       - 
       - Additonal notes about the Dataset 
     
     * - folderPath 
       - list  
       - 
       - Full path to the folder where the Dataset resides 
     
     * - size 
       - integer  
       -  read only 
       - Lower bound size of Dataset respondent data in bytes. 
     
     * - status 
       -   
       -  read only 
       - Dataset processing status. Datasets in `ready` state are available for use. 
     
     * - external 
       - boolean  
       -  read only 
       - If `true`, the Dataset is linked to an external source. Otherwise is from anuploaded file. 
     
     * - account 
       - string  
       -  read only 
       - ID of the Account that owns the Dataset 
     
     * - numVariables 
       - integer  
       -  read only 
       - 
     
     * - numRespondents 
       - integer  
       -  read only 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Dataset") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _NDatasetStatus:

NDatasetStatus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to define dataset processing status. Datasets in `ready` state are available for use. 

  



.. _AsyncDatasetResult:

AsyncDatasetResult
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Information regarding asynchronous dataset operations. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - dataset 
       -   
       - 
       - Dataset information. 
     
     * - job 
       -   
       - 
       - 
     

  



.. _DatasetIn:

DatasetIn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Properties when importing a dataset. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - account 
       - string  
       -  read only 
       - ID of the Account importing the Dataset 
     
     * - external 
       -   
       - 
       - Information for external datasets. 
     
     * - folderPath 
       - list  
       - 
       - 3bf930cd-0255-4ac3-9e1f-a8e800fe674d. 
     
     * - name 
       - string  
       - 
       - Dataset name. Must be unique within the target folder. 
     
     * - description 
       - string  
       - 
       - A human-readable description of the dataset. 
     

  



.. _ExternalDatasetIn:

ExternalDatasetIn
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Dataset Metadata within the Partner's platform 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - externalId 
       - string  
       -  required 
       - Unique identifier for the Dataset in the Partner's platform 
     
     * - partner 
       - string  
       -  read only 
       - Partner ID for the Dataset's originator 
     
     * - dataSource 
       - string  
       -  read only 
       - ID for External Data Source 
     
     * - config 
       -   
       - 
       - Vendor-specific external dataset information. 
     
     * - tokens 
       -   
       - 
       - Tokens used to synchronize datasets with the provider's platforms 
     

  



.. _CriticalMixDatasetConfig:

CriticalMixDatasetConfig
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - respondentTypeFilter 
       -   
       -  required 
       - Declares the type of respondents to fetch. 
     
     * - retrievalMode 
       -   
       -  required 
       - Declares the context for data retrieval for a specific vendor implementation. 
     
     * - localeId 
       - integer  
       - 
       - 
     

  



.. _CriticalMixRespondentTypeFilter:

CriticalMixRespondentTypeFilter
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _CriticalMixRetrievalMode:

CriticalMixRetrievalMode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _DatasetCopy:

DatasetCopy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Model for copying a dataset. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - destinationAccount 
       - string  
       -  read only 
       - ID of the Receiving Account 
     
     * - folderPath 
       - list  
       - 
       - Folder path of the target destination. 
     

  



.. _Respondents:

Respondents
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Dataset respondents changes model 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - changes 
       - list  of  `OneOfOfUpdateRespondentAndDeleteRespondent`_
       - 
       - Array of dataset respondents changes 
     

  



.. _UpdateRespondent:

UpdateRespondent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - respondent 
       -   
       -  required 
       - Respondent selector by which respondent will be selected for updates 
     
     * - answers 
       - object  
       -  required 
       - Pairs of  and  which will be used for update respondent data 
     

  



.. _ChangeRespondentsType:

ChangeRespondentsType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _OneRespondentSelector:

OneRespondentSelector
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Respondent selector by respondents answers data for specified variables 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - answers 
       - object  
       - 
       - Pairs of Variable reference and respondent answer, by which respondent will be selected for changes 
     

  



.. _RespondentSelectorType:

RespondentSelectorType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _RespondentAnswer:

RespondentAnswer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _RespondentSelector:

RespondentSelector
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Respondent selector by which specific respondent will be selected for changes 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - OneRespondentSelector 
       -   
       - 
       - 
     

  



.. _DeleteRespondent:

DeleteRespondent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - respondent 
       -   
       -  required 
       - Respondent selector by which respondent will be selected for delete 
     

  



.. _OneOfOfUpdateRespondentAndDeleteRespondent:

OneOfOfUpdateRespondentAndDeleteRespondent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - UpdateRespondent 
       -   
       - 
       - 
     
     * - DeleteRespondent 
       -   
       - 
       - 
     

  



.. _AppendVariables:

AppendVariables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - appendUnmatchedRespondents 
       - boolean  
       -  required 
       - If respondents with missing keys from CodesToJoinOn from the dataset being appended should be added. 
     
     * - codesToJoinOn 
       - list  
       - 
       - Codes of variables to join datasets.Codes should be the same in both datasets.If empty - records will be matched in order of appearance in the datasets. 
     
     * - variablesToRecode 
       - list  of  `RecodeVariable`_
       - 
       - If in original and new dataset exists variables with the same code they should be recoded. 
     
     * - excludedVariables 
       - list  of  `VariableDescriptor`_
       - 
       - Variables from original and new dataset which will be excluded from resulted dataset. 
     
     * - newDatasetName 
       - string  
       - 
       - Name for resulted dataset. 
     
     * - newDatasetDescription 
       - string  
       - 
       - Description for resulted dataset. 
     
     * - appendToOriginalDataset 
       - boolean  
       - 
       - Defines target for append. 
     
     * - wave 
       -   
       - 
       - Wave options. 
     
     * - excludeMissingValues 
       - boolean  
       - 
       - If true user-missing values will be ignored in appended datasetand imported respondent answers will be recorded as system-missing values. 
     

  



.. _AppendType:

AppendType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _RecodeVariable:

RecodeVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - newCode 
       - string  
       -  required 
       - Code which will be applied to variable in case of code duplication in Select Variables process. 
     
     * - variable 
       - string  
       - 
       - 
     
     * - fromOriginalDataset 
       - boolean  
       - 
       - Flag for determine from new or original dataset current variable.If `true` - variable from original (already existing) dataset. 
     

  



.. _VariableDescriptor:

VariableDescriptor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - variable 
       - string  
       -  required 
       - 
     
     * - fromOriginalDataset 
       - boolean  
       -  required 
       - Flag for determine from new or original dataset current variable.If `true` - variable from original (already existing) dataset. 
     

  



.. _AppendBase:

AppendBase
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - newDatasetName 
       - string  
       -  required 
       - Name for resulted dataset. 
     
     * - appendToOriginalDataset 
       - boolean  
       -  required 
       - Defines target for append. 
     
     * - excludeMissingValues 
       - boolean  
       -  required 
       - If true user-missing values will be ignored in appended datasetand imported respondent answers will be recorded as system-missing values. 
     
     * - newDatasetDescription 
       - string  
       - 
       - Description for resulted dataset. 
     
     * - wave 
       -   
       - 
       - Wave options. 
     

  



.. _Wave:

Wave
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - addNewWave 
       - boolean  
       -  required 
       - Flag for determine use wave migration option from choices or not. 
     
     * - waveMigrationOption 
       -   
       -  required 
       - 
     
     * - appendToExistingWaveValue 
       - number  
       -  required 
       - 
     
     * - firstWaveName 
       - string  
       - 
       - 
     
     * - newWaveName 
       - string  
       - 
       - 
     
     * - waveVariableName 
       - string  
       - 
       - 
     

  



.. _WaveMigrationOption:

WaveMigrationOption
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum representing the migration options of the wave variable during append 

  



.. _AppendRespondents:

AppendRespondents
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - useNewLabelsAndCodes 
       - boolean  
       -  required 
       - If `true`, variable labels and codes will be taken from the new dataset. 
     
     * - matchedVariables 
       - list  of  `MatchingResult`_
       - 
       - 
     
     * - newDatasetName 
       - string  
       - 
       - Name for resulted dataset. 
     
     * - newDatasetDescription 
       - string  
       - 
       - Description for resulted dataset. 
     
     * - appendToOriginalDataset 
       - boolean  
       - 
       - Defines target for append. 
     
     * - wave 
       -   
       - 
       - Wave options. 
     
     * - excludeMissingValues 
       - boolean  
       - 
       - If true user-missing values will be ignored in appended datasetand imported respondent answers will be recorded as system-missing values. 
     

  



.. _MatchingResult:

MatchingResult
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - originalVariable 
       - string  
       -  required 
       - 
     
     * - appendedVariable 
       - string  
       -  required 
       - 
     

  



.. _OneOfOfAppendVariablesAndAppendRespondents:

OneOfOfAppendVariablesAndAppendRespondents
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - AppendVariables 
       -   
       - 
       - 
     
     * - AppendRespondents 
       -   
       - 
       - 
     

  



.. _ExternalDatasetLink:

ExternalDatasetLink
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   A connection between a MarketSight Dataset and an external source. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - enableAutoSync 
       - boolean  
       -  required 
       - Enable automatic synchronization 
     
     * - id 
       - string  
       - 
       - 
     
     * - dataset 
       - string  
       - 
       - MarketSight Dataset ID 
     
     * - externalId 
       - string  
       - 
       - Unique identifier of the dataset in the external platform 
     
     * - lastUpdateTime 
       - string  
       -  read only 
       - Timestamp of the most-recent dataset update 
     
     * - dataSource 
       - string  
       - 
       - MarektSight ID of the external data source. 
     
     * - config 
       -   
       - 
       - Vendor-specific external dataset information. 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("ExternalDatasetLink") 
     

  



.. _DatasetSync:

DatasetSync
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of a DataSync Operation 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - update 
       -   
       -  required 
       - Configuration of the Dataset Sync 
     
     * - tokens 
       -   
       - 
       - Authentication tokens to use for the dataset sync 
     

  



.. _DatasetSyncUpdate:

DatasetSyncUpdate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Configuration of external dataset sync operations. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - metadata 
       - boolean  
       -  required 
       - If `true`, update metadata from the remote source. 
     
     * - data 
       -   
       -  required 
       - The method by which respondent records should be updated. 
     

  



.. _DatasetDataUpdateMode:

DatasetDataUpdateMode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of values to specify the scope of respondent data updates. 

  



.. _CollectionOfDatasetMessage:

CollectionOfDatasetMessage
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `DatasetMessage`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _DatasetMessage:

DatasetMessage
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - severity 
       - string  
       -  read only 
       - Severity of the message 
     
     * - message 
       - string  
       -  read only 
       - Message text 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("DatasetMessage") 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _CollectionOfJob:

CollectionOfJob
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `Job`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _CollectionOfJobLog:

CollectionOfJobLog
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `JobLog`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _JobLog:

JobLog
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Optional information about execution of a job. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - details 
       -   
       - 
       - Job Details 
     

  



.. _IJobDetails:

IJobDetails
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _CollectionOfPartnerDatasetAssociation:

CollectionOfPartnerDatasetAssociation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `PartnerDatasetAssociation`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _PartnerDatasetAssociation:

PartnerDatasetAssociation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - user 
       - string  
       -  read only 
       - 
     
     * - dataset 
       - string  
       -  read only 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("PartnerDatasetAssociation") 
     
     * - partner 
       - string  
       -  read only 
       - 
     
     * - externalId 
       - string  
       - 
       - The ID of the entity in the Partner's platform 
     
     * - externalName 
       - string  
       - 
       - The name of the entity in the Partner's platform 
     

  



.. _PartnerAssociation:

PartnerAssociation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - externalId 
       - string  
       -  required 
       - The ID of the entity in the Partner's platform 
     
     * - partner 
       - string  
       -  read only 
       - 
     
     * - externalName 
       - string  
       - 
       - The name of the entity in the Partner's platform 
     

  



.. _PartnerAccountAssociation:

PartnerAccountAssociation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - owned 
       - boolean  
       -  required 
       - If `True`, the Account is owned by the Partner (i.e. the Partner handles billing).If `False`, the Account is owned by MarketSight. 
     
     * - account 
       - string  
       -  read only 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("PartnerAccountAssociation") 
     
     * - partner 
       - string  
       -  read only 
       - 
     
     * - externalId 
       - string  
       - 
       - The ID of the entity in the Partner's platform 
     
     * - externalName 
       - string  
       - 
       - The name of the entity in the Partner's platform 
     

  



.. _CollectionOfPartnerAccountAssociation:

CollectionOfPartnerAccountAssociation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `PartnerAccountAssociation`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _PartnerUserAssociation:

PartnerUserAssociation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - owned 
       - boolean  
       -  required 
       - If `true`, the Partner owns the related User record. If `false`, thePartner does not. 
     
     * - user 
       - string  
       -  read only 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("PartnerUserAssociation") 
     
     * - partner 
       - string  
       -  read only 
       - 
     
     * - externalId 
       - string  
       - 
       - The ID of the entity in the Partner's platform 
     
     * - externalName 
       - string  
       - 
       - The name of the entity in the Partner's platform 
     

  



.. _CollectionOfPartnerUserAssociation:

CollectionOfPartnerUserAssociation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `PartnerUserAssociation`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _PartnerUserConsent:

PartnerUserConsent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - timestamp 
       - string  
       -  required 
       - 
     
     * - user 
       - string  
       -  read only 
       - 
     
     * - partner 
       - string  
       -  read only 
       - 
     
     * - scope 
       - list  of  `PartnerConsentScope`_
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("PartnerUserConsent") 
     

  



.. _PartnerConsentScope:

PartnerConsentScope
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _CollectionOfPartnerUserConsent:

CollectionOfPartnerUserConsent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `PartnerUserConsent`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _UsersCollectionOut:

UsersCollectionOut
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - totalCount 
       - integer  
       -  required 
       - Numbers of users before permission checks, if available. 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     
     * - items 
       - list  of  `User`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     

  



.. _CollectionOfUser:

CollectionOfUser
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `User`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _UserRole2:

UserRole2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Enum set of user types that define application and administrative access. 

  



.. _UserOut:

UserOut
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Properties of a User. 

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - tokens 
       -   
       - 
       - Tokens used to grant the Partner's platform access to the User's data. 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("User") 
     
     * - userName 
       - string  
       - 
       - Username that the User can use to log into the MarketSight platformMust be unique across all Accounts and Partners. 
     
     * - firstName 
       - string  
       - 
       - The User's given name 
     
     * - middleName 
       - string  
       - 
       - The User's middle name or initial 
     
     * - lastName 
       - string  
       - 
       - The User's family name 
     
     * - email 
       - string  
       - 
       - The User's email address.Used to reset passwords and other correspondence. 
     
     * - company 
       - string  
       - 
       - The name of the User's company or organization. 
     
     * - title 
       - string  
       - 
       - The User's job title 
     
     * - expirationDate 
       - string  
       - 
       - If set, represents the last day on which the User can access MarketSight 
     
     * - role 
       -   
       - 
       - The level of permissions granted to the User 
     
     * - status 
       -   
       - 
       - Indication of the User account's condition.            Only `active` users can be used. 
     
     * - comped 
       - boolean  
       - 
       - If `true`, the User does not count against the `maxFullAccessUsers` limit.If `false`, the User does count towards the `maxFullAccessUsers` limit. 
     
     * - homeAccount 
       - string  
       -  read only 
       - ID of the Account that owns the User record 
     
     * - currentAccount 
       - string  
       -  read only 
       - ID of the Account last accessed by the User            For non-administrator users, this will always be the same as `HomeAccount`. 
     
     * - id 
       - string  
       -  read only 
       - 
     

  



.. _CollectionOfVariableCategory:

CollectionOfVariableCategory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `VariableCategory`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _VariableCategory:

VariableCategory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - name 
       - string  
       -  required 
       - Name of the Variable Category 
     
     * - description 
       - string  
       -  required 
       - Description of the category 
     
     * - hidden 
       - boolean  
       -  required 
       - If `true`, the category will be hidden. 
     
     * - id 
       - string  
       - 
       - 
     
     * - dataset 
       - string  
       - 
       - Dataset ID 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("VariableCategory") 
     

  



.. _CollectionOfVariable:

CollectionOfVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - items 
       - list  of  `Variable`_
       - 
       - 
     
     * - count 
       - integer  
       - 
       - 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Collection") 
     

  



.. _Variable:

Variable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - id 
       - string  
       - 
       - 
     
     * - dataset 
       - string  
       - 
       - 
     
     * - category 
       - string  
       - 
       - Variable Category Name or ID 
     
     * - code 
       - string  
       - 
       - Variable Code 
     
     * - label 
       - string  
       - 
       - Variable label 
     
     * - description 
       - string  
       - 
       - Variable Description 
     
     * - kind 
       -   
       -  read only 
       - 
     
     * - mrvModel 
       -   
       - 
       - Is used for MRV variables only 
     
     * - weightVariableModel 
       -   
       - 
       - Is used for weight variables only 
     
     * - object 
       - string  
       - 
       - String representing the object's type. Objects of the same type share the same value. ("Variable") 
     

  



.. _VariableKind:

VariableKind
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Set of enum values for determining the type of Dataset Variable 

  



.. _MRV:

MRV
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - countedValueCode 
       - number  
       - 
       - 
     

  



.. _WeightVariable:

WeightVariable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - calculationMethod 
       -   
       -  required 
       - 
     
     * - tolerance 
       - number  
       -  required 
       - 
     
     * - maxIterations 
       - integer  
       -  required 
       - 
     

  



.. _WeightVariableCalculationMethod:

WeightVariableCalculationMethod
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _UploadedVariableType:

UploadedVariableType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Set of enum values to indicate sub-type of uploaded variable. 

  



.. _UserDefinedVariableType:

UserDefinedVariableType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   Set of enum values to indicate sub-type of a user-defined variable. 

  



.. _MultiResponseVariableType:

MultiResponseVariableType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _VariableType:

VariableType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _DeleteVariables:

DeleteVariables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - variables 
       - list  
       -  required 
       - Variables to delete. variables must belong to the same dataset. 
     

  



.. _ConditionalValue:

ConditionalValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - include_missing 
       - boolean  
       -  required 
       - 
     
     * - all_other 
       - boolean  
       -  required 
       - 
     
     * - expr 
       - string  
       - 
       - A conditional expression in ordinal (plain) form. Variables and values are encoded using their respective codes.Q.1 = 0 and demo01 = 1 
     
     * - label 
       - string  
       - 
       - 
     
     * - code 
       - string  
       - 
       - 
     

  



.. _VariableValueType:

VariableValueType
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  



.. _VariableValueBase:

VariableValueBase
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - label 
       - string  
       - 
       - 
     
     * - code 
       - string  
       - 
       - 
     

  



.. _MrvValue:

MrvValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - prec_var 
       - string  
       - 
       - should be precedent variable code 
     
     * - label 
       - string  
       - 
       - 
     
     * - code 
       - string  
       - 
       - 
     

  



.. _VariableValue:

VariableValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - label 
       - string  
       - 
       - 
     
     * - code 
       - string  
       - 
       - 
     

  



.. _WeightValue:

WeightValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - type 
       -   
       -  required 
       - 
     
     * - targetProportion 
       - number  
       -  required 
       - 
     
     * - precedentVariable 
       - string  
       - 
       - 
     
     * - groupValueCode 
       - number  
       - 
       - 
     
     * - label 
       - string  
       - 
       - 
     
     * - code 
       - string  
       - 
       - 
     

  



.. _AnyVariableValue:

AnyVariableValue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - ConditionalValue 
       -   
       - 
       - 
     
     * - MrvValue 
       -   
       - 
       - 
     
     * - VariableValue 
       -   
       - 
       - 
     
     * - WeightValue 
       -   
       - 
       - 
     

  



.. _WeightVariableValues:

WeightVariableValues
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - filterVariable 
       - string  
       - 
       - 
     
     * - groupingVariable 
       - string  
       - 
       - 
     
     * - values 
       - list  of  `AnyVariableValue`_
       - 
       - 
     

  



.. _VariableValues:

VariableValues
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  

  

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Name
       - Type
       - Info
       - Description
     
     * - values 
       - list  of  `AnyVariableValue`_
       - 
       - 
     

  

