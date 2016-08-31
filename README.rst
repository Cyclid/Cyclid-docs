####################
Cyclid Documentation
####################

This is the documentation for Cyclid, including the Cyclid API server,
Cyclid UI server, the Cyclid command line client, and documentation on how to
write Cyclid jobs.

The documentation is written in reStructuredText format, and we use
`Read the Docs <https://readthedocs.org/>`_ to host them.

If you just want to read the documentation, see
`docs.cyclid.io <http://docs.cyclid.io/en/latest/index.html>`_

If you want to contribute to the documentation, ensure you have Python ``pip``
installed and then run

.. code:: bash

  $ pip install -r requirements.txt

This will install the docutils & Sphinx packages which are used to generate
the documentation. To (re-)generate the documentation, run ``make html`` in the
``docs`` directory; the documentation will be built into the ``_build/html/``
directory. See `the Sphinx documentation <http://www.sphinx-doc.org/en/stable/contents.html>`_
for more details.

************
CONTRIBUTING
************

1. Fork it.
2. Create your feature branch (git checkout -b my-new-feature).
3. Commit your changes (git commit -am 'Add some feature').
4. Push to the branch (git push origin my-new-feature).
5. Create new Pull Request.
