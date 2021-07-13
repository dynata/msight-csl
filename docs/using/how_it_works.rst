.. sidebar:: Version Compatibility

  The **MarketSight API Client** is designed to be compatible with:

    * Python 3.6 or higher

The **MarketSight API Client** is a Python library that wraps the RESTful
:term:`Dynata Reporting & Analytics API`. It is designed to be a direct
Pythonic interface for the API, allowing you to simplify your Python
integration with the Reporting & Analytics API.

How does it accomplish this? Well, by providing a Pythonic way of interacting
with the resources and endpoints exposed by the API. Consider the following
scenarios (both in Python), where we want to retrieve the number of full-access
users authorized for our Reporting & Analytics account:

.. tabs::

  .. tab:: Direct API Call

    .. code-block:: python
      :linenos:

      import requests

      headers = {
          'Accept': 'application/json',
          'Authorization': 'Bearer {access-token}',
          'Authorization': 'API_KEY'
      }

      account_id = 'MY ACCOUNT ID'

      endpoint_url = f'https://application.marketsight.com/api/v1/accounts/{MY ACCOUNT ID}'

      try:
        r = requests.get(endpoint_url,
                         params = {},
                         headers = headers)
      except Exception:
        # Some error handling goes here!

      account_json = r.json()

      max_full_access_users = account_json.get('maxFullAccessUsers', 0)

  .. tab:: Using the API Client

    .. code-block:: python
      :linenos:

      import marketsight

      api = marketsight.client()

      api.connect(client_id = 'MY CLIENT ID', client_secret = 'MY CLIENT SECRET')

      account = api.Accounts.retrieve('{MY ACCOUNT ID}')

      max_full_access_users = account.maxFullAccessUsers

As the example above shows, you can avoid having to worry about API endpoints,
manually constructing headers/parameters, handling HTTP communication errors,
etc. The **MarketSight API Client** handles all of that for you.

---------------------

Getting Started
--------------------

To get started using the **MarketSight API Client**, it is very straightforward.
You just have to follow the following four steps:

1. Install the MarketSight API Client
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To install **MarketSight API Client**, just execute:

  .. code:: bash

   $ pip install marketsight

2. Initialize an Instance of the API Client
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The **MarketSight API Client** can be initialized using
the :func:`client <marketsight.client>` factory-function.

This function returns an
:class:`ApplicationAPI <marketsight.application_api.ApplicationAPI>` instance,
whose methods provide a Python interface for interacting with the
:term:`Dynata Reporting & Analytics API`.

  .. note::

    The **MarketSight API Client** is built on top of
    `Bravado <https://bravado.readthedocs.io/en/stable/>`_, a library which
    constructs Python object representations programmatically at the moment of
    initialization.

    In other words, the API structure and syntax is dynamically created based on the
    OpenAPI 2.0 (Swagger) specification supplied at the moment of initialization
    as described below.

.. tabs::

  .. tab:: Factory Function

    .. code-block:: python
       :linenos:

       # Import the MarketSight API Client
       import marketsight

       # Call the "client" factory function.
       api = marketsight.client()

       # by defult `client` works with `https://application.marketsight.com/api/v1/swagger/public.json`
       # for working on another MarketSight system you can
       # use some another OpenApi url:
        api = marketsight.client("https://eu.application.marketsight.com/api/v1/swagger/public.json")

  .. tab:: Dynamic Initialization

    .. code-block:: python
       :linenos:

       import requests

       # Import the ApplicationAPI class.
       from marketsight_core import ApplicationAPI


       # Retrieve the most-recent production OpenAPI v.2.0 (Swagger)
       # specification for the Dynata Reporting & Analytics API.
       swagger_spec = requests.get(
           "https://sandbox.marketsight.com/platform-dev-api/v1/swagger/development.json"
       ).json()

       # Initialize against the production API using the programmatically
       # retrieved OpenAPI v.2.0 specification.
       api = ApplicationAPI("https://application.marketsight.com/api/v1/",
                            swagger_spec)


  .. tab:: Use With An External API

    You can use our library for working with any external API that has support
    correct OpenApi v2 (Swagger) format.

    .. code-block:: python
       :linenos:

       # Import the MarketSight API Client
       import marketsight

       # Initialize client based on external OpenAPI 2.0 (swagger) json file
       # for more details visit demo page of `https://petstore.swagger.io/`
       api = marketsight.client("https://petstore.swagger.io/v2/swagger.json")

       # retrieve the list of pet's names which status are `available`
        for pet in api.pet.find_pets_by_status(status=['available']):
            print(pet.name)


3. Authenticate Against the API
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. seealso::

  * :ref:`Authentication and Authorization <authentication_and_authorization>`

4. Execute Methods against Resources
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Using the API Client instance, call methods that belong to
:term:`Resources <Resource>` (which are themselves members of the API client
instance). These methods perform operations  against the
:term:`Dynata Reporting & Analytics API`, and that's it!

----------------------------------

.. _working_with_resources:

Working With Resources
--------------------------

Just like any API, the :term:`Dynata Reporting & Analytics API` exposes a variety
of :term:`Resources <Resource>` which can be created, retrieved, updated, or
deleted programmatically using the API. Examples of resources might include
:class:`Accounts <marketsight.application_api.Accounts>`,
:class:`Datasets <marketsight.application_api.Datasets>`,
:class:`Crosstabs <marketsight.application_api.Crosstabs>`, or
:class:`Charts <marketsight.application_api.Charts>`.

Syntactically, the **MarketSight API Client** exposes these resources as
members (properties) of the API client instance. So if we have an API client
instance called ``api`` in our code, we can execute operations on them using
the following pattern:

  .. code-block:: python

    api.<Resource>.<resource_method>([**kwargs])

where ``<Resource>`` is the name of a supported resource, ``<resource_method>``
is a method exposed for that resource, and ``[**kwargs]`` are keyword arguments
that you want to supply to that method.

  .. note::

    Each time you initialize an API client instance, that instance dynamically
    generates its member resources. It does this by downloading the RESTful API's
    OpenAPI Specification, dynamically creating a resource for each tag it
    finds in that specification, and then dynamically creating a method for each
    route (endpoint and HTTP method combination) specified for that tag.

    So the ``'Accounts'`` tag will become the
    :class:`Accounts <marketsight.application_api.Accounts>` resource, and the
    ``POST /accounts/{account_id}`` route will become the
    :meth:`Accounts.create <marketsight.application_api.Accounts.create>` method.

  .. tip::

    If you need to discover programmatically what resources and resource methods
    are supported by the API client, you can do so by calling Python's magic
    ``__dir__()`` method on the API client instance:

    .. code-block:: python
      :linenos:

      # Import the MarketSight API Client
      import marketsight

      # Create an API Client instance
      api = marketsight.client()

      # Produce a list of the Resources
      resource_names = api.__dir__()

      resource_method_map = {}

      for resource in resource_names:
          # Get the Resource from the API client.
          resource_object = getattr(api, resource)

          # Produce a list of the methods supported by the resource.
          methods = resource_object.__dir__()

          # Add the Resource and its methods to the resource_method_map dictionary
          resource_method_map[resource] = methods

.. seealso::

  * :doc:`API Reference <api>`: :ref:`Resources <resources>`


--------------

.. _using_resource_methods:

Using Resource Methods
-------------------------

Many of the resource methods supported by the API client take arguments. These
arguments will often be identifiers to scope your API requests, or they may be
payloads to submit to the API itself. We recommend reviewing the
:doc:`API Reference <api>` to review the arguments that each method supports.

  .. note::

    If a method argument is an object, then it inherently expects to receive a
    :class:`dict <python:dict>`. Each resource method documents the arguments
    it needs, and links to the specification of the object model that it
    expects to receive.

    .. seealso::

      * :doc:`API Reference <api>`: :ref:`Object Models <object_models>`

  .. tip::

    Many resource methods accept various ID arguments (e.g. ``account_id``,
    ``user_id``, etc.) which act as filters to more precisely profile the data
    you want the API to return. Argumenst that end in ``_id`` are typically
    optional, and will default to a value of ``'-'`` (which leaves is a wildcard
    that treats them as unconstrained).

    This can be valuable if you want to shorten / simplify your code. For example,
    the following two operations are identical:

    .. code-block:: python
      :linenos:

      user = api.Users.retrieve(user_id="a54847d7e69d422f9004134069bed353")

      user = api.Users.retrieve(account_id="-", user_id="a54847d7e69d422f9004134069bed353")

--------------------------

.. _long_running_jobs:

Asynchronous Jobs
------------------------

The Dynata Reporting & Analytics platform allows you to perform various analyses
on large and complex :term:`datasets <Dataset>`. Given that some datasets are large with
many variables and records, certain tasks you ask the API to perform may
naturally take a long time to complete. Imagine, for example, that you want to
download a 1 GB SPSS file that contains all of the data within a single
:class:`Dataset <marketsight.application_api.Datasets>`. It will take time to
process the data and generate the SPSS file before the file is even ready for
you to download. This would be considered an "Asynchronous :term:`Job`".

When you call a method that initiates an asynchronous job, the method will
return a :ref:`AsyncResult` object model, which contains information about the
asynchronous job (including its unique ``job_id``).

While that job is running, you can feel free to call other methods or perform
other activities using the API. You can then periodically check back to see
whether the job has completed using the
:class:`Jobs <marketsight.application_api.Jobs>` resource.

  .. tip::

    .. _working_with_helpers:

    **Working with Helpers**

    Because so many of the API client operations produce long-running jobs,
    we have developed a special
    :class:`Helper <marketsight.application_api.Helper>` class which can be used
    to automatically wait and poll to see whether the operation has completed.

    Using the :class:`Helper <marketsight.application_api.Helper>` saves you the
    trouble of having to write or maintain scaffolding to manage polling
    (querying and then retrying) the API to see if a job has completed.

    To use the :class:`Helper <marketsight.application_api.Helper>` class, you
    can easily find it on the ``helper`` property of your API client instance.
    To use it in practice, just call it using the
    :meth:`.helper.wait_until() <marketsight.application_api.Helper.wait_until>`
    method as shown below:

      .. code-block:: python
        :linenos:

        job_result = api.helper.wait_until(fnc = api.Jobs.retrieve,
                                           fnc_kwargs = {'job_id': 1},
                                           conditions = {'result': 'succeeded'}
                                           )

    Because so many long-running jobs relate to the downloading of data files,
    we've also created useful helper methods to directly download the binary
    data produced when a job completes, using the
    :meth:`.helper.wait_and_download_blob() <marketsight.application_api.Helper.wait_and_download_blob>`
    method:

      .. code-block:: python
        :linenos:

        binary_data = api.helper.wait_and_download_blob(job_id = '123456',
                                                        sleep_time = 6,
                                                        max_attempts = 20)

    .. seealso::

      * :doc:`API Reference <api>`: :class:`Helper <marketsight.application_api.Helper>`

---------------

Handling Errors
--------------------

The **MarketSight API Client** raises exceptions which inherit from Python's
base :exc:`Exception <python:Exception>`. The "base" exception raised is
:exc:`marketsight.exceptions.MarketSightError`, form which all other exceptions
are derived.

  .. tip::

    The best practice when using the API client is to wrap any call to a resource
    method in a ``try: ... except ...:`` block.

Because many of the exceptions that the API client raises may be tied to errors
returned by the :term:`Dynata Reporting & Analytics API` itself, the exceptions
subclassed from
:exc:`MarketSightError <marketsight.exceptions.MarketSightError>` also feature
a number of special properties like
:attr:`status_code <marketsight.exceptions.MarketSightError.status_code>` and
:attr:`message <marketsight.exceptions.MarketSightError.message>`.

Furthermore, for the sake of convenience all exceptions feature methods to
serialize their content to :class:`dict <python:dict>` or JSON.

.. seealso::

  * :doc:`Error Reference <errors>`