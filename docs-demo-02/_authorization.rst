In order to use the Dynata Reporting & Analytics API, you need to be authenticated
and authorized to perform various actions. This authentication and authorization
occurs at two different levels:

  * **Partner-level**. This level enables your application to manipulate and
    create :class:`Accounts <Account>` and :class:`Users <User>` within those
    accounts.
  * **User-level**. This level enables your application to "act as" or
    "impersonate" a specific :class:`User`

In general terms, the process of authorizing your API instance always consists
of the following steps:

  #. Instantiate a **MarketSight API Client** API instance.
  #. Retrieve the appropriate :term:`access token` (partner-level or user-level).
  #. Set the access token on your API instance.
  #. Use the API.

Partner-level Authorization
---------------------------------

.. code-block:: python
   :linenos:

    # Initialize the MarketSight API Client.
    api = marketsight.client()

    # Connect your instance to the API and authorize as a partner.
    api.connect(
        client_id = "621ead33dccd40e0b5dsfds00bd3027a5aa34",
        client_secret = "WFDXcvYTJUIKracca0c9a26e14b2e9ew0292d22acewrbf1fc0WsEWRsd"
    )

    # (optional) Check that the API instance is properly authorized.
    print(api.Membership.get_me())


User-level Authorization
-----------------------------

.. error::

  User-level Authorization is not currently functional or supported at this
  time. However, it will be supported in a future release of the
  **MarketSIght API Client**.

Your API instance can only be authorized at one level at a time, which means that
if you need to use user-level authorization, you must create two API instances:

  * One API instance at the Partner-level which you use to retrieve your
    User-level token.
  * One API instance at the User-level which you authorize at the User-level.

.. code-block:: python
   :linenos:

   # Import the MarketSight API Client.
   import marketsight


   # Instantiate the MarketSight Core API for Partner-level access.
   partner_api = marketsight.client()

   # Retrieve your partner-level API Access Token.
   response = partner_api.connect(
      client_id="621ead33dccd40e0b5dsfds00bd3027a5aa34",
      client_secret='WFDXcvYTJUIKracca0c9a26e14b2e9ew0292d22acewrbf1fc0WsEWRsd'
   )

   # Instantiate the MarketSight Core API for User-level access.
   user_api =  marketsight.client()

   # Authorize the User-level API instance for user-level access.
   user_api.access_token = partner_api.Partners.get_user_token(user_id = "3dccd40e0b5dsfds00bd3027")