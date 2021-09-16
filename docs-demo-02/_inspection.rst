The **MarketSight API Client** API bindings are generated programmatically when the
API is initialized, based on an OpenAPI v.2.0 (Swagger) specification file. As a
result, it is possible that the API reference will automatically evolve as the
Dynata Reporting & Analytics API evolves.

.. warning::

  **PLEASE NOTE:** The Dynata Reporting & Analytics API should not be rolling out
  breaking changes without advance notice reported through the
  `Dynata Status Page <https://status.dynata.com>`_. Therefore, code relying on
  the **MarketSight API Client** library should be stable, pending standard notices
  about deprecated functionality distributed via the status page.

Syntactically, the **MarketSight API Client** library's API is structured along the
following lines:

.. code-block:: python

  api_instance.<Resource>.<resource_method>([**kwargs])

where:

  * ``<Resource>`` is the ``Tag`` in the OpenAPI v.2.0 (Swagger) specification
    associated with the resource (e.g. ``Accounts``, ``Dashboards``,
    ``Crosstabs``, etc.)
  * ``<resource_method>`` is an operation identifier defined in the specification
    for that resource (e.g. ``list``, ``create``, ``retrieve``, etc.)
  * the remaining elements align to standard Python syntax.

Given the dynamic nature of the API syntax, there are two ways to familiarize
yourself with the API syntax and Python bindings provided by the
**MarketSight API Client**:

  #. By reviewing this documentation.
  #. By inspecting the API bindings within Python.

When inspecting the resources and methods exposed by the **MarketSight API Client**
within Python, there are two patterns you may find useful:

.. tabs::

  .. tab:: Printing to Console

    .. code-block:: python
      :linenos:

      from marketsight_core import ApplicationAPI

      api = ApplicationAPI("https://application.marketsight.com/api/v1/")

      for resource_name in api.__dir__():
         print (resource_name)

    which will output the following to standard out (typically the console):

    .. code-block:: bash
      :linenos:

      Accounts
      Dashboards
      Data_Sources
      Datasets
      Charts
      Crosstabs
      Data_Views
      Variables
      Files
      Injected_Scripts
      Key_Findings
      Users
      Jobs
      Membership
      Event_Log
      Generate
      OAuth
      Partners
      >

    You can then inspect the methods exposed by each resource using a similar
    pattern:

    .. code-block:: python
      :linenos:

      for method_name in api.Variables.__dir__():
         print (method_name)

    which will output the following to standard out (typically the console):

    .. code-block:: bash
      :linenos:

      get_grid_sets
      delete_categories
      get_categories
      post_category
      delete_category
      get_category
      patch_category
      put_category
      get_category_variables
      get_identities
      delete_variables
      list
      create
      delete
      retrieve
      modify
      delete_identity
      get_identity
      patch_identity
      post_identity
      put_identity
      get_grid_set
      get_variable_values
      >


  .. tab:: Inspecting in Code

    .. code-block:: python
      :linenos:

      # Import the ApplicationAPI class
      from marketsight_core import ApplicationAPI

      # Instantiate the API.
      api = ApplicationAPI("https://application.marketsight.com/api/v1/")

      # Retrieve the resource collection.
      resources = api.inspect_resources()

      # Returns: dict where keys are "Resources" and values are a list of
      # methods:
      #
      # {
      #    ...,
      #    "Variables": [
      #        "get_grid_sets",
      #        "delete_categories",
      #        "get_categories",
      #        "post_category",
      #        "delete_category",
      #        ...
      #    ],
      #    ...
      # }