Quickstart
==========

Run segmentation
-----------------

.. code-block:: bash

   segger segment \
       -i /path/to/your/ist/data/ \
       -o /path/to/save/outputs/

``-i`` is a standardized IST dataset directory (Xenium, CosMx, ...); ``-o`` is where outputs are
written. The main output is ``segger_segmentation.parquet``, the per-transcript assignment table (see :doc:`outputs`).

See all available options:

.. code-block:: bash

   segger segment --help

Export the segmentation to other formats
------------------------------------------------

.. code-block:: bash

   segger export \
       -s /path/to/save/outputs/segger_segmentation.parquet \
       -o /path/to/export/

Writes ``anndata`` and ``boundaries`` by default (add ``transcripts`` to also write the assigned
transcript table). See :doc:`outputs` for a description of each file, or:

.. code-block:: bash

   segger export --help

Use with SpatialData
----------------------

If you want a `SpatialData <https://spatialdata.scverse.org>`_ object instead of plain files
(e.g. for squidpy, SOPA, or napari-spatialdata), the recommended workflow is:

1. **Run segmentation** as above.
2. **Build a SpatialData object** from your raw data. For Xenium, use `spatialdata-io
   <https://spatialdata.scverse.org/projects/io/>`_:

   .. code-block:: python

      import spatialdata_io

      sdata = spatialdata_io.xenium("/path/to/your/ist/data/")
      sdata.write("/path/to/sdata.zarr")

3. **Add segger's elements** to that store:

   .. code-block:: bash

      segger export spatialdata \
          -s /path/to/save/outputs/segger_segmentation.parquet \
          --sdata /path/to/sdata.zarr

This edits ``sdata.zarr`` in place: it appends segger's per-transcript columns to the existing
``transcripts`` points element, and adds ``cell_boundaries_segger`` (shapes) and ``table_segger``
(table) elements. See :doc:`outputs` for details.
