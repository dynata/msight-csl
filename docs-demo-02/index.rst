.. image:: _static/marketsight-logo.png
  :alt: Dynata Reporting & Analytics
  :align: right
  :width: 120
  :height: 120

|

####################################################
Dynata Reporting & Analytics API
####################################################

**Python bindings for the Dynata Reporting & Analytics (MarketSight) API**

.. |strong| raw:: html

 <strong>

.. |/strong| raw:: html

 </strong>

.. sidebar:: Version Compatibility

  The **MarketSight API Client** is designed to be compatible with:

    * Python 2.7
    * Python 3.6 or higher


.. include:: _unit_tests_code_coverage.rst

.. toctree::
  :hidden:
  :maxdepth: 3
  :caption: Contents:

  Home <self>
  Quickstart: Patterns and Best Practices <quickstart>
  Using the MarketSight API Client <using>
  API Reference <api>
  Error Reference <errors>
  Contributor Guide <contributing>
  Testing Reference <testing>
  Release History <history>
  Glossary <glossary>
  License <license>

The **MarketSight Client API** library provides Python bindings for the
:term:`Dynata Reporting & Analytics API`, providing a Pythonic interface for
interacting with the underlying platform's RESTful APIs. For more information
information on how to work with the API, please see
:doc:`Using the MarketSight API Client <using>`.

.. contents::
  :depth: 3
  :backlinks: entry

------------------------

*****************
Installation
*****************

.. include:: _installation.rst

Dependencies
=================

.. include:: _dependencies.rst

-----------------------------------

*********************************
Key MarketSight API Features
*********************************

* Ability to manage your Account settings within the Dynata Reporting
  & Analytics platform.
* Ability to manage data within the Dynata Reporting & Analytics platform.
* Ability to perform high-end statistical analyses on your data in the
  Dynata Reporting & Analytics platform.
* Ability to produce interactive visualizations of your data and insights.
* Ability to produce and share interactive dashboards to deliver your insights
  to downstream users.
* Ability to embed or otherwise integrate the MarketSight platform into your
  applications.

-----------------------

**********************************
Hello, World and Basic Usage
**********************************

1. Initialize the Library
==========================================

.. include:: _initialization.rst

2. Authorize Against the API
================================

.. include:: _authorization.rst

3. Inspect the API Structure (optional)
============================================

.. include:: _inspection.rst

4. Call the API
=====================

Execute API calls to perform operations, for example:

.. code-block:: python
  :linenos:

  # Retrieve an Account
  account = api.Accounts.retrieve(account_id = "97bfec8e2b86495b8834ec621b29b756")

  # Retrieve a User
  user = api.Users.retrieve(user_id = "a54847d7e69d422f9004134069bed353")

  # Retrieve a Dataset's Meta-data
  dataset = api.Datasets.retrieve(dataset_id = "6d82a02c6bc2487a940f85ac62c04e0f")

---------------

*********************
Questions and Issues
*********************

You can ask questions and report issues on the project's
`Github Issues Page <https://github.com/dynata/msight-csl/issues>`_

-----------------

*********************
Contributing
*********************

We welcome contributions and pull requests! For more information, please see the
:doc:`Contributor Guide <contributing>`

-------------------

*********************
Testing
*********************

We use `TravisCI <http://travisci.org>`_ for our build automation and
`ReadTheDocs <https://readthedocs.org>`_ for our documentation.

Detailed information about our test suite and how to run tests locally can be
found in our :doc:`Testing Reference <testing>`.

--------------------

**********************
License
**********************

**MarketSight Core** is made available under an :doc:`MIT License <license>`.

----------------

********************
Indices and tables
********************

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`