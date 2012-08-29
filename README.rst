Maintaining Your Sanity While Maintaining Your Open Source App
===================================================================

These are the materials used for my talk on managing an open source project. The
talk comes from my experience as a Python/Django developer and so the talk has
a Django slant.


Building the Slides
-------------------------------------------------

The slides are written in `reStructuredText <http://docutils.sourceforge.net/rst.html>`_
and built using `landslide <https://github.com/adamzap/landslide>`_. To build the
slides you can install the requirements and then call ``landslide``::

    pip install -r requirements.txt
    landslide -i slides.cfg

This generates the ``presentation.html`` slides.


License
-------------------------------------------------

These slides are available to use under the Creative Commons Attribution Non-Commercial
Share-Alike license. Here is a `summary. <http://creativecommons.org/licenses/by-nc-sa/3.0/>`_
The gist is that you can use or adapt these slides for in any non-commercial purpose 
as long as the original source is cited and the adapted works are available under 
a compatible license.
