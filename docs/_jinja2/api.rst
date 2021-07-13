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



.. _object_models:

Object Models
-----------------

The Dynata Reporting & Analytics API relies on a wide variety of object models,
each of which is used as either a payload or a response object returned by the
**MarketSight API Client**. Below you will find a list of those object models,
along with documentation about their properties and their meaning.

