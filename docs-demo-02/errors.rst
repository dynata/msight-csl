**********************************
Error Reference
**********************************

.. module:: marketsight.exceptions

.. contents::
  :local:
  :depth: 3
  :backlinks: entry

----------

Handling Errors
=================

The **MarketSight API Client** raises exceptions which inherit from Python's
base :exc:`Exception <python:Exception>`. The "base" exception raised is
:exc:`marketsight.exceptions.MarketSightError`, form which all other exceptions
are derived.

  .. tip::

    The best practice when using the API client is to wrap any call to a resource
    method in a ``try: ... except ...:`` block.

Error Messages
---------------------

Because the **MarketSight API Client** produces exceptions which inherit from the
standard library, we leverage the same API. This means they print to standard
output with a human-readable message that provides an explanation for "what went
wrong."

These messages are available using the
:attr:`.message <marketsight.exceptions.MarketSightError.message>` property.

Stack Traces
--------------

Because the **MarketSight API Client** produces exceptions which inherit from the
standard library, it leverages the same API for handling stack trace information.
This means that it will be handled just like a normal exception in unit test
frameworks, logging solutions, and other tools that might need that information.

---------

Standard Errors
===================

MarketSightError (from :exc:`Exception <python:Exception>`)
-----------------------------------------------------------------

.. autoclass:: MarketSightError
  :members:

----------------------------------

BindingError (from :exc:`MarketSightError`)
-------------------------------------------------------

.. autoclass:: BindingError

--------------------------------------------------------

InternalAPIError (from :exc:`MarketSightError`)
--------------------------------------------------------

.. autoclass:: InternalAPIError

--------------------------------------------------------

RequestPayloadError (from :exc:`MarketSightError`)
--------------------------------------------------------

.. autoclass:: RequestPayloadError

--------------------------------------------------------

AuthenticationError (from :exc:`AuthenticationError`)
--------------------------------------------------------

.. autoclass:: AuthenticationError

--------------------------------------------------------

AuthorizationError (from :exc:`AuthenticationError`)
--------------------------------------------------------

.. autoclass:: AuthorizationError

--------------------------------------------------------

MissingAPIKeyError (from :exc:`AuthenticationError`)
--------------------------------------------------------

.. autoclass:: MissingAPIKeyError

--------------------------------------------------------

ResourceNotFoundError (from :exc:`MarketSightError`)
---------------------------------------------------------------

.. autoclass:: ResourceNotFoundError

--------------------------------------------------------

ConflictError (from :exc:`MarketSightError`)
--------------------------------------------------------

.. autoclass:: ConflictError

--------------------------------------------------------

DuplicateRecordError (from :exc:`ConflictError`)
--------------------------------------------------------

.. autoclass:: DuplicateRecordError

--------------------------------------------------------

HTTPConnectionError (from :exc:`MarketSightError`)
--------------------------------------------------------

.. autoclass:: HTTPConnectionError

--------------------------------------------------------

HTTPTimeoutError (from :exc:`HTTPConnectionError`)
--------------------------------------------------------

.. autoclass:: HTTPTimeoutError

--------------------------------------------------------

SSLError (from :exc:`MarketSightError`)
--------------------------------------------------------

.. autoclass:: SSLError