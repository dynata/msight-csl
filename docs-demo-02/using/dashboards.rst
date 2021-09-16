MarketSight's dashboard API makes the process of dashboard creation simple
and can allow to remove a lot of manual tasks.

:term:`Dashboards <Dashboards>` is a representative layer of Data Analysis object in MarketSight.

.. todo::

  Write up a detailed info about Dashboard:
  * What is it


--------------

.. _creating_dashboards:

Creating Dashboards
---------------------------

Above in this tutorial, we have already created objects
 that can be placed on the dashboard:
:term:`View <Data View>`, :term:`Crosstabs <Crosstab>`. Now it is time use them and place onto Dashboard.
In this example, we will perform several steps one after the other in a row, such as:
    *   in step 0, we will load data by creating a DataSet
    *   in step 1, we create a Crosstab on the basis of the created DataSet
    *   in step 2 we create a DataView on the basis of created DataSet
    *   in step 3, we'll create a Dashboard that contains Crosstab from step 1, DataView from step 2 and add a text block as a part of Dashboard

We should get something like:

.. image:: _static/working_with_dashboard_creation.png
  :alt: Created Crosstab
  :align: center


.. tabs::

  .. tab:: Step 0: Uploading DataSet

    .. code-block:: python
       :linenos:

           dataset_id = None

           with tmp.NamedTemporaryFile(
                                       prefix="demo_data_file",
                                       suffix=".csv") as f:
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
                            "Rubie,Wilson,Female,20,dog",
                            "Charlie,Watson,Male,19,cat",
                        ]
                    ).encode()
                )
                f.seek(0)

                job = api.Datasets.create(
                    account_id=account_id,
                    model={"folderPath": ["Demo", "Dashboard", time.time()]},
                    data=f,
                )
                dataset_id = job.dataset.id

  .. tab:: Step 1: Creating Crosstab

    .. code-block:: python
       :linenos:

        model = {
            "name": f"Demo CrossTab name",
            "description": f"Demo CrossTab description",
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

        crosstab = api.Crosstabs.create(dataset_id=dataset_id, model=model)

  .. tab:: Step 2: Creating DataView

    .. code-block:: python
       :linenos:

       data_view = api.Data_Views.create(
           dataset_id=dataset_id,
            model={
                "definition": {
                    "columns": {
                        "hideEmptyResponses": True,
                        "variables": api.Variables.list(
                            dataset_id=dataset_id
                        ).items,
                    }
                },
                "dataset": dataset_id,
                "name": f"DataView for {dataset_id} variant 2",
                "description": f"DataView for {dataset_id} variant 2",
            },
        )


  .. tab:: Step 3: Creating Dashboard with DataView, Crosstab and Text field

    .. code-block:: python
       :linenos:

        dashboard = api.Dashboards.create(
                model={
                    "account": account,
                    "name": f"Dashboard {dataset_id} {time.time()}",
                    "description": f"Dashboard {dataset_id} {time.time()} ",
                    "definition": {
                        "activeScripts": {"scripts": []},
                        "items": [
                            {
                                "content": {
                                    "DashboardDataViewContent": {
                                        "dataViewId": data_view.id,
                                        "type": "dataView",
                                        "layoutInfo": {
                                            "columnsWidths": [
                                                0.21441441441441442,
                                                0.21441441441441442,
                                                0.07567567567567568,
                                                0.04954954954954955,
                                                0.09549549549549549,
                                            ],
                                            "rowsHeights": [
                                                0.10460251046025104,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                                0.09623430962343096,
                                            ],
                                        },
                                    }
                                },
                                "type": "dataView",
                                "options": {
                                    "autoSize": True,
                                    "backgroundColor": "",
                                    "borderColor": "#AAAAAA",
                                    "borderThickness": 0,
                                    "hideScrollbars": False,
                                    "lockAspectRatio": False,
                                    "margin": 0,
                                    "position": {
                                        "width": {"value": 1110, "unit": "fixed"},
                                        "height": {"value": 260, "unit": "fixed"},
                                        "top": {"value": 330, "unit": "fixed"},
                                        "bottom": {"value": 0, "unit": "unset"},
                                        "left": {"value": 20, "unit": "fixed"},
                                        "right": {"value": 0, "unit": "unset"},
                                    },
                                    "tag": "",
                                },
                                "tabIndex": 0,
                                "orderInGroup": 0,
                                "object": "DashboardItem",
                            },
                            {
                                "content": {
                                    "DashboardCrosstabContent": {
                                        "type": "crosstab",
                                        "crosstabId": crosstab.id,
                                        "layoutInfo": {
                                            "columnsWidths": [
                                                0.292,
                                                0.108,
                                                0.136,
                                                0.106,
                                            ],
                                            "rowsHeights": [
                                                0.16267942583732056,
                                                0.11483253588516747,
                                                0.11483253588516747,
                                                0.18660287081339713,
                                                0.18660287081339713,
                                            ],
                                        },
                                    }
                                },
                                "type": "crosstab",
                                "options": {
                                    "autoSize": True,
                                    "backgroundColor": "",
                                    "borderColor": "#AAAAAA",
                                    "borderThickness": 0,
                                    "hideScrollbars": False,
                                    "lockAspectRatio": False,
                                    "margin": 0,
                                    "position": {
                                        "width": {"value": 500, "unit": "fixed"},
                                        "height": {"value": 230, "unit": "fixed"},
                                        "top": {"value": 60, "unit": "fixed"},
                                        "bottom": {"value": 0, "unit": "unset"},
                                        "left": {"value": 610, "unit": "fixed"},
                                        "right": {"value": 0, "unit": "unset"},
                                    },
                                    "tag": "",
                                },
                                "tabIndex": 0,
                                "orderInGroup": 1,
                                "object": "DashboardItem",
                            },
                            {
                                "content": {
                                    "DashboardRichTextContext": {
                                        "xmlMarkup": '<span class="table-wrapper stop-indicator"><span class="align-wrapper top"><div><b><span style="font-size: 20px;">New text item: Demo text</span></b></div></span></span>',
                                        "type": "rich",
                                    }
                                },
                                "type": "rich",
                                "options": {
                                    "autoSize": True,
                                    "backgroundColor": "",
                                    "borderColor": "#AAAAAA",
                                    "borderThickness": 0,
                                    "hideScrollbars": True,
                                    "lockAspectRatio": False,
                                    "margin": 0,
                                    "position": {
                                        "width": {"value": 510, "unit": "fixed"},
                                        "height": {"value": 30, "unit": "fixed"},
                                        "top": {"value": 50, "unit": "fixed"},
                                        "bottom": {"value": 0, "unit": "unset"},
                                        "left": {"value": 20, "unit": "fixed"},
                                        "right": {"value": 0, "unit": "unset"},
                                    },
                                    "tag": "",
                                },
                                "tabIndex": 0,
                                "orderInGroup": 2,
                                "object": "DashboardItem",
                            },
                        ],
                        "design": {
                            "position": {
                                "width": {"value": 1150, "unit": "fixed"},
                                "height": {"value": 650, "unit": "fixed"},
                                "top": {"value": 0, "unit": "unset"},
                                "bottom": {"value": 0, "unit": "unset"},
                                "left": {"value": 0, "unit": "unset"},
                                "right": {"value": 0, "unit": "unset"},
                            },
                            "width": 1150,
                            "height": 650,
                            "backgroundColor": "#FFFFFF",
                            "borderColor": "#AAAAAA",
                            "showZoomInButtons": True,
                            "showExportButtons": False,
                            "borderThickness": 1,
                            "tabsBackgroundColor": "#FFFFFF",
                            "tabsHoverBackgroundColor": "#CCCCCC",
                            "tabsFontColor": "#333333",
                            "tabsBorderColor": "#949494",
                            "tabsSelectedBackgroundColor": "#CCCCCC",
                            "tabsBorderThickness": 1,
                            "tabsPosition": "left",
                            "tabsAreaWidth": 80,
                            "tabsEnabled": False,
                            "autoApply": True,
                            "variableDisplayMode": "name",
                            "logosEdited": False,
                            "allowDimmingOnDragOver": False,
                            "allowDimmingOnHover": False,
                            "filtersMigrated": True,
                            "tabsLazyLoadingEnabled": False,
                            "tabs": [{"name": "Page 1", "index": 0, "isHidden": False}],
                            "object": "DashboardDesign",
                        },
                        "interactivity": {
                            "filters": {
                                "controlTypeLocked": True,
                                "panelPosition": "left",
                                "panelExpanded": True,
                                "variablesExpanded": True,
                            },
                            "design": {
                                "label": "Filter",
                                "currentSelectionControl": "#FFFFFF",
                                "isFilterControlsUnlocked": False,
                                "panelAccentColor": "#456797",
                                "panelTheme": "light",
                                "showDashboardLevelFilterControls": False,
                                "showFilterControls": True,
                                "showRowVariableControls": True,
                                "showColumnVariableControls": True,
                                "controlsExpanded": True,
                                "panelPosition": "left",
                            },
                        },
                        "object": "DashboardDefinition",
                    },
                }
            )


Full python code can be found
`here <https://github.com/dynata/msight-csl/blob/develop-csl-versioning/tests/test_example.py#L316>`_.


Redirecting Dashboards
---------------------------


.. tabs::

  .. tab:: Step By Step

     Suppose that we have some Dashboard that contains such elements as:
       * :term: Crosstab
       * :term: DataView
       * :term: Charts
       * Other visual elements like "images, link, text" that are available inside the Dashboard.

     Let's imaging the situation that you get some "fresh" data and with to update your Dashboard.
     We can do in with method Dashboards.append

     Let's check how to do it.

     Suppose that the initial DataSet has next view:

        .. image:: _static/working_with_dashboard_redirecting_01.png
          :alt: Initial DataSet for test Dashboard
          :align: center

     Creating it in MarketSight:

     .. code-block:: python
       :linenos:

           import time
           # account_id = <YOU ACCOUNT ID HERE>

           with open("ds1.csv", "w") as f:
               # prepare csv file from 1st pic.
               f.write("\n".join([
                   "code1,code2",
                   "1,a",
                   "2,b"
               ]))

           # uploading ds1.csv file as dataset
           _async = api.Datasets.create(
               account_id=account_id,
               model={
                   "folderPath": ["Demo", "Dashboard.redirect", time.time()]
               },
               data=open("ds1.csv", 'rb')
           )

           # Wait until dataset will be processed by MarketSight
           while True:
               dataset1 = api.Datasets.retrieve(dataset_id=_async.dataset.id)
               if dataset1.status == "ready":
                   break
               time.sleep(5)


     Creating simple DataView element for this DataSet:

     .. code-block:: python
       :linenos:

       data_view_1 = api.Data_Views.create(model={
               "definition": {
                   "columns": {
                       "hideEmptyResponses": True,
                       "variables": [
                           {"code": "code1", "displayName": "code1"},
                           {"code": "code2", "displayName": "code2"},
                       ],
                   },
               },
               "dataset": dataset1.id,
               "name": "Data View For Dashboard",
       })

     Will create Dashboard and place just created `data_view_1` on it:

     .. code-block:: python
        :linenos:

         dashboard_definition = {
               "activeScripts": {"scripts": []},
               "items": [
                   {
                       "content": {
                           "DashboardDataViewContent": {
                               "dataViewId": data_view_1.id,
                               "type": "dataView",
                               "layoutInfo": {
                                   "columnsWidths": [
                                       0.11739130434782609,
                                       0.4304347826086956,
                                   ],
                                   "rowsHeights": [
                                       0.10460251046025104,
                                       0.09623430962343096,
                                       0.09623430962343096,
                                   ],
                               },
                           }
                       },
                       "type": "dataView",
                       "options": {
                           "autoSize": True,
                           "backgroundColor": "",
                           "borderColor": "#AAAAAA",
                           "borderThickness": 0,
                           "hideScrollbars": False,
                           "lockAspectRatio": False,
                           "margin": 0,
                           "position": {
                               "width": {"value": 460, "unit": "fixed"},
                               "height": {"value": 260, "unit": "fixed"},
                               "top": {"value": 10, "unit": "fixed"},
                               "bottom": {"value": 0, "unit": "unset"},
                               "left": {"value": 20, "unit": "fixed"},
                               "right": {"value": 0, "unit": "unset"},
                           },
                           "tag": "",
                       },
                       "tabIndex": 0,
                       "orderInGroup": 0,
                       "object": "DashboardItem",
                   }
               ],
               "design": {
                   "position": {
                       "width": {"value": 1150, "unit": "fixed"},
                       "height": {"value": 650, "unit": "fixed"},
                       "top": {"value": 0, "unit": "unset"},
                       "bottom": {"value": 0, "unit": "unset"},
                       "left": {"value": 0, "unit": "unset"},
                       "right": {"value": 0, "unit": "unset"},
                   },
                   "width": 1150,
                   "height": 650,
                   "backgroundColor": "#FFFFFF",
                   "borderColor": "#AAAAAA",
                   "showZoomInButtons": True,
                   "showExportButtons": False,
                   "borderThickness": 1,
                   "tabsBackgroundColor": "#FFFFFF",
                   "tabsHoverBackgroundColor": "#CCCCCC",
                   "tabsFontColor": "#333333",
                   "tabsBorderColor": "#949494",
                   "tabsSelectedBackgroundColor": "#CCCCCC",
                   "tabsBorderThickness": 1,
                   "tabsPosition": "left",
                   "tabsAreaWidth": 80,
                   "tabsEnabled": False,
                   "autoApply": True,
                   "variableDisplayMode": "name",
                   "logosEdited": False,
                   "allowDimmingOnDragOver": False,
                   "allowDimmingOnHover": False,
                   "filtersMigrated": True,
                   "tabsLazyLoadingEnabled": False,
                   "tabs": [{"name": "Page 1", "index": 0, "isHidden": False}],
                   "object": "DashboardDesign",
               },
               "interactivity": {
                   "filters": {
                       "controlTypeLocked": True,
                       "panelPosition": "left",
                       "panelExpanded": True,
                       "variablesExpanded": True,
                   },
                   "design": {
                       "label": "Filter",
                       "currentSelectionControl": "#FFFFFF",
                       "isFilterControlsUnlocked": False,
                       "panelAccentColor": "#456797",
                       "panelTheme": "light",
                       "showDashboardLevelFilterControls": False,
                       "showFilterControls": True,
                       "showRowVariableControls": True,
                       "showColumnVariableControls": True,
                       "controlsExpanded": True,
                       "panelPosition": "left",
                   },
               },
               "object": "DashboardDefinition",
         }

         dashboard_1 = api.Dashboards.create(
               account_id=account_id,
               model=api.models.DashboardIn(
                   account=account_id,
                   name=f"DashBoard with {data_view_1.name} {time.time()}",
                   description=f"csl.description {data_view_1.name} {time.time()}",
                   definition=dashboard_definition,
               ),
         )


     Now DashBoard should looks like:

     .. image:: _static/working_with_dashboard_redirection_01.png
        :alt: Dashboard that containing the DataView created above
        :align: center

     Supposing that new (fresh) data looks like:


       .. image:: _static/working_with_dashboard_redirecting_02.png
          :alt: Initial DataSet for test Dashboard
          :align: center

     Uploading it into MarketSight looks like:

     .. code-block:: python
        :linenos:

           # uploading ds2.csv file as DataSet
           with open("ds2.csv", "w") as f:
               # prepare csv file from 2nd pic.
               f.write("\n".join([
                   "code1,code2",
                   "3,c",
                   "4,d"
               ]))

           _async2 = api.Datasets.create(
               account_id=account_id,
               model={
                   "folderPath": ["Demo", "Dashboard.redirect", time.time()]
               },
               data=open("ds2.csv", 'rb')
           )

           # Wait until file ds2.csv  will be processed by MarketSight
           while True:
               dataset2 = api.Datasets.retrieve(dataset_id=_async2.dataset.id)
               if dataset2.status == "ready":
                   break
               time.sleep(5)


     .. note::

         For correct redirection of Dashboard to newly uploaded ds2 dataset,
         we should recreate all object(s) for ds2 that we used in Dashboard for initial dataset (ds1).

     .. warning::

         The `name` of each objects created for ds2 should be the same as they named in initial Dashboard.

     In out tutorial we have only one object (DataView) on Dashboard so we need create it for ds2
     with the same name

      .. code-block:: python
         :linenos:

           # create DataView for dataset_2 with the same `name` param
           api.Data_Views.create(model={
               "definition": {
                   "columns": {
                       "hideEmptyResponses": True,
                       "variables": [
                           {"code": "code1", "displayName": "code1"},
                           {"code": "code2", "displayName": "code2"},
                       ],
                   },
               },
               "dataset": dataset2.id,
               "name": "Data View For Dashboard",
           })

       So now we can redirect Dashboard from ds1 to ds2 data:


      .. code-block:: python
         :linenos:

         _async_redirect = api.Dashboards.redirect(
               account_id=account,
               dashboard_id=dashboard_1.id,
               model={"origin": dataset1.id, "destination": dataset2.id},
          )
         # wait until job will be complited
           api.helper.wait_until(
               fnc=api.Jobs.retrieve,
               fnc_kwargs={"job_id": _async.job.id},
               conditions={"result": "succeeded"},
         )

      Now refresh the initial dashboard it should looks like:

        .. image:: _static/working_with_dashboard_redirection_02.png
          :alt: Redirected Dashboard
          :align: center

  .. tab:: Raw python code

    .. code-block:: python
       :linenos:

       import time
       # account_id = <YOU ACCOUNT ID HERE>

       with open("ds1.csv", "w") as f:
        # prepare csv file from 1st pic.
        f.write("\n".join([
            "code1,code2",
            "1,a",
            "2,b"
        ]))

       # uploading ds1.csv file as dataset
       _async = api.Datasets.create(
        account_id=account_id,
        model={
            "folderPath": ["Demo", "Dashboard.redirect", time.time()]
        },
        data=open("ds1.csv", 'rb')
       )

       # Wait until dataset will be processed by MarketSight
       while True:
        dataset1 = api.Datasets.retrieve(dataset_id=_async.dataset.id)
        if dataset1.status == "ready":
            break
        time.sleep(5)

       # define model for creating DataView for
       data_view_model = {
        "definition": {
            "columns": {
                "hideEmptyResponses": True,
                "variables": [
                    {"code": "code1", "displayName": "code1"},
                    {"code": "code2", "displayName": "code2"},
                ],
            },
        },
        "dataset": dataset1.id,
        "name": "Data View For Dashboard",
       }
       # creating DataView for dataset1
       data_view_1 = api.Data_Views.create(model={
        "definition": {
            "columns": {
                "hideEmptyResponses": True,
                "variables": [
                    {"code": "code1", "displayName": "code1"},
                    {"code": "code2", "displayName": "code2"},
                ],
            },
        },
        "dataset": dataset1.id,
        "name": "Data View For Dashboard",
       })

       dashboard_definition = {
        "activeScripts": {"scripts": []},
        "items": [
            {
                "content": {
                    "DashboardDataViewContent": {
                        "dataViewId": data_view_1.id,
                        "type": "dataView",
                        "layoutInfo": {
                            "columnsWidths": [
                                0.11739130434782609,
                                0.4304347826086956,
                            ],
                            "rowsHeights": [
                                0.10460251046025104,
                                0.09623430962343096,
                                0.09623430962343096,
                            ],
                        },
                    }
                },
                "type": "dataView",
                "options": {
                    "autoSize": True,
                    "backgroundColor": "",
                    "borderColor": "#AAAAAA",
                    "borderThickness": 0,
                    "hideScrollbars": False,
                    "lockAspectRatio": False,
                    "margin": 0,
                    "position": {
                        "width": {"value": 460, "unit": "fixed"},
                        "height": {"value": 260, "unit": "fixed"},
                        "top": {"value": 10, "unit": "fixed"},
                        "bottom": {"value": 0, "unit": "unset"},
                        "left": {"value": 20, "unit": "fixed"},
                        "right": {"value": 0, "unit": "unset"},
                    },
                    "tag": "",
                },
                "tabIndex": 0,
                "orderInGroup": 0,
                "object": "DashboardItem",
            }
        ],
        "design": {
            "position": {
                "width": {"value": 1150, "unit": "fixed"},
                "height": {"value": 650, "unit": "fixed"},
                "top": {"value": 0, "unit": "unset"},
                "bottom": {"value": 0, "unit": "unset"},
                "left": {"value": 0, "unit": "unset"},
                "right": {"value": 0, "unit": "unset"},
            },
            "width": 1150,
            "height": 650,
            "backgroundColor": "#FFFFFF",
            "borderColor": "#AAAAAA",
            "showZoomInButtons": True,
            "showExportButtons": False,
            "borderThickness": 1,
            "tabsBackgroundColor": "#FFFFFF",
            "tabsHoverBackgroundColor": "#CCCCCC",
            "tabsFontColor": "#333333",
            "tabsBorderColor": "#949494",
            "tabsSelectedBackgroundColor": "#CCCCCC",
            "tabsBorderThickness": 1,
            "tabsPosition": "left",
            "tabsAreaWidth": 80,
            "tabsEnabled": False,
            "autoApply": True,
            "variableDisplayMode": "name",
            "logosEdited": False,
            "allowDimmingOnDragOver": False,
            "allowDimmingOnHover": False,
            "filtersMigrated": True,
            "tabsLazyLoadingEnabled": False,
            "tabs": [{"name": "Page 1", "index": 0, "isHidden": False}],
            "object": "DashboardDesign",
        },
        "interactivity": {
            "filters": {
                "controlTypeLocked": True,
                "panelPosition": "left",
                "panelExpanded": True,
                "variablesExpanded": True,
            },
            "design": {
                "label": "Filter",
                "currentSelectionControl": "#FFFFFF",
                "isFilterControlsUnlocked": False,
                "panelAccentColor": "#456797",
                "panelTheme": "light",
                "showDashboardLevelFilterControls": False,
                "showFilterControls": True,
                "showRowVariableControls": True,
                "showColumnVariableControls": True,
                "controlsExpanded": True,
                "panelPosition": "left",
            },
        },
        "object": "DashboardDefinition",
       }

       dashboard_1 = api.Dashboards.create(
        account_id=account_id,
        model=api.models.DashboardIn(
            account=account_id,
            name=f"DashBoard with {data_view_1.name} {time.time()}",
            description=f"csl.description {data_view_1.name} {time.time()}",
            definition=dashboard_definition,
        ),
       )

       # uploading ds2.csv file as DataSet
       with open("ds2.csv", "w") as f:
        # prepare csv file from 2nd pic.
        f.write("\n".join([
            "code1,code2",
            "3,c",
            "4,d"
        ]))

       _async2 = api.Datasets.create(
        account_id=account_id,
        model={
            "folderPath": ["Demo", "Dashboard.redirect", time.time()]
        },
        data=open("ds2.csv", 'rb')
       )

       # Wait until file ds2.csv  will be processed by MarketSight
       while True:
        dataset2 = api.Datasets.retrieve(dataset_id=_async2.dataset.id)
        if dataset2.status == "ready":
            break
        time.sleep(5)

       # create DataView for dataset_2 with the same `name` param
       api.Data_Views.create(model={
        "definition": {
            "columns": {
                "hideEmptyResponses": True,
                "variables": [
                    {"code": "code1", "displayName": "code1"},
                    {"code": "code2", "displayName": "code2"},
                ],
            },
        },
        "dataset": dataset2.id,
        "name": "Data View For Dashboard",
       })

       _async_redirect = api.Dashboards.redirect(
        account_id=account,
        dashboard_id=dashboard_1.id,
        model={"origin": dataset1.id, "destination": dataset2.id},
       )
       # wait until job will be complited
       api.helper.wait_until(
        fnc=api.Jobs.retrieve,
        fnc_kwargs={"job_id": _async.job.id},
        conditions={"result": "succeeded"},
       )




Injection Script
---------------------------

 * Advanced level

Injection Script - is the way to improve representative layer of  MarketSight
(such as  :term:`Dashboards <Dashboard>`, :term:`Crosstabs <Crosstab>`)
with the JavaScript/TypeScript code. It is a very powerful tool to customize the view of any visual object placed on
:term:`Crosstabs<Dashboard>`. Because the script provides access to low-level objects
used by the MarketSight, this section requires
a certain ability to extend the functionality of displayed objects using the JavaScript/TypeScript programming language.
You also need to know how to change the appearance of objects using CSS.


.. warning::

       JavaScript/TypeScript code that we will use for Dashboard customization
       can contain potentially dangerous code that MarketSight's backend can't prevent, so over the standard authorization
       with the access token, InjectionScript's API methods have additional asynchronous encryption
       of transmitted data.

Before we start changing the apiarance of Dashboard, we need properly activate and configure everything
to work correctly with the Injection Scripts. Sign in into MarketSight as account administrator and follow to
`Profile` -> `Scripting` menu. On the `Code Signing` tab press `Go to Key Generation ⟶` button and there
press `Generate a key pair`.



.. image:: _static/working_with_injection_script_generating_public_private_keys.png
  :alt: Generating Public/Private keys
  :align: center

You need copy both generated private and public keys into safe place.
After that, you should send to MarketSight system administrator the public key by email or by the messenger.
He should associate your public key with your account and he will send you back some unique ID-cocde that will
be associated with your public key.


The basic (theoretical) flow how updating/creating  methods work:
before sending some JavaScript/TypeScript code to MarketSight,
you should `sign` it with the private key that you have got from the step above. The result of signing is some
binary string. You should send 3 things to server:

  *  raw JavaScript/Typescript code;
  *  signed message;
  *  unique identity that you has received from MarketSight administrator on exchange for your public key.

On the MarketSight side the API application will find your public key by your unique identity and try
to verify you raw JavaScript/Typescript code. If verified message will be the same as your `signed message`
(2nd parameter you have sent) application will approve your request and apply changing.

For using the MarketSight API Client with the InjectionScript we need to install additional package:


    .. code-block:: bash

       pip install ecdsa

to verify that cryptographic key works correctly run next `python` code:


    .. code-block:: python
       :linenos:

       import ecdsa
       import base64

       # `private_id` is the copy/paste value that we have generated in the MarketSight backend
       private_id = "MIGHAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBG0wawIBAQQgh4WFXmNOHKgQDZIlz/6edvXi6mx172crgFp66izc7o6hRANCAATzbKUebaXF7Weh6ZPjmJGP0erP92qfLSfe+fa29aG/nGYbM1LM6MMe9DOAjGsCSHWRUdqLQXKXQSFcYns6mUQV"
       private_key = ecdsa.SigningKey.from_der(base64.b64decode(private_id.encode()))
       public_key = private_key.get_verifying_key()

       raw_message = "message".encode()  # should be in binary format
       # Signing
       signature = private_key.sign(raw_message)

       # Verifying
       if public_key.verify(signature, raw_message):
           print("`raw_message` is successfully signed")
       else:
           print("`raw_message` verification was failed")

.. note::

       We don't need restore public key on client side,
       cause we are having the private key and can restore public key from it in any time.
