
.. _uploading_datasets:

Loading Datasets into the Platform
-------------------------------------

One of the most common operations that people do using the
:term:`Dynata Reporting & Analytics API` is to programmatically upload a new
:term:`dataset` to the platform. In general terms, the platform accepts data
in either SPSS, CSV, or Excel format. To upload the dataset, you would use the
:class:`Datasets <marketsight.application_api.Datasets>` resource:

.. tabs::

   .. tab:: Loading an SPSS File

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

As you can see in the code snippets above, the process of uplaoding a dataset is
very straightforward:

  #. You prepare the object model which says where you intend the dataset to "live"
     (i.e. what account the dataset should be added to, and what folder path the
     dataset should be placed in)
  #. You create the dataset, supplying an optional
     :term:`Dataset Labeling Workbook`. This process uploads the dataset, and
     is an asynchronous :term:`Job`.
  #. You wait until that asynchronous job is complete, and can then interact
     further with the dataset the way you would normally.

----------------------------------------

.. _appending_data:

Appending Data to an Existing Dataset
-----------------------------------------

Often when you have an existing :term:`Dataset`, you may need to add additional
data. That might meaning adding new respondent records, adding new
:term:`variables <Variable>`, adding an entire new wave of data, or any
combination of these additions.

Appending data using the reporting & analytics API is very similar to
:ref:`uploading datasets <uploading_datasets>` in the first place, though it
also requires the configuration of instructions on how to handle mismatches
between the old and new data:

.. tabs::

  .. tab:: New Wave

    Assuming that we have some initial dataset:

    .. image:: _static/working_with_dataset_appending_01.png
       :alt: Initial Data
       :align: center


    and your wish to append some new data:

    .. image:: _static/working_with_dataset_appending_02.png
       :alt: Initial Data
       :align: center


    Same time you wish to separate `old` and `new` data by the new variable called `Wave Variable` in the next way:

    .. image:: _static/working_with_dataset_appending_03.png
       :alt: Result
       :align: center


    so:

    .. code-block:: python
      :linenos:

      import tempfile as tmp
      import time

      # transform data from 1st picture as some *.csv file and
      # upload it to MarketSight as initial DataSet
      with tmp.NamedTemporaryFile(prefix="demo1", suffix=".csv") as f:
            f.write(
                "\n".join(
                    [
                        "code1,code2",
                        "1,a",
                        "2,b",
                    ]
                ).encode()
            )
            f.seek(0)

            job = api.Datasets.create(
                account_id=account_id,
                model={"folderPath": ["Demo", "Datasets.append", time.time()]},
                data=f,
            )
            dataset_id_1 = job.dataset.id

        # wait until MarketSight will finish processing the data
        while api.Datasets.retrieve(dataset_id=dataset_id_1).status != "ready":
            time.sleep(2)

        # Will save data from 2nd pic into csv file and append it
        # to first one with `Datasets.append` method

        with tmp.NamedTemporaryFile(prefix="demo_ds2", suffix=".csv") as f:
            f.write(
                "\n".join(
                    [
                        "code1,code2",
                        "3,c",
                        "4,d",
                    ]
                ).encode()
            )
            f.seek(0)

            model = {
                "AppendRespondents": {
                    "type": "respondents",
                    "NewDatasetName": f"ds1 + ds2 {time.time()}",
                    "NewDatasetDescription": f"ds1 + ds2 {time.time()}",
                    "AppendToOriginalDataset": False,
                    "Wave": {
                        "AddNewWave": True,
                        "AppendToExistingWave": False,
                        "WaveMigrationOption": "includeExistingAndNew",
                        "FirstWaveName": "Wave 1",
                        "NewWaveName": "Wave 2",
                        "WaveVariableName": "Wave Variable",
                        "AppendToExistingWaveValue": "NaN",
                    },
                    "ExcludeMissingValues": False,
                    "MatchedVariables": [
                        {"OriginalVariable": "code1", "AppendedVariable": "code1"},
                        {"OriginalVariable": "code2", "AppendedVariable": "code2"},
                    ],
                }
            }

            _async2 = api.Datasets.append(
                account_id=account_id,
                dataset_id=dataset_id_1,
                model=model,
                data=f,
            )
        # Wait until Job will be finished
        job = api.helper.wait_until(
            fnc=api.Jobs.retrieve,
            fnc_kwargs={"job_id": _async2.job.id},
            conditions={"result": "succeeded"},
            sleep_time=30,
            max_attempts=5,
        )


  .. tab:: New Respondent Records

      Assuming that we have some initial dataset:

      .. image:: _static/working_with_dataset_appending_01.png
         :alt: Initial Data
         :align: center


      and your wish to append some new data:

      .. image:: _static/working_with_dataset_appending_02.png
         :alt: Append Data
         :align: center


      Same time you wish merge the both DataSets without separating them by the new variable:

      .. image:: _static/working_with_dataset_appending_06.png
         :alt: Result of data appending. (without `Waves`)
         :align: center

      python code for it:


     .. code-block:: python
        :linenos:

        import tempfile as tmp
        import time
        account_id = "<YOUR ACCOUNT ID HERE>"

        with tmp.NamedTemporaryFile(prefix="demo1", suffix=".csv") as f:
            f.write("\n".join([
                "code1,code2", "1,a", "2,b"]).encode())
            f.seek(0)

            job = api.Datasets.create(
                account_id=account_id,
                model={"folderPath": ["Demo", "Datasets.append", time.time()]},
                data=f,
            )
            dataset_id_1 = job.dataset.id

        # wait until MarketSight will finish processing the data
        while api.Datasets.retrieve(dataset_id=dataset_id_1).status != "ready":
            time.sleep(2)

        with tmp.NamedTemporaryFile(prefix="demo_ds2", suffix=".csv") as f:
            f.write("\n".join([
                "code1,code2",
                "3,c",
                "4,d"
            ]).encode())
            f.seek(0)

            # in model we use next assignment
            # "Wave": {"addNewWave": False}
            # to not create the new `Wave`

            model = {
                "AppendRespondents": {
                    "type": "respondents",
                    "NewDatasetName": f"ds1 + ds2 {time.time()}",
                    "NewDatasetDescription": f"ds1 + ds2 {time.time()}",
                    "AppendToOriginalDataset": False,
                    "ExcludeMissingValues": False,
                    "Wave": {"addNewWave": False},
                    "MatchedVariables": [
                        {"OriginalVariable": "code1", "AppendedVariable": "code1"},
                        {"OriginalVariable": "code2", "AppendedVariable": "code2"},
                    ],
                }
            }

            _async2 = api.Datasets.append(
                account_id=account_id,
                dataset_id=dataset_id_1,
                model=model,
                data=f
            )

        # Wait until Job will be finished
        job = api.helper.wait_until(
            fnc=api.Jobs.retrieve,
            fnc_kwargs={"job_id": _async2.job.id},
            conditions={"result": "succeeded"},
            sleep_time=5,
            max_attempts=30,
        )


  .. tab:: New Variables

     Assuming that we have some initial dataset:

    .. image:: _static/working_with_dataset_appending_01.png
       :alt: Initial Data
       :align: center


    and you wish to append new variable `code3` from another one:

    .. image:: _static/working_with_dataset_appending_04.png
       :alt: Initial Data
       :align: center



    Same time all new respondent data that not present in first dataset should be ignored.

    .. note::
        Last respondent in appended file will be ignored cause no such respondent is in the first (initial) dataset.
        In case if you wish to append everything (Variables & respondets) you should run `Datasets.append` twice:

        * append respondents;
        * append variables.

    As result we wish to get:

    .. image:: _static/working_with_dataset_appending_05.png
       :alt: Initial Data
       :align: center

    python code for it:

    .. code-block:: python
      :linenos:

        import tempfile as tmp
        import time

        with open("ds1.csv", "w") as f:
            # prepare csv file from 1st pic.
            f.write("\n".join([
                "code1,code2",
                "1,a",
                "2,b"
            ]))

        # uploading prepared ds1.csv
        _async1 = api.Datasets.create(
            account_id=account_id,
            model={"folderPath": ["Demo", "Datasets.append", time.time()]},
            data=open("ds1.csv", 'rb'),
        )
        dataset_id_1 = _async1.dataset.id

        # wait until MarketSight will finish processing the data
        while api.Datasets.retrieve(dataset_id=dataset_id_1).status != "ready":
            time.sleep(5)

        print(f"ds1_id={dataset_id_1}")

      # creating temporary csv file from 2nd pic.
      with open("ds3.csv", "w") as f:
        f.write("\n".join([
            "code1,code2,code3",
            "1,a,x",
            "2,b,x",
            "5,e,y",
        ]))

      model = {
        "AppendVariables": {
            "type": "variables",
            "appendUnmatchedRespondents": False,
            "codesToJoinOn": ["code1", "code2"],
            "variablesToRecode": [],
            "excludedVariables": [
                {
                    "Variable": "code1",
                    "FromOriginalDataset": False
                },
                {
                    "Variable": "code2",
                    "FromOriginalDataset": False
                }
            ],
            "NewDatasetName": f"ds1 + ds3 {time.time()}",
            "NewDatasetDescription": f"ds1 + ds3 {time.time()}",
            "AppendToOriginalDataset": False,
            "wave": {},
            "excludeMissingValues": False
        }
      }

      # appending new data to initial data
      _async2 = api.Datasets.append(
          account_id=account_id,
          dataset_id=dataset_id_1,
          model=model,
          data=open("ds3.csv", "rb")
      )

      # wait until `append job` will be finished
      while True:
          job = api.Jobs.retrieve(job_id=_async2.job.id)
          time.sleep(10)
          if job.result == "succeeded":
             break


    When the code above will successfully apply we can check the result of appending new variable
    throught the MarketSight DataView section. (Select newly create dataset and add all it variables into DataView):

    .. image:: _static/working_with_dataset_appending_07.png
       :alt: Result
       :align: center



------------------------

.. _working_with_variables:

Working with Variables
----------------------------

A big part of managing your datasets is working with :term:`Variables <Variable>`.
Typical operations include:

  * categorizing variables to make it easier to use them in the platform's
    user interface
  * modifying the human-readable labels that are displayed for particular
    variables
  * creating (or modifying)
    :term:`User-defined Variables <User-defined Variable>`

For any one variable, you can perform these operations using the
:class:`Variables <marketsight.application_api.Variables>` resource. However,
you can also perform these operations in bulk using the
:term:`Dataset Labeling Workbook`:

.. _using_the_variables_resource:

Using the :class:`Variables <marketsight.application_api.Variables>` Resource
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can retrieve the variables within a particular dataset easily using:

.. code-block:: python
  :linenos:

  variables = api.Variables.retrieve(dataset_id = 'MY DATASET ID')

Frequent operations include:

  #. Categorizing the Variable. You can do this using:

     .. code-block:: python
       :linenos:

       # Set the Dataset ID of the dataset whose variable you wish to modify.
       dataset_id = 'MY DATASET ID'

       # Set the ID of the variable that you wish to modify.
       variable_code = 'MY VARIABLE_ID'

       # Create the payload that provides instructions on how to modify the
       # variable.
       variable_model = {
           'category': 'My New Category'
       }

       # Modify the Variable's category.
       job = api.Variables.modify(dataset_id = dataset_id,
                                  variable_code = variable_code,
                                  model = variable_model)

  #. Retrieve variable labels. You can do this using:

     .. code-block:: python
       :linenos:

       # Retrieve the Variable Values. This will return a collection of
       # VariableValue dicts.
       variable_values = api.Variables.get_values(dataset_id = 'MY DATASET ID',
                                                  variable_code = 'MY VARIABLE CODE')

  #. Update variable labels. You can do this using:

     .. code-block:: python
       :linenos:

       # Update the value code 1 to have a new label "Updated Label"
       async_job = api.Variables.put_values(dataset_id = 'MY DATASET ID',
                                            variable_code = 'MY VARIABLE CODE',
                                            value = {
                                                'code': 1,
                                                'value': 'Updated Label'
                                            })

  #. Create a new :term:`Regrouping Variable`.

  Suppose we have a data set with the results of the English exams of a random group of people:

     .. image:: _static/working_with_regrouping_variable_01.png
       :alt: Initial Data
       :align: center

  Suppose that we know that scoring was done according to Cambridge English Scale:

     .. image:: _static/working_with_regrouping_variable_02.png
       :alt: Initial Data
       :align: center


    Let's add a new  :term:`Regrouping Variable`  with code `ENGLISH_LEVEL` that allows us to group people according
    to more general attributes of language proficiency:

  .. code-block:: python
     :linenos:

        import tempfile as tmp
        import time

        GENERAL_LEVELS = {
            "pre A1": [75, 100],
            "A1": [101, 120],
            "A2": [121, 140],
            "B1": [141, 160],
            "B2": [161, 180],
            "C1": [181, 200],
            "C2": [201, 230],
        }

        # create  csv file with the raw data and upload it as DataSet to MarketSight
        with tmp.NamedTemporaryFile(prefix="demo_data", suffix=".csv") as f:
            f.write("\n".join(
                    [
                        "first_name,last_name,english_score",
                        "Kay,Trenaman,171",
                        "Sawyere,Freeland,227",
                        "Rafaellle,Milnes,211",
                        "Allegra,McFarland,219",
                        "Chase,Ziemke,182",
                        "Revkah,Buglass,154",
                        "Benedick,Shekle,93",
                        "Cherri,Waberer,221",
                        "Ann-marie,Lowdwell,81",
                        "Daffi,Escudier,113",
                        "Angie,Ryce,132",
                        "Tori,Risbridger,131",
                        "Theodore,Macklin,81",
                        "Agna,Milsom,168",
                        "Jay,Testo,169",
                        "Babbette,Stockhill,203",
                        "Lazar,Sawkins,87",
                        "Dulcine,Longfield,136",
                        "Delphinia,Hincham,141",
                        "Alexis,Eykel,104",
                        "Aksel,Mucillo,199",
                        "Kain,Dunkerley,140",
                        "Benita,Beresford,116",
                        "Sylvester,Dioniso,143",
                        "Tadio,Rymer,84",
                        "Beniamino,Clarabut,205",
                        "Maddalena,Deviney,137",
                        "Joaquin,Unger,152",
                        "Lucy,Litzmann,207",
                        "Ford,Ary,225"
                    ]
                ).encode()
            )

            f.seek(0)

            job = api.Datasets.create(
                account_id=account_id,
                model={"folderPath": ["Demo English Score", time.time()]},
                data=f,
            )
            dataset_id = job.dataset.id

        # wait until MarketSight will finish processing the data
        while api.Datasets.retrieve(dataset_id=dataset_id).status != "ready":
            time.sleep(2)

        variable = api.Variables.create(
            dataset_id=dataset_id,
            model={
              "dataset": dataset_id,
              "code": "ENGLISH_LEVEL",
              "label": "English Level",
              "description": "",
              "kind": "udv",
              "type": "regrouping",
            }
        )
        values = []
        idx = 1  # we need idx` for creating unique value for every variable
        for level_name, _range in GENERAL_LEVELS.items():
            values.append({
                    "ConditionalValue": {
                      "type": "conditionalValue",
                      "expr": f"english_score >= {_range[0]} " + \
                              f" and english_score <= {_range[1]}",
                      "include_missing": False,
                      "all_other": False,
                      "label": level_name,
                      "code": idx
                    }
                  })
            idx += 1

        # main method for creating values
        api.Variables.put_values(
            dataset_id=dataset_id,
            variable_code=variable.code,
            values=values
        )

  the result is:
     .. image:: _static/working_with_regrouping_variable_03.png
       :alt: Data View with newly created `Regrouping variable`
       :align: center


.. _working_with_dataset_labeling_workbooks:

Working with the Dataset Labeling Workbook
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The :term:`Dataset Labeling Workbook` is a useful tool to perform bulk changes
to many :term:`variables <Variable>` in a given :term:`dataset <Dataset>`.

Because the Dataset Labeling Workbook supports a wide variety of reference
information regarding your dataset, and itself can contain very complicated
instructions for the definition of
:term:`User-defined Variables <User-defined Variable>`, it is rarely suitable
for programmatic creation.

Instead, most users:

  #. Start by uploading a :term:`Dataset` to the platform. See:
     :ref:`Loading Data into the Platform <uploading_datasets>`.
  #. Download that dataset's :term:`Dataset Labeling Workbook`. You can do this
     by calling:

     .. code-block:: python
       :linenos:

       # Download the Dataset Labeling Workbook contents.
       excel_bytes = api.Datasets.get_metadata(dataset_id = 'MY DATASET ID')

       # Write the Dataset Labeling Workbook to disk.
       with open('my-labeling-workbook.xlsx', 'wb') as file_:
           file_.write(excel_bytes)

  #. Modify that workbook, either manually in Excel or programmatically.
  #. Upload the updated Dataset Labeling Workbook to the platform. You can do
     this by calling:

     .. code-block:: python
       :linenos:

       job = api.Datasets.modify(dataset_id = 'MY DATASET ID',
                                 metadata = open('my-labeling-workbook.xlsx', 'rb'))

When editing a Dataset Labeling Workbook, all changes that you make must be made
on the "Labels" worksheet. You will find that this worksheet contains a simple
table where each row represents a single value within a
:term:`variable <Variable>`. That value is described using the following columns:

  * **Variable Code**. The unique identifier of the :term:`Variable` in the
    dataset.
  * **Variable Name**. The human-readable label that is applied ot the
    :term:`Variable`. If you need to clear a pre-existing value, you can enter
    a value of ``<clear>``.
  * **Value Code**. For :term:`Discrete Variables <Discrete Variable>`, this
    represents the numeric representation of the value in the raw data. For
    example, a variable "Gender" may have a value code of 1 for "Male".

    .. warning::

      This column should be blank for
      :term:`Continuous Variables <Continuous Variable>`,
      :term:`Date / Time Variables <Date / Time Variable>`,
      :term:`Text Variables <Text Variable>`, or
      :term:`Multiple Response Variables <Multiple Response Variable>`

  * **Value Label**. For :term:`Discrete Variables <Discrete Variable>`, this
    is the human-readable label that should be applied to the value code. For
    example, in a variable "Gender" with a value code of 1, its value label may
    be "Male".

    .. warning::

      This column should be blank for
      :term:`Continuous Variables <Continuous Variable>`,
      :term:`Date / Time Variables <Date / Time Variable>`,
      :term:`Text Variables <Text Variable>`, or
      :term:`Multiple Response Variables <Multiple Response Variable>`

  * **Category**. The category tag to apply to this variable. To clear the
    category and return a variable to the default "Uncategorized" setting, enter
    a value of ``<clear>``.
  * **Description/Question Text**. An optional set of metadata that is often
    used to provide a description of a given variable or to quote the survey
    question that produced the variable. Enter ``<clear>`` to clear any
    pre-existing settings.
  * **Missing Values**. For variables that may have missing values, you can
    either a) enter up to three value codes separated by commas, or b) indicate
    a numeric range of value codes (e.g. ``99-150``) that are classified as
    "missing" responses. Enter ``<clear>`` to clear this setting for a variable.
  * **UDV Type**. If defining a :term:`User-defined Variable`, this column will
    indicate which type it should be. Accepts:

      * ``RG`` for a :term:`Regrouping Variable`
      * ``CV`` for a :term:`Conditional Variable`
      * ``MV`` for a :term:`Mathematical Variable`
      * ``FV`` for a :term:`Filter Variable`
      * ``MRV`` for a :term:`Multiple Response Variable`

    .. tip::

      When creating a :term:`User-defined Variable`, the UDV type only needs to
      be provided once, on the first value row for the variable being created.

  * **UDV Definition**. If defining a :term:`User-defined Variable`, this column
    contains the logic syntax that governs how it is computed. For more details
    on the appropriate syntax, please see the "Examples" worksheet in a
    Dataset Labeling Workbook.
  * **Value to Count in MRV**. If defining a :term:`Multiple Response Variable`,
    this column indicates which value code should be included in the counts
    for the multiple response variable. May either be a single number
    corresponding to a single value code, or the value ``all`` (which will count
    all value codes in the underlying
    :term:`predecessor variables <Predecessor Variable>`).

---------------------

.. _exporting_datasets:

Exporting a Dataset
-----------------------

Whenever you export a dataset to SPSS format, it is expected to be an
asynchronous :term:`Job`. Below is a snippet of code that shows you you can
easily download the data:

  .. code-block:: python
     :linenos:

     dataset_id = "1bbe88ceb6214ab2a0dd4cb9c1cb788a"

     # Verify that the dataset has a "ready" status
     if api.Datasets.retrieve(dataset_id=dataset_id).status == 'ready':

         # Create an Asynchronous Job for the export.
         async_job = api.Datasets.export(dataset_id = dataset_id)

     # Use a Helper `wait_until` to wait until the exported SPSS file is ready
     # to download.
     byte_data = api.helper.wait_and_download_blob(job_id = async_job.job.id,
                                                   sleep_time = 5,
                                                   max_attempts = 100)

     # Download the SPSS file
     with open('exported-dataset.sav', 'wb') as fp:
         fp.write(byte_data)