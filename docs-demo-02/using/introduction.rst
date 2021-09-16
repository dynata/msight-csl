The **Dynata Reporting & Analytics** platform is built around the
`MarketSight <https://www.marketsight.com>`_ application, which Dynata acquired
with the Reimagine group of companies in 2019. It is currently utilized by over
6,000 people around the world to analyze, visualize, and communicate statistical
insights from survey data.

While the platform provides an easy-to-use user interface, there are times when
it makes sense to integrate with it programmatically. Examples of typical use
cases include when:

  * you want to automatically load :term:`datasets <Dataset>` into the platform
    for further analysis
  * you want to automatically extract data tables computed from your
    :term:`datasets <Dataset>`
  * you want to embed the MarketSight tool into your own application
  * and more!

To facilitate this, Dynata provides the :term:`Dynata Reporting & Analytics API`,
which is a RESTful API to allow you, your scripts, or your applications to
interact programmatically with the platform. You can find detailed documentation,
including example code snippets in multiple programming languages, for that
RESTful API here: https://api-documentation.marketsight.com

To make working with the API easier when programming in Python, we provide this
**MarketSight API Client** library. It wraps the functionality of the RESTful
API, and makes it easy for you to call the API without worrying about the
"scaffolding" of making direct HTTP requests against the API.