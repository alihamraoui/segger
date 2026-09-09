Outputs
=======

``segger segment``
-------------------

``segger_segmentation.parquet``
   Per-transcript assignment table, indexed by ``row_index`` (aligns with the input transcripts).
   Columns: ``segger_cell_id``, ``segger_similarity``, ``similarity_threshold`` (per-gene cutoff),
   ``converged`` (whether the cutoff was computed directly for that gene or backfilled from the
   global median), ``x``, ``y``, ``feature_name``, and ``filtered`` (assigned, converged, and above
   threshold — the recommended keep mask).

   .. code-block:: python

      import polars as pl

      seg = pl.read_parquet("outputs/segger_segmentation.parquet")
      assigned = seg.filter(pl.col("filtered"))

``segger_anndata.h5ad``
   Cell x gene table (written only with ``--save-anndata``, the CLI default).

``debug/``
   Written only with ``--debug``: run params, tiles, predictions, and trainer checkpoint.

``segger export``
----------------------

``adata.h5ad``
   Cell x gene :class:`anndata.AnnData`. ``obs`` is indexed by cell ID with ``n_transcripts`` and
   (when boundaries are also exported) ``area``; centroids are in ``obsm["spatial"]``.

   .. code-block:: python

      import anndata as ad
      adata = ad.read_h5ad("export/adata.h5ad")

``cell_boundaries.parquet``
   One polygon per cell (GeoParquet), indexed by ``cell_id``.

   .. code-block:: python

      import geopandas as gpd
      boundaries = gpd.read_parquet("export/cell_boundaries.parquet")

``transcripts.parquet``
   Assigned transcripts (written with ``segger export transcripts``): ``row_index``,
   ``segger_cell_id``, ``feature_name``, ``x``, ``y``.

   .. code-block:: python

      import polars as pl
      transcripts = pl.read_parquet("export/transcripts.parquet")

``<sdata>.zarr`` (in place)
   Written with ``segger export spatialdata --sdata /path/to/sdata.zarr``: edits the given
   SpatialData store in place. Appends segger's per-transcript columns (``segger_cell_id``,
   ``segger_similarity``, ``segger_similarity_threshold``, ``segger_converged``,
   ``segger_filtered``, ``segger_seen``) to its existing ``transcripts`` points element, and adds
   ``cell_boundaries_segger`` (shapes) and ``table_segger`` (table) elements. Element names are
   configurable with ``--sdata-transcripts-name``, ``--sdata-cell-boundaries-name``, and
   ``--sdata-table-name``. See :doc:`quickstart` for how to build the SpatialData store in the
   first place (e.g. with ``spatialdata-io`` for Xenium).

   .. code-block:: python

      import spatialdata as sd

      sdata = sd.read_zarr("/path/to/sdata.zarr")
      sdata["transcripts"]             # vendor transcripts + segger's columns
      sdata["cell_boundaries_segger"]  # segger's cell polygons
      sdata["table_segger"]            # segger's cell x gene AnnData
