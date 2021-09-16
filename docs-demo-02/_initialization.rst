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
       # use some another OpenAPI 2.0 (swagger) definition:
        openapi_json_url = "https://eu.application.marketsight.com/api/v1/swagger/public.json"
        api = marketsight.client(openapi_json_url)

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