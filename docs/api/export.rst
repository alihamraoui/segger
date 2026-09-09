segger export
==============

Positional arguments select which elements to write: ``anndata``, ``transcripts``,
``boundaries``, ``spatialdata`` (default: ``anndata boundaries``). Run ``segger export --help``
for the authoritative list.

.. list-table::
   :header-rows: 1

   * - Flag
     - Default
     - Description
   * - ``-s, --segmentation-path``
     - *required*
     - Path to ``segger_segmentation.parquet``.
   * - ``-i, --source-path``
     - None
     - Source transcripts directory. Only needed for segger v0.2.0 outputs, which predate inline
       ``x``/``y``/``feature_name`` columns.
   * - ``-o, --output-directory``
     - None
     - Output directory. Required unless the only element being exported is ``spatialdata``.
   * - ``--sdata``
     - None
     - Existing SpatialData Zarr store to edit in place (required for the ``spatialdata``
       element).
   * - ``--sdata-transcripts-name``
     - transcripts
     - Existing points element in ``--sdata`` to append segger's columns to.
   * - ``--sdata-cell-boundaries-name``
     - cell_boundaries_segger
     - Shapes element segger's cell boundaries are written to.
   * - ``--sdata-table-name``
     - table_segger
     - Table element segger's AnnData is written to.
   * - ``--method``
     - delaunay
     - Cell-polygon method for boundaries (``delaunay`` or ``convex_hull``).
   * - ``--chaikin-iterations``
     - 0
     - Chaikin corner-cutting iterations to round boundaries (``0`` disables).
   * - ``--include-all-transcripts``
     - True
     - Keep every transcript in the segmentation output, not just the ones segger's ``filtered``
       column marks as kept.
   * - ``--min-counts``
     - 10
     - Minimum assigned transcripts a cell must have to be included (must be ``>= 3`` for
       ``spatialdata``, since boundaries need ``>= 3`` points).
