:orphan:

:class:`Charts` are data visualizations that are produced from
:class:`Crosstabs` or :class:`Data_Views`.

As with :class:`Crosstabs` themselves, each :class:`Chart <Charts>` is built based on a
:ref:`ChartDefinition <ChartDefinition>` which provides the configuration
details for how a given visualization should be rendered.

:class:`Charts` can also be exported from the platform using the :meth:`Charts.export`
method, which can return a chart in either an image or PowerPoint format.