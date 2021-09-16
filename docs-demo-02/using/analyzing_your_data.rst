The Dynata Reporting & Analytics platform is all about finding and then sharing
statistical insights from your data. Now that we've covered how to
:ref:`manage your datasets <managing_datasets>`, the next step is to actually
analyze your data.

:term:`Crosstabs <Crosstab>` and :term:`Data Views <Data View>` are the
analytical heart of the Dynata Reporting & Analytics platform. They are data
tables that are calculated from your respondent-level survey data, and which
ultimately drive your data visualizations and dashboards.

--------------

.. _creating_data_tables:

Creating Data Tables
---------------------------


Assuming that we have some raw data in csv/xls/sav format:

.. image:: _static/working_with_crosstab_creating_01.png
  :alt: Anatomy of a Crosstab
  :align: center

In a couple of several steps we will create
:term:`Crosstabs <Crosstab>`, :term:`Data Views <Data View>`, :term:`Charts <Chart>`
and put all of them into :term:`Dashboard <Dashboard>`.


Uploading DataSet
^^^^^^^^^^^^^^^^^^^^^^

For illustrative purposes, let's convert the data from the table above into
python code and upload them as :term:`DataSet <DataSet>`:


.. code-block:: python
    :linenos:

    import tempfile as tmp
    import time

    account_id = account  # put your account id here

    # create  csv file with the raw data and upload it to MarketSight
    with tmp.NamedTemporaryFile(prefix="demo_data_file", suffix=".csv") as f:
        f.write(
            "\n".join(
                [
                    "FirstName,LastName,Gender,Age,Pet",
                    "Rubie,Johnston,Female,27,dog",
                    "Rebecca,Cooper,Female,26,cat",
                    "Tony,Turner,Male,21,cat",
                    "Lily,Casey,Female,25,dog",
                    "Maya,Baker,Female,22,dog",
                    "Alisa,Phillips,Female,23,cat",
                    "Natalie,Kelly,Female,23,dog",
                    "Lilianna,Watson,Female,21,cat",
                    "Daniel,Farrell,Male,21,cat",
                    "Myra,Owens,Female,24,cat",
                    "Jordan,Morgan,Male,30,dog",
                    "Henry,Douglas,Male,19,cat",
                    "Preston,Harris,Male,28,dog",
                    "Rubie,Wilson,Female,20.dog",
                    "Charlie,Watson,Male,19,cat",
                ]
            ).encode()
        )
        f.seek(0)

        job = api.Datasets.create(
            account_id=account_id,
            model={"folderPath": ["Demo", "DataSet", time.time()]},
            data=f,
        )
        dataset_id = job.dataset.id

    # wait until MarketSight will finish processing the data
    while api.Datasets.retrieve(dataset_id=dataset_id).status != "ready":
        time.sleep(2)


Defining Crosstabs
^^^^^^^^^^^^^^^^^^^^^^

Parameter `dataset_id` contains the reference to
:term:`DataSet <DataSet>` with the data from the table above, so:


.. code-block:: python
  :linenos:

  model = {
    "name": f"Demo Crosstab {time.time()}",
    "description": f"description {time.time()}",
    "definition": {
        "rows": {
            "sections": [
                {
                    "variables": [
                        {
                            "DefinitionRowVariable": {
                                "values": [],
                                "kind": "variable",
                                "code": "Pet",
                                "includeAllValues": True,
                            }
                        }
                    ],
                    "calculations": {"count": True, "columnPercent": True},
                }
            ]
        },
        "columns": {
            "includeTotals": True,
            "sections": [
                {
                    "variables": [
                        {
                            "DefinitionColumnVariable": {
                                "reverseOrderOfValues": False,
                                "name": "",
                                "values": [],
                                "kind": "variable",
                                "code": "Gender",
                                "includeAllValues": True,
                            }
                        }
                    ]
                }
            ],
        },
        "interactivity": {
            "display": {
                "theme": "light",
                "accentColor": "#456797",
                "selectionColor": "#FFFFFF",
            },
            "rows": {"label": "Demo Title For Rows"},
            "columns": {"label": "Demo Title For Columns"},
            "filters": {
                "controlTypeLocked": True,
                "panelPosition": "left",
                "panelExpanded": True,
                "variablesExpanded": True,
                "label": "Filter",
            },
            "autoApply": True,
        },
    },
  }
  # Creating CrossTab itself
  crosstab = api.Crosstabs.create(dataset_id=dataset_id, model=model)
  print(crosstab.id)


result is:

.. image:: _static/working_with_crosstab_creating_02.png
  :alt: Created Crosstab
  :align: center


Updating Crosstab
^^^^^^^^^^^^^^^^^^^^^^

For updating Crosstab there are 2 different methods:

First one allows to change simple Grosstab attribute(s) like
 `name` and `description`:

.. code-block:: python
  :linenos:

  crosstab.name = crosstab.name + " updated"
  crosstab.description = crosstab.description + " updated"

  updated_crosstab = api.Crosstabs.modify(
      dataset_id=dataset_id,
      crosstab_id=crosstab.id,
      model=crosstab
  )
  print("New Crosstab name:: " + updated_crosstab.name)
  print("New Crosstab description:: " + updated_crosstab.description)

Second one helps to update Grosstab definition itself:

.. code-block:: python
  :linenos:

  api.Crosstabs.patch_definition(
      dataset_id=dataset_id,
      crosstab_id=crosstab.id,
      model={
          "rows": {
              "sections": [
                  {
                      "variables": [
                          {
                              "DefinitionRowVariable": {
                                  "values": [],
                                  "kind": "variable",
                                  "code": "Pet",
                                  "includeAllValues": True,
                              }
                          }
                      ],
                      "calculations": {"count": True, "columnPercent": False},
                  }
              ]
          }
      },
  )

after updating the definition the Crosstab should not contains the rows with percentage calculation:

.. image:: _static/working_with_crosstab_updating_02.png
  :alt: Created Crosstab
  :align: center


.. _creating_dataviews:

Defining Data Views
^^^^^^^^^^^^^^^^^^^^^^^^^

DataViews is a simplest plain table structure. To create it just invoke:

.. code-block:: python
   :linenos:


   data_view = api.Data_Views.create(
        dataset_id=dataset_id,
        model={
            "definition": {
                "columns": {
                    "hideEmptyResponses": True,
                    "variables": [
                        {"code": "FirstName"},
                        {"code": "LastName"},
                        {"code": "Gender"},
                        {"code": "Age"},
                    ],
                }
            },
            "dataset": dataset_id,
            "name": f"DataView for {dataset_id}",
            "description": f"DataView for {dataset_id}",
        },
   )

Sometimes dataset contains a lot of variables, so we get all of them automatically using the
additional api call :meth:`api.Variables.list <marketsight.application_api.Variables.list>`


.. code-block:: python
   :linenos:

    # this example do the same as code above but allow us retrive list of all available
    data_view2 = api.Data_Views.create(
        dataset_id=dataset_id,
        model={
            "definition": {
                "columns": {
                    "hideEmptyResponses": True,
                    "variables": api.Variables.list(dataset_id=dataset_id).items
                }
            },
            "dataset": dataset_id,
            "name": f"DataView for {dataset_id} variant 2",
            "description": f"DataView for {dataset_id} variant 2",
        },
    )



----------------------

.. _retrieving_data_tables:

Retrieving Your Data Tables
--------------------------------

Now that we have created our data tables, our next step is to retrieve them.
While you can always view and adjust your data tables in the MarketSight
application, you can also interact with them programmatically through the
MarketSight API client.

.. _retrieving_crosstabs:

Retrieving Crosstabs
^^^^^^^^^^^^^^^^^^^^^^^^

To retrieve your crosstab, simply pass it to the
:meth:`Crosstabs.calculate() <marketsight.application_api.Crosstabs.calculate>`
method, along with a :ref:`CrosstabCalculationRequest` model that indicates
the format you wish to receive.

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

.. _retrieving_data_views:

Retrieving Data Views
^^^^^^^^^^^^^^^^^^^^^^^^

To  retrieve/download you DataView into local file just pass dataView.id into the
:meth:`Data_Views.export()  <marketsight.application_api.Data_Views.export>`,
along with a :ref:`DataViewCalculationRequest` model that indicates the format you wish to receive.


.. tabs::

  .. tab:: Excel format

    .. code-block:: python
       :linenos:

        async_job = api.Data_Views.export(
            dataview_id=data_view.id,
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
        with open('result.xls', 'wb') as fp:
            for blob in api.Jobs.retrieve(job_id=job.id).blobs:
                fp.write(
                    api.Jobs.get_blob_data(
                        job_id=job.id,
                        blob_id=blob.id
                    )
                )


    the `result.xls` will be downloaded locally:

    .. image:: _static/working_with_data_view_exported2xls.png
      :alt: Export DataView
      :align: center

  .. tab:: SPSS format

    .. code-block:: python
       :linenos:

        async_job = api.Data_Views.export(
            dataview_id=data_view.id,
            model={
                "format": "spss",
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
        with open('result.sav', 'wb') as fp:
            for blob in api.Jobs.retrieve(job_id=job.id).blobs:
                fp.write(
                    api.Jobs.get_blob_data(
                        job_id=job.id,
                        blob_id=blob.id
                    )
                )


    the `result.sav` will be saved locally.


--------------------

.. _working_with_crosstab_results:

Working with Crosstab Results
--------------------------------

:term:`Crosstabs <Crosstab>` can be quite complicated and at times very large,
so if you are going to programmatically work with their results it can be
a complicated undertaking. For that reason, we provide a detailed walkthrough
given an example.

.. _anatomy_of_a_crosstab:

Anatomy of a Crosstab
^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's start by discussing the anatomy of a crosstab. The following
**Simple Crosstab** will be used for reference, and is what you would see either
in the MarketSight application or in an exported Excel file of the
:class:`Crosstab <marketsight.application_api.Crosstabs>`.

As you can see, this crosstab has a single un-nested :term:`Column Variable`
(*Gender*) and one un-nested :term:`Row Variable` (*Age*). There are also two
:term:`calculations <Calculation>` enabled: count and frequency %.

  .. image:: _static/working_with_crosstab_results_01.png
    :alt: Simple Crosstab
    :align: center

In order to fully understand how the example crosstab result shown above is
represented by the API in JSON, it is important to first understand specific
terminology used to describe the crosstab result's components:

  .. image:: _static/working_with_crosstab_results_02.png
    :alt: Anatomy of a Crosstab
    :align: center

Variable Labels
""""""""""""""""""

When defining a crosstab, you can set Variable Labels to either:

  * Variable Name (default value)
  * Variable Codes
  * Variable Codes and Names

This determines what content is returned as the variable label in your crosstab
object. Here is how the *Age* row variable would be labeled for each of these
three settings:

  .. list-table::
     :widths: auto
     :header-rows: 1

     * - Variable Name
       - Variable Codes
       - Variable Codes and Names
     * - Age
       - demo001
       - demo001: Age

  .. tip::
    The relationship between variable codes and variable names in your dataset may
    vary, based on the system that was used to prepare the dataset before it was
    uploaded to the platform. Therefore, when designing an application that works
    with crosstab results programmatically it is important to always be aware of the
    Variable Labels setting so that you can provide the desired user experience.

.. _crosstab_calculations:

Calculations
"""""""""""""""

Your crosstab definition determines which :term:`calculations <Calculation>` are
executed and returned within your crosstab result. There are many available
calculations, including:

.. _variable_level_calculations:

  **Variable-level Calculations**

    :Sample Sizes: Displays the sample size for the selected variable.
    :Unweighted Sample Sizes: Only available if weighting is in use.  Displays the unweighted sample size for the selected variable.
    :Effective Base: Only available if weighting is in use.  Displays the effective base for the selected variable.
    :Mean: Displays the average of all values for the row variable.
    :Median: Displays the median for the row variable. The median is the value calculated when 50% (weighted) of the responses for the row variable are greater than the value, and 50% (weighted) of the responses are less than the value. Ties are broken by taking the average of the two tying values.
    :Standard Deviation: Displays a measure of the amount that the row variable's values vary from the mean.
    :Standard Error: Displays a measure of the accuracy of a statistical estimate.
    :Min: Displays the minimum value within the variable.
    :Max: Displays the maximum value within the variable.
    :Sum: Displays the sum of all values for the variable.
    :Valid Cases: (:term:`Multiple Response Variables <Multiple Response Variable>` only) What is the number of *respondents* who selected *at least one* answer to a given survey question?
    :Total Mentions: (:term:`Multiple Response Variables <Multiple Response Variable>` only) What is the *total* number of *responses* across the *entire* question?
    :Unweighted Total Mentions: (:term:`Multiple Response Variables <Multiple Response Variable>` only) Only available if weighting is in effect. What is the *total* number of responses across the *entire* question?
    :Total Unique Mentions: (:term:`Multiple Response Variables <Multiple Response Variable>` only) How many *different respondents* said "Yes" *at least once* (and possibly more than once) to each option?

.. _value_level_calculations:

  **Value-level Calculations**

    :Count: Displays the number of times each unique value occurs. Note that if weighting is in effect, weighted counts will be displayed.
    :Column %: Displays the % of the cell in relation to the total percentage of the column.
    :Row %: Displays the % of the cell in relation to the total percentage of the row.
    :Mentions: (:term:`Multiple Response Variables <Multiple Response Variable>` only) The number of responses for a particular option.
    :Unweighted Mentions: (:term:`Multiple Response Variables <Multiple Response Variable>` only) Only available if weighting is in effect. The number of unweighted responses for a particular option.
    :% Sample Size: (:term:`Multiple Response Variables <Multiple Response Variable>` only) What percentage of respondents who were asked a particular question chose this option?
    :Row % Mentions: (:term:`Multiple Response Variables <Multiple Response Variable>` only) Row % of respondents who selected a particular option.
    :% Valid Cases: What percentage of respondents chose at least one of the options for a particular question?  If weighting is in effect, this will display weighted % of valid cases.
    :% Total Mentions: (:term:`Multiple Response Variables <Multiple Response Variable>` only) The percentage of all total "yes" responses represented by a particular option.
    :Unique Mentions: (:term:`Multiple Response Variables <Multiple Response Variable>` only) How many respondents said "yes" to a particular option at least once.
    :Unique % Sample Size: (:term:`Multiple Response Variables <Multiple Response Variable>` only) What percentage of respondents who were asked a particular question chose this option at least once.
    :Unique % Valid Cases: What percentage of respondents who chose at least one option for a given question also chose this option at least once.

.. _date_variable_calculations:

  **Date Variable Calculations**

    :yyyy-mm-dd: Sortable format (ISO-8601). Example: **2019-01-23**
    :mm-dd-yyyy: United States date format. Example: **01-23-2019**
    :dd-mm-yyyy: European date format. Example: **23-01-2019**

Value and Value Positions
""""""""""""""""""""""""""""

The crosstab result always respects the :ref:`CrosstabDefinition` used to
generate it. As a result, if you exclude particular values in the crosstab
definition, then the positions of column and row values in your crosstab result
will change.

In other words, *Age* value "55 – 64" may not be on the seventh row of your
crosstab result if you have excluded preceding row values within the
:ref:`CrosstabDefinition`.

Crosstab results returned as JSON objects rely on ordered lists of values. To
locate the :term:`calculation <Calculation>` for a particular column and row
intersection, your application must:

  #. Identify the desired column and row positions by parsing the column header
     and row header sections.
  #. Offset to the desired row based on the position identified in step **(1)**.
  #. Offset to the desired column based on the position identified in step **(1)**.

JSON Result
^^^^^^^^^^^^^^

The general structure of the JSON object returned has three core areas
corresponding to the Column Variable Headings, Row Variable Headings, and
Calculations sections. The image below identifies the three core areas to be
examined. All sections are order dependent and rely on relative ordering
within the section.

.. image:: _static/working_with_crosstab_results_03.png
  :alt: Basic Layout
  :align: left
  :width: 200

For example, in the example above to find calculations related to "Males - 65
and Older", you will need to traverse the calculations section to the 8th row
and then to the 2nd set of values.

Column Variable Headings
""""""""""""""""""""""""""

The ``Header.Sections`` object contains a list of ``Rows``, where each row is an
object containing a list of ``Cells`` representing column variable labels and
column variable values.

To locate a column variable value, you must traverse the ``Sections``
representing column variables.

Each section will have multiple ``Rows`` to denote the variable name and
variable values. The snippet below shows the *Gender* variable and headings for
three values. Note this example includes Column Totals indicated by type
``TotalHeadingCell`` in the first position within the ``Cells`` list:

  .. code-block:: javascript
    :linenos:

    "Header": {
      "Corner": {
        "Text": null,
        "SortDirection": "None",
        "RowSpan": 2
      },
      "Sections": [
        {
          "CutIndex": 0,
          "Rows": [
            {
              "Cells": [
                { "$type": "SectionHeadingCell", "Text": "Gender", "ColumnSpan": 3 }
              ]
            },
            {
              "Cells": [
                { "$type": "TotalHeadingCell" },
                { "$type": "ValueHeadingCell", "Text": "Male" },
                { "$type": "ValueHeadingCell", "Text": "Female" }
              ]
            }
          ],
          "Id": "8206a4b7-945c-4170-a413-5f4700000000"
        }
      ],
      "RowSpan": 2
    },
    ...

Row Variable Headings
"""""""""""""""""""""""""

Under ``Body.Sections`` you'll find the ``SubHeader`` and ``RowHeader``
objects to define the row variables and row values included in the crosstab,
respectively.

In the example below, the ``SubHeader`` object lists a single row for the row
variable label *Age*. The corresponding values for the row variable are then
listed under ``RowHeader.Rows``.

:ref:`Variable-level Calculations <variable_level_calculations>` are listed in
rows with type ``CalculationHeadingCell`` with each Variable-level Calculation
in a separate row. This differs from
:ref:`Value-level Calculations <value_level_calculations>` where computed values
are grouped within cells within a row. These rows are noted with type
``ValueHeadingCell``.

The snippet below exhibits three rows of calculations: The first row is a
Variable-level Calculation for ``SampleSize``. The other rows are for *Age*
values *Under 18* and *18-24*. Each will display two calculations in every cell,
"``Count``" and "``Frequency``":

  .. code-block:: javascript
    :linenos:

    ... etc. ...,
    "Body": {
      "Sections": [
        {
          "RowDesignIndex": 0,
          "SubHeader": [
            {
              "Rows": [
                {
                  "Cells": [
                    { "$type": "SectionHeadingCell", "Text": "Age", "ColumnSpan": 4 }
                  ]
                }
              ]
            }
          ],
          "RowHeader": {
            "Rows": [
              {
                "Cells": [
                  {
                    "$type": "CalculationHeadingCell",
                    "Calculation": "SampleSize",
                    "AndOthers": false
                  }
                ]
              },
              {
                "Cells": [
                  {
                    "$type": "ValueHeadingCell",
                    "Text": "Under 18",
                    "Calculations": [ "Count", "Frequency" ],
                    "DisplayCalculations": false
                  }
                ]
              },
              {
                "Cells": [
                  {
                    "$type": "ValueHeadingCell",
                    "Text": "18-24",
                    "Calculations": [ "Count", "Frequency" ],
                    "DisplayCalculations": false
                  }
                ]
              },
              ... etc ...
            ]
          }
        },
        ... etc. ...
      ]
    }

Calculations
""""""""""""""""

Under ``Body.Sections`` you'll also find the ``Groups`` object that lists the
calculation output for the crosstab. ``Groups`` is a collection of ordered
``Rows`` that correspond directly to the listing of ``Rows`` found in
``Body.Sections.RowHeader.Rows``.

Each ``Row`` in ``Groups`` is a collection of ``Cells`` that contain the
crosstab's calculated values. ``Cells`` are listed in order according to the
column headings in ``Header.Sections.Rows.Cells``. ``Cells`` can also contain
multiple values when they correspond to
:ref:`Value-level Calculations <value_level_calculations>`.

The JSON snippet below corresponds to the first two rows of the
**Simple Crosstab** example. These two rows are a
:ref:`Variable-level Calculation <variable_level_calculations>` and a
:ref:`Value-level Calculation <value_level_calculations>`, the latter having
values for "``Count``" and "``Frequency``".

Using the layout defined for row headings, ``Body.Groups.Rows[0]`` corresponds
to *Age – Sample Size* and ``Body.Groups.Rows[1]`` corresponds to
*Age – Under 18*.

Within each row there are three ``Cells`` the correspond in order with the
column headings, e.g.: ``Cells[0]`` related to "*Total*", ``Cells[1]`` related
to "*Male*", and ``Cells[2]`` related to "*Female*".

  .. image:: _static/working_with_crosstab_results_04.png
    :alt: Simple Crosstab Snippet
    :align: center

  .. code-block:: javascript
    :linenos:

    ... etc ...,
    "Body": {
        <snip>
        "Groups": [
          {
            "Rows": [
              {
                "Cells": [
                  { "Values": [ [ 617.000000000006, "sampleSize" ] ],
                    "SmallCounts": { "Item1": 0, "Item2": 0, "Item3": "None" },
                    "SmallSampleSize": { "Item1": 0, "Item2": 0 }
                  },
                  { "Values": [ [ 308.5000000000003, "sampleSize" ] ],
                    "SmallCounts": { "Item1": 0, "Item2": 0, "Item3": "None" },
                    "SmallSampleSize": { "Item1": 0, "Item2": 0 }
                  },
                  { "Values": [ [ 308.49999999999983, "sampleSize" ] ],
                    "SmallCounts": { "Item1": 0, "Item2": 0, "Item3": "None" },
                    "SmallSampleSize": { "Item1": 0, "Item2": 0 }
                  }
                ]
              },
              {
                "Cells": [
                  { "Values": [
                      [ 100.69030776571644, "count" ],
                      [ 0.16319336752952263, "frequency" ]
                    ],
                    "SmallCounts": { "Item1": 0, "Item2": 0, "Item3": "None" },
                    "SmallSampleSize": { "Item1": 0, "Item2": 0 }
                  },
                  { "Values": [
                      [ 31.96506024096383, "count" ],
                      [0.10361445783132513,"frequency"]
                    ],
                    <snip>
                  },
                  {
                    "Values": [
                      [ 68.72524752475242, "count" ],
                      [ 0.22277227722772272, "frequency" ]
                    ],
                    <snip>
                  }
                ]
              },
              <snip>
          ]
    }