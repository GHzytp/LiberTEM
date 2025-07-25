Changelog
=========

.. testsetup:: *

    from libertem import api
    from libertem.executor.inline import InlineJobExecutor
    from libertem.udf.sum import SumUDF

    ctx = api.Context(executor=InlineJobExecutor())
    dataset = ctx.load("memory", datashape=(16, 16, 16, 16), sig_dims=2)
    udf = SumUDF()

.. _continuous:

0.16.0.dev0
###########

.. toctree::
  :glob:

  changelog/*/*


.. _latest:

.. _`v0-15-2`:

0.15.2 / 2025-07-14
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.15877919.svg
  :target: https://doi.org/10.5281/zenodo.15877919

Fix for a backwards-incompatible change of :code:`scheduler_info()` in
Dask Distributed 2025.4.0 (:pr:`1751`, see also
`dask/distributed#9045 <https://github.com/dask/distributed/pull/9045>`_).

Special thanks to Mat who tracked this down!

.. _`v0-15-1`:

0.15.1 / 2025-06-16
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.15676206.svg
  :target: https://doi.org/10.5281/zenodo.15676206

Fix dependency specifications to allow installation in Gatan Digital Micrograph
Python environment with Python 3.10 and NumPy 1.23.5 (:pr:`1743`).


.. _`v0-15-0`:

0.15.0 / 2025-06-04
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.15584540.svg
  :target: https://doi.org/10.5281/zenodo.15584540

With the release 0.15.0 LiberTEM is **re-licensed** under the **MIT license**
(:pr:`1689`)! Subprojects, in particular `LiberTEM-live
<https://github.com/LiberTEM/LiberTEM-live/pull/172>`__, will follow as far as
their upstream licenses allow. Many thanks to all creators for agreeing to this
change! We hope that this makes integrating LiberTEM into other software or
systems easier. The discussion can be found at :issue:`1649`.

Thanks to a joint bug hunt with Gatan, LiberTEM (and many other Python packages)
work again within the Python scripting environment of Gatan Digital Micrograph /
GMS since at least version 3.61 and newer. That allowed us to update the GMS
integration examples. Most notably, they now default to the threaded executor
which combines fast startup with decent performance on most systems. Many thanks
for the fruitful collaboration!

A warm welcome and many thanks to `Sivert Dagenborg
<https://github.com/sivborg>`_ who started contributing to LiberTEM with this
release. Thanks go out to all other contributors as well!

Features
--------

* Add support for 16-bit BLO v1.3 files (:pr:`1676`).
* Support to snooze the executor after inactivity to free resources occupied by
  worker processes, in particular GPU memory held by CuPy. This was previously
  available in the web client, but has now been exposed in the Python API.
  It is supported only in the default :code:`DaskJobExecutor` at this time
  (:issue:`1576`, :pr:`1690`). Currently, it must be activated by the user. We
  plan to activate it with aggressive defaults during the 0.16 development cycle for
  testing, and then activate it by default with conservative defaults in the
  next release.
* MIB: Starting with version 1.5 of the merlin software, the `ScanX` and `ScanY`
  fields are included with the acquisition header (:code:`.hdr` file), if the
  software knows about them. We now try to read these and fall back to the old
  logic if they are not present (:issue:`1630`, :pr:`1631`).
* Allow overriding the number of partitions through a parameter
  :code:`num_partitions` that was added to most :code:`DataSet` implementations.
  With that expert users can override the number of partitions when loading
  data, in cases where the default heuristic doesn't work well (:issue:`1701`, :pr:`1702`).
* Change the number of partitions in case there are fewer frames than
  workers, where it is an overall advantage to have small (1-frame) partitions
  instead of aggregating all frames into a single partition, especially if there
  is a lot of processing done per frame (:issue:`1701`, :pr:`1702`).
* Add support for Python 3.13 (:pr:`1696`).
* Add a :ref:`shortcut to generate ROIs <udf roi>` from any numpy-supported slicing (:pr:`1704`):

.. testcode:: run

    roi = dataset.roi[3:7, 9:12]
    ctx.run_udf(udf=udf, dataset=dataset, roi=roi)

* Adds support for :code:`scipy.sparse.{coo,csr,csc}_array` in addition to
  :code:`scipy.sparse.{coo,csr,csc}_matrix` through
  :code:`sparseconverter>=0.4.0` (:pr:`1684`).

Bugfixes
--------

* Update GMS examples and fix live plotting for GMS 3.61 (:issue:`1418`, :pr:`1712`).

Obsolescence
------------

* Remove :code:`ClusterDataSet` and :code:`CachedDataSet` since they are
  under-documented and don't work well; they are fully replacable by filesystem
  system level caching like fscache. (:pr:`1706`)
* Remove the holography implementation. It was partly adapted from HyperSpy
  under GPL v3 and would prevent relicensing of LiberTEM to MIT (:issue:`1649`).
  Development of holography support in LiberTEM has moved to
  https://github.com/LiberTEM/LiberTEM-holo. Since the previous code had only
  few users, and relicensing to MIT should not be delayed, it is removed without
  deprecation period and will throw an error on import explaining the situation.
  We recommend that users who depend on the previous code use an older LiberTEM
  version (<= 0.14.2) for the time being and migrate to the new package when
  possible. (:pr:`1689`)

Miscellaneous
-------------

* Replace vector field colormap from :code:`empyre` with custom implementation
  based on :code:`colorcet` to remove dependency on GPL code (:pr:`1687`, see
  also :issue:`1649`).
* LiberTEM has been migrated to use `hatchling` as its build backend and all
  metadata moved into :code:`pyproject.toml`. (:pr:`1667`)

.. _`v0-14-2`:

0.14.2 / 2025-04-10
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.15191240.svg
  :target: https://doi.org/10.5281/zenodo.15191240

Support for sparse v0.16.0, and an important bugfix for high idle CPU usage
(:issue:`1707`, :pr:`1708`).

.. _`v0-14-1`:

0.14.1 / 2024-06-19
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.12168380.svg
  :target: https://doi.org/10.5281/zenodo.12168380

Support for numpy 2.x. Note that some examples may not yet run, as the
dependencies might not be numpy 2 compatible. For these cases, please downgrade
to :code:`numpy<2` (:pr:`1656`, :issue:`1653`).

.. _`v0-14-0`:

0.14.0 / 2024-05-16
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.11204954.svg
  :target: https://doi.org/10.5281/zenodo.11204954

The 0.14.0 release adds a system for UDFs to be aware of which parts of a
dataset have already been processed during merging and post-processing,
and to mark parts of results as invalid when the processing would otherwise
be incorrect. This feature supports the plotting interface by allowing
a UDF to mark outliers in results to be ignored, and computations such as
discrete derivatives where the result at the boundaries of the processed
data is undefined.

This release also increases the minimum supported Python version to
3.9 in line with a range of other projects. Older versions of LiberTEM
remain available in sitations where Python cannot be upgraded.

Many thanks to everyone who has contributed to this release!

Features
--------

- Add a system for UDFs to be aware of which parts of a
  dataset have already been processed during merging and
  post-processing, and mark parts of results as invalid.
  Documentation for this feature is found `here <https://libertem.github.io/LiberTEM/udf/advanced.html#valid-data-masking>`_.
  (:issue:`1449` :pr:`1593`).
- Add a new snooze function to :code:`libertem-server`; the argument
  :code:`--snooze-timeout` will free resources after a duration of inactivity
  which is especially useful when deploying LiberTEM as a service on a
  multi-user system. (:issue:`1570`, :pr:`1572`).
- In :class:`~libertem.udf.com.CoMUDF`, added `field_y` and `field_x` to results, 
  which are slices of their respective components in the `field` result (:pr:`1608`).

Bugfixes
--------

- Fix loading of EMPAD data acquired in "search" mode. In some data sets, the actual raw
  data acquired corresponds to the :code:`mode="search"` scan parameters,
  and not :code:`mode="acquire"`. The correct mode can be determined by comparing the file size and file name (:issue:`1617`, :pr:`1620`).
- Fix the :class:`~libertem.udf.com.CoMUDF` automatic offset and gradient
  compensation to yield reasonable results during live processing (:pr:`1593`).
- Manually add MIME type for Javascript on Windows, this is needed to fix
  setups with an invalid registry entry (:issue:`1612` :pr:`1613`).

Documentation
-------------

- Describe how to install LiberTEM safely on
  `air-gapped systems <https://libertem.github.io/LiberTEM/install.html#air-gapped-installation>`_.
  (:pr:`1627`).

Obsolescence
------------

- Remove support for Python 3.7 and Python 3.8 following numpy and other
  projects (:issue:`1606`, :pr:`1607`).
- The modules :code:`libertem.analysis.gridmatching` and
  :code:`libertem.analysis.fullmatch` are now available from
  :mod:`libertem_blobfinder.common.gridmatching` and
  :mod:`libertem_blobfinder.common.fullmatch` since :mod:`libertem-blobfinder`
  version 0.6. The original import locations trigger a deprecation warning and
  are slated for removal in LiberTEM 0.16. (:issue:`1469`, :pr:`1600`).

.. _`v0-13-1`:

0.13.1 / 2023-11-09
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.10091765.svg
  :target: https://doi.org/10.5281/zenodo.10091765

The 0.13.1 point-release fixes an issue with loading assets when
connecting to the :code:`libertem-server` web interface via a proxy (:pr:`1554`).

.. _`v0-13-0`:

0.13.0 / 2023-11-09
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.10091431.svg
  :target: https://doi.org/10.5281/zenodo.10091431

The 0.13.0 release adds a number of improvements to the :code:`libertem-server`
web interface and its user experience, as well as laying the foundation
for descan error compensation in all UDFs based on integration over masked
regions of a frame.

Many thanks to everyone who has contributed to this release!

Web Interface
-------------

- A progress bar has been added to the web GUI to give feedback on the state of any
  in-flight computations. (:issue:`1500`, :pr:`1514`)
- The cluster launcher in the web interface now allows specification
  of multiple workers-per-GPU, allowing better utilisation of large
  GPUs. (:issue:`1489`, :pr:`1499`)
- Direct opening of datasets from URL parameters. The web interface
  now responds to arguments passed to it by URL, namely instructions
  to load a particular dataset, e.g. :code:`#action=open&path=/path/to/your/data/`.
  (:issue:`1085`, :pr:`1518`)
- When launching :code:`libertem-server` additional parameters are now accepted
  to preload a cluster with a given number of CPU and GPU workers, and also preload data. See
  `the libertem-server documentation <https://libertem.github.io/LiberTEM/usage.html#configuring-the-libertem-server-advanced>`_
  for more detail.(:issue:`1419`, :pr:`1535`)

Descan compensation
-------------------

- The backend and interface of :code:`ApplyMasksUDF` has been extended to allow
  compensation for descan error in the form of a :code:`shifts` parameter.
  The shifts information is used to translate any applied masks
  relative to the given frame being processed. This lays the groundwork
  for future support for :code:`shifts` in all UDFs which rely on
  the backend of :code:`ApplyMasksUDF`, including :code:`CoMUDF` and
  all virtual imaging analyses. (:pr:`1304`)

UDF Interface
-------------

- The UDF interface was extended to allow a UDF to declare
  multiple processing methods (:code:`process_tile`, :code:`process_frame` etc)
  and choose at runtime the most appropriate one to use. See
  `the UDF documentation <https://libertem.github.io/LiberTEM/udf/advanced.html#precedence>`_
  for more detail. (:issue:`1508`, :pr:`1509`)

Misc
----

- A function :meth:`libertem.contrib.convert_transposed.convert_dm4_transposed`
  has been added to efficiently convert Gatan Digital Micrograph STEM datasets
  stored in :code:`(sig, nav)` ordering to numpy .npy files in :code:`(nav, sig)`
  ordering (:pr:`1520`).
- Several Exception types were moved to :mod:`libertem.common` for MIT
  license compatibility. They are re-exported to
  the old import location for backwards compatibility (:pr:`1543`).
- The :code:`Shape` class is now hashable allowing it to be used as key
  in a :code:`dict` and :code:`set`. (:pr:`1507`).


.. _`v0-12-0`:

0.12.0 / 2023-08-16
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.8252548.svg
   :target: https://doi.org/10.5281/zenodo.8252548

The 0.12 release introduces a dedicated Centre-of-Mass analysis UDF
with additional features compared to the existing
:meth:`~libertem.api.Context.create_com_analysis()` API. The dedicated
UDF allows more straightforward live processing support, and can be
subclassed as in the `LiberTEM-iCoM <https://github.com/LiberTEM/LiberTEM-iCoM>`_
project.

Also included are two usability improvements aimed at simplifying
interaction with LiberTEM:

* :code:`ctx.export_dataset()` for conversion of any supported
  LiberTEM dataset to the Numpy binary format *.npy*.
* :code:`lt.Context.make_with(cpus=n, gpus=m)` to simplify creation
  of a LiberTEM :code:`Context` with a specfic number of workers
  (rather than using all available resources by default).

This release also contains numerous fixes and backend changes
across the codebase, improving the robustness of support for
both sparse data and live processing in particular.

Many thanks to everyone who has contributed to this release!

The 0.12.0 release is also the first major release for which
LiberTEM is available on `conda-forge <https://anaconda.org/conda-forge/libertem>`_.
See `the installation documentation <https://libertem.github.io/LiberTEM/install.html>`_
for installation instructions using :code:`pip` or :code:`conda`.

Features
--------

* Adds :class:`~libertem.udf.com.CoMUDF` as an extension from
  :meth:`~libertem.api.Context.create_com_analysis`, with support for automatic
  correction of flat field effects (beam offset and descan
  error) (:pr:`1392`).
* Adds :meth:`libertem.api.Context.export_dataset` for export of any supported
  LiberTEM dataset to another format. At this time only exporting to
  the Numpy binary fomat *.npy* is supported, but other formats
  can later be added according to need (:pr:`1379`).
* The :meth:`libertem.api.Context.make_with` constructor method has been improved
  to allow simple specification of the number CPU and GPU workers to use,
  as well as the type of executor to create (:pr:`1443`).
* Experimental API for dynamic updates to parameters while UDFs
  are running, allowing for faster feedback loops during live processing,
  notably (:pr:`1441`).

Miscellaneous
-------------

* The :class:`~libertem.executor.pipelined.PipelinedExecutor` executor now
  schedules tasks on the worker with the smallest request queue size (:pr:`1451`).
* Update :class:`~libertem.executor.pipelined.PipelinedExecutor` to properly
  match tasks to workers based on resources requested, including GPU workers
  (:pr:`1453`).
* Array backend categories are now available in
  :class:`~libertem.udf.base.UDF` (:pr:`1470`).
* The UDF runner internals will now raise :class:`~libertem.common.exceptions.UDFRunCancelled`
  if a UDF run is cancelled, allowing the user to handle this case (:pr:`1448`).
* Introduce option for :code:`sparse.GCXS` in
  :class:`~libertem.common.container.MaskContainer` (:pr:`1447`).

Bugfixes
--------

* We now properly forward the logging level to :code:`dask.distributed` workers
  during initialization. This prevents a substantial amount of logging
  information being printed to :code:`stderr` at cluster startup. (:pr:`1438`).
* When processing *raw_csr* datasets, we now avoid upcasting to
  float64 when reading an indptr of type uint64 (:pr:`1465`).

Deployment
----------

* Starting with version 0.12, LiberTEM container images will be available
  `from the GitHub container registry <https://ghcr.io/libertem/libertem>`_.


.. _`v0-11-2`:

0.11.2 / 2023-07-04
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.8114529.svg
   :target: https://doi.org/10.5281/zenodo.8114529

This point release works around `issue #597 in hdbscan <https://github.com/scikit-learn-contrib/hdbscan/issues/597>`_,
which is an incompatibility with the latest scikit-learn release 1.3.0.

.. _`v0-11-1`:

0.11.1 / 2023-05-11
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.7924714.svg
   :target: https://doi.org/10.5281/zenodo.7924714

This point release works around `issue #8940 in numba 0.57.0 <https://github.com/numba/numba/issues/8940>`_,
which can cause crashes under certain circumstances.

.. _`v0-11-0`:

0.11.0 / 2023-04-21
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.7853070.svg
   :target: https://doi.org/10.5281/zenodo.7853070

This release introduces first-class support for sparse input data and
processing using sparse-compatible libraries, primarily oriented towards
data from event-based detectors. This support is provided through the new
`sparseconverter <https://github.com/LiberTEM/sparseconverter>`_ package,
which was developed to enable efficient inter-conversion between different
sparse formats and dense arrays. Many thanks in particular to Alexander
Clausen and Dieter Weber for their extensive work on sparse support.
See :ref:`sparse` for details on how to use it in your UDFs, and :ref:`raw csr`
for a sparse input file format!

This release also marks the end of official support for Python 3.6
(:issue:`1369`, :pr:`1380`). At this time the maximum supported Python 
version remains 3.10 owing to usage of the
`Numba library <https://numba.pydata.org/>`_ within LiberTEM.

Sparse processing
-----------------
* Datasets and UDFs now support tiles in various array formats, in
  particular :ref:`sparse`. (:issue:`1205`, :pr:`1207`)
* Add support for :code:`raw_csr` file format (:issue:`1195`, :pr:`1207`).
* ROIs can now now be provided to :code:`ctx.run_udf` as sparse arrays or coordinate
  tuples rather than as a dense boolean mask. (:pr:`1306`)

Other features
--------------

* Single-file Gatan Digital Micrograph (DM3/DM4) STEM datasets are now supported
  in the API and in the web interface. The files must use the newer C-style
  ordering available in Digital Micrograph to be read correctly. (:pr:`1401`)
* The information provided by the :code:`ctx.run_udf` progress bar has been improved
  with framewise updates, allowing feedback on progress before a partition completes.
  (:pr:`1341`)
* HDF5 datasets can now perform on-the-fly reshaping of the scan grid and
  can be adjusted using the `sync_offset` parameter. (:issue:`441`, :pr:`1364`)

Bugfixes
--------

* Numerous bugs were fixed in the behaviour of the :code:`PipelinedExecutor`,
  to avoid crashes and deadlocks throughout the lifecycle of the object,
  particulary duing live processing.
  (:pr:`1308`, :pr:`1311`, :pr:`1316`, :pr:`1318`, :pr:`1319`, :pr:`1342`)
* The :code:`Context` object will now attempt to close itself cleanly at
  process exit (:pr:`1343`).
* The handling of :code:`sync_offset`, :code:`nav_shape`, :code:`sig_shape` and
  :code:`sig_dims` in :class:`libertem.io.dataset.memory.MemoryDataSet` is now
  consistent with other datasets. (:pr:`1207`)
* Bugs were fixed in :class:`libertem.udf.stddev.StdDevUDF` for both complex
  input data and when some partitions are empty.  (:pr:`1314`)

Miscellaneous
-------------

* Make `self.meta.coordinates` available in `UDF.get_task_data` (:pr:`1397`).
* The methods :meth:`libertem.executor.pipelined.PipelinedExecutor.make_spec` and
  :func:`libertem.executor.dask.cluster_spec` both now accept integers for
  their :code:`cpus` and :code:`cudas` arguments, in addition to the existing
  iterable forms. (:issue:`1294`, :pr:`1336`).
* Reduced the overhead of (re-)constructing :code:`Slice` and :code:`Shape`
  objects when slicing tiles, in particular focused on the method
  :code:`BufferWrapper.get_contiguous_view_for_tile()`. (:issue:`1313`, :pr:`1321`)

Web interface
-------------

* Datasets which don't declare their nav shape will be interpreted as square
  if their number of frames is a square number (:issue:`1309`, :pr:`1338`).
* Raise if user input for :code:`numWorker` is non-positive integer
  or other error is encountered in :code:`DaskJobExecutor` creation. (:pr:`1334`).
* The dataset loader `Reset`` button now correctly resets the sync_offset
  field, if present (:pr:`1378`).
* The function :code:`detect` to automatically determine dataset type
  will now use the file suffix as a hint to choose its search order.
  This may lead to faster responses in the web client when configuring
  a new dataset. (:pr:`1377`)

Breaking changes
----------------
* Instead of :class:`~libertem.io.dataset.base.tiling.DataTile` objects, an UDF's
  processing method will receive plain array objects, such as
  :class:`numpy.ndarray`, :class:`sparse.SparseArray` etc. That means the
  :code:`scheme_idx` and :code:`tile_slice` attributes are not available from
  the tile anymore, but only from the corresponding
  :attr:`libertem.udf.base.UDFMeta.tiling_scheme_idx` and
  :attr:`libertem.udf.base.UDFMeta.slice`. This change makes handling different
  array types such as sparse arrays or CuPy arrays easier. For CuPy arrays this
  was already the previous behavior.

.. _`v0-10-0`:

0.10.0 / 2022-07-28
###################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.6927963.svg
   :target: https://doi.org/10.5281/zenodo.6927963

This release features the :ref:`pipelined` for parallel live data processing (:pr:`1267`).
This change greatly improves the processing performance for live data, in
particular to support detectors with high data rate. Many thanks to Alexander
Clausen and Matthew Bryan for their work! The corresponding capabilities in the
`LiberTEM-live <https://github.com/LiberTEM/LiberTEM-live/>`_ package will be
released soon and announced separately.

Other changes:

New features
------------

* Support for Python 3.10.
* :ref:`npy format` for reading NumPy .npy files (:issue:`222`, :pr:`1249`).
* Support for updated EMPAD XML format, including series (:issue:`1259`,
  :pr:`1260`).
* Integrate :ref:`tracing` that allows to debug and trace distribted operation
  of LiberTEM (:issue:`691`, :pr:`1266`).
* :code:`libertem-server` picks a free port if the default is in use and no port
  was specified (:issue:`1184`, :pr:`1279`).
* :func:`~libertem.executor.dask.cluster_spec` now accepts the same CUDA device
  ID multiple times to spawn multiple workers on the same GPU. This can help
  increase GPU resource utilisation for some workloads (:pr:`1270`).

Bugfixes
--------

* Correct type determination in :class:`~libertem.udf.auto.AutoUDF`
  (:pr:`1298`).
* Fix non-square plots (:pr:`1255`).
* Disable the Dask profiler due to `issues with the DM dataset
  <https://github.com/dask/distributed/issues/6776>`_ (:pr:`1289`).
* Fix GUI glitch in center of mass analysis (:pr:`1278`).

Documentation
-------------

* Example on :ref:`binning <binning>` (:pr:`1250`).

Miscellaneous
-------------

* Include tests in PyPI release to prepare release on conda-forge, and exclude
  unneeded files. (:issue:`1271,1275`, :pr:`1276`).
* Move some code around to make sure that :mod:`libertem.io` and
  :mod:`libertem.common` only depend on code that is compatible with the MIT
  license. Moved items are re-imported at the same positions as before to keep
  backwards compatibility (:issue:`1031`, :pr:`1245`).


.. _`v0-9-2`:

0.9.2 / 2022-04-28
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.6502282.svg
   :target: https://doi.org/10.5281/zenodo.6502282

This is a bugfix release with two small fixes:

* Example notebook: compatibility with HyperSpy 1.7.0 :pr:`1242`
* Compatibility of CoM auto button with Jupyter server proxy :pr:`1220`

.. _`v0-9-0`:

0.9.0 / 2022-02-17
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.6125247.svg
   :target: https://doi.org/10.5281/zenodo.6125247

We are most happy to announce **full Dask array integration** with this release!
Many thanks to Matthew Bryan who implemented major parts of this non-trivial
feature. Most notably, HyperSpy lazy signals and LiberTEM can now be combined
seamlessly. See :ref:`dask` for details and an example!

This enables the following applications:

* Use HyperSpy file readers and other readers that create Dask arrays for
  LiberTEM.
* Create an ad-hoc file reader for LiberTEM by just building a Dask array. This
  is often simpler than implementing a native LiberTEM dataset, at the expense
  of performance.
* Use LiberTEM file readers for HyperSpy and other software that works with
  Dask arrays.
* Use the same implementation of an algorithm for live processing with LiberTEM,
  offline processing with LiberTEM, and offline processing with HyperSpy.
* Simplify implementation of complex processing routines on Dask arrays. That
  includes, for example, routines that are not purely implemented with NumPy
  array operations and produce complex output or are not compatible with all
  Dask array chunking schemes. Here, LiberTEM UDFs offer a more powerful and
  versatile interface than Dask's native `map_blocks()
  <https://docs.dask.org/en/latest/generated/dask.array.map_blocks.html>`_
  interface.
* Chain processing steps together using Dask arrays for intermediate results,
  including using the output of one UDF as input for another UDF. Dask arrays
  allow working with large intermediate results efficiently since they can
  remain on the workers.

Specifically, the Dask integration encompasses the following features:

* Create LiberTEM datasets from Dask arrays via the :ref:`daskds` (:pr:`1137`).
* Create Dask arrays from LiberTEM UDF results using the
  :class:`~libertem.executor.delayed.DelayedJobExecutor`. A UDF can define a
  :meth:`~libertem.udf.base.UDFMergeAllMixin.merge_all` method in addition to
  the usual :meth:`~libertem.udf.base.UDF.merge` to improve performance. See
  :ref:`merge_all` for details (:pr:`1170`)!
* Create Dask arrays directly from LiberTEM datasets using
  :func:`libertem.contrib.daskadapter.make_dask_array`, which is already
  possible since release 0.2.
* Executor options to improve integration, see :ref:`scheduler` and
  :ref:`executors` (:pr:`1170`, :issue:`1146,922`).

Please note that these features are still experimental and cover a large space
of possible uses and parameters. Expect the unexpected! Tests, feedback and
improvements are highly appreciated.

Other changes in this release:

New features
------------

* Experimental helper function :meth:`libertem.analysis.com.guess_corrections`
  to guess parameters for Center of Mass analysis (:pr:`1111`).
* GUI interface for the COM analysis to call :meth:`libertem.analysis.com.guess_corrections`
  and update the GUI parameters from the result (:pr:`1172`).
* Support for some MIB Quad formats. All integer formats should be supported and
  were tested with :code:`1x1` and :code:`2x2` layouts. Raw formats with
  :code:`1x1` and :code:`2x2` layouts using 1 bit, 6 bit, and 12 bit counter
  depth are supported as well. Support for raw MIB data in other layouts and bit
  depths can be added on demand (:pr:`1169`, :issue:`1135`).
* New attributes :attr:`libertem.udf.base.UDFMeta.sig_slice` and
  :attr:`libertem.udf.base.UDFMeta.tiling_scheme_idx`. These attributes can be used for performant
  access to the current signal slice - mostly important for throughput-limited
  analysis (:pr:`1167`, :issue:`1166`).
* New :code:`--preload` option to :code:`libertem-server` and :code:`libertem-worker`.
  That makes it work as documented in :ref:`hdf5`, following
  `Dask worker preloading
  <https://docs.dask.org/en/stable/customize-initialization.html#preload-scripts>`_
  (:pr:`1151`).
* Allow selection of I/O backend in GUI and Python API (:issue:`753`, :pr:`896,1129`).
* Re-add support for direct I/O. It was previously only supported as a special
  case for raw files on Linux. Now it is supported for all native dataset
  formats we support on Linux and Windows. Notable exceptions are the OS X
  platform or HDF5, MRC, and SER formats (:pr:`1129`, :issue:`753`).
* Support for reading TVIPS binary files, i.e. :code:`*_NNN.tvips` files (:pr:`1179`).

Bugfixes
--------

* Allow running CoM analysis on a linescan dataset by only returning divergence
  and curl if they are defined (:issue:`1138`, :pr:`1139`).
* :code:`make_dask_array` now works correctly when a :code:`roi` is specified
  (:issue:`933`).
* Correct shape of buffer views in :meth:`~libertem.udf.base.UDFTileMixin.process_tile`
  when the tile has depth 1 (:pr:`1215`).


Documentation
-------------

* Information on multithreading added to UDF docs in :ref:`threading` (:pr:`1170`).

Miscellaneous
-------------

* A `Docker image with a LiberTEM installation
  <https://hub.docker.com/r/libertem/libertem/tags>`_ is available on DockerHub
  now. See :ref:`containers` for details (:pr:`1144`, :issue:`484`).
* Improve performance with large UDF parameters (:pr:`1143`).
* Start using :mod:`libertem.preload` again and import :code:`hdf5plugin` if
  present so that users don't have to specify this common selection of HDF5
  filters as preload themselves (:pr:`1160`).


.. _`v0-8-0`:

0.8.0 / 2021-10-04
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.5547992.svg
   :target: https://doi.org/10.5281/zenodo.5547992

This release mainly contains improvements of center of mass / first moment
analysis and support for starting the web GUI from JupyterHub or JupyterLab.

New features
------------

* Support for center of mass with annular masks in :meth:`~libertem.api.Context.create_com_analysis`,
  :class:`~libertem.analysis.com.COMAnalysis` and the GUI (:issue:`633`, :pr:`1089`).
* Support in the GUI for specifying rotation of scan against detector and
  flipping the detector y axis (:pr:`1087`, :issue:`31`). Previously this was only
  supported in the Python API.
* Tweaks and instructions for JupyterHub and JupyterLab integration in LiberTEM, see :ref:`jupyter integration` (:pr:`1074`).
  New package `LiberTEM/LiberTEM-jupyter-proxy <https://github.com/LiberTEM/LiberTEM-jupyter-proxy>`_
  for interfacing.
* In the web API, support was added to re-run visualization only, without
  re-running UDFs for an analysis. This allows for almost instant feedback
  for some operations, like changing CoM parameters.
* Added token-based authentication. For now, it is only usable via
  integrations like Jupyter. It will be extended to local/manual usage
  later (:pr:`1074`, :issue:`1097`). Please comment on :issue:`1097` if local/manual use
  would be beneficial for you so that it is prioritized accordingly.
* SEQ dataset: Added support for loading excluded pixels from XML (:issue:`805`, :pr:`1077`).
  See :class:`~libertem.io.dataset.seq.SEQDataSet` for more information. Also
  support both :code:`*.seq.seq` and :code:`*.seq` as extension for the main SEQ file
  to find files with matching base name that contain correction data (:issue:`1120`, :pr:`1121`).

Bugfixes
--------

* Assert that the :code:`files` argument to :class:`~libertem.io.dataset.dm.DMDataSet` is actually a list or tuple,
  to prevent iterating over a string path (:pr:`1058`).
* Escape globs to support special characters in file names for multi-file
  datasets (:issue:`1066`, :pr:`1067`).
* Make sure multithreading in the main process still works properly after
  launching a :class:`~libertem.api.Context` (:issue:`1053`, :pr:`1100`).
* Allow custom plots to return RGB as plot data, for example a color
  wheel for vector fields (:issue:`1052`, :pr:`1101`).
* Adjust partition count to match the number of CPU compute workers,
  not total workers to prevent residual partitions (:issue:`1086`, :pr:`1103`).
* Correct partition shape for ROI in :class:`~libertem.udf.base.UDFMeta` (:pr:`1109`).
* Fix memory leak: Don't submit dynamically generated callables directly to the distributed cluster,
  as they are cached in an unbounded cache (:issue:`894,964`, :pr:`1119`).

Documentation
-------------

* Note on handling HDF5 files with non-standard compression
  in :class:`~libertem.io.dataset.hdf5.H5DataSet` (:pr:`1059`).
* Link to two more public datasets: :ref:`hires STO` and :ref:`synthetic STO` (:pr:`1073`).

Misc
----

* Speed up coordinate calculation (:issue:`1108`, :pr:`1109`).
* Make sure tasks are scheduled dynamically on available workers if they have uneven
  run time to benefit more from GPUs (:pr:`1107`).
* Cache loaded libraries to reduce overhead of setting the thread count (:issue:`1117`, :pr:`1118`).

Many thanks to our new contributors Levente Puskás for the excluded pixel loading and to
Matthew Bryan for figuring non-standard compression in HDF5 and improving DM
input validation. Congratulations to Alex for closing the long-standing CoM issue :issue:`31`
and for enabling easy and secure access to the web interface on shared IT infrastructure.

.. _`v0-7-1`:

0.7.1 / 2021-07-08
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.5083032.svg
   :target: https://doi.org/10.5281/zenodo.5083032

This is a bugfix release that ensures compatibility with the upcoming numba 0.54
release.

Our custom numba caching makes some assumptions about numba internals, which
have changed in numba 0.54. This fixes compatibility with numba 0.54, and also
makes sure we fail gracefully for future changes (:issue:`1060`, :pr:`1061`).

.. _`v0-7-0`:

0.7.0 / 2021-06-10
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.4923277.svg
   :target: https://doi.org/10.5281/zenodo.4923277

This release introduces features that are essential for live data processing,
but can be used for offline processing as well: Live plotting, API for bundled
execution of several UDFs in one run, iteration over partial UDF results, and
asynchronous UDF execution. Features and infrastructure that are specific to
live processing are included in the `LiberTEM-live
<https://github.com/LiberTEM/LiberTEM-live/>`_ package, which will be released
soon.

New features
------------

* Support for postprocessing of results on the main node after merging partial
  results. This adds :meth:`~libertem.udf.base.UDF.get_results` and the
  :code:`use` parameter to :meth:`~libertem.udf.base.UDF.buffer`. See :ref:`udf
  final post processing` for details (:pr:`994`, :pr:`1003`, :issue:`1001`).
* Obtain partial results from each merge step iteratively as a generator
  using :meth:`~libertem.api.Context.run_udf_iter`. See :ref:`partial` and an
  `example
  <https://github.com/LiberTEM/LiberTEM/blob/master/examples/async.ipynb>`_ for
  details (:pr:`1011`)!
* Run multiple UDFs in one pass over a single :code:`DataSet` by passing a
  list of UDFs instead of one UDF in :meth:`~libertem.api.Context.run_udf` and
  :meth:`~libertem.api.Context.run_udf_iter` (:pr:`1011`).
* Allow usage from an asynchronous context with the new :code:`sync=False`
  argument to :meth:`~libertem.api.Context.run_udf` and
  :meth:`~libertem.api.Context.run_udf_iter`. See :ref:`partial` and an `example
  <https://github.com/LiberTEM/LiberTEM/blob/master/examples/async.ipynb>`_ for
  details (:issue:`216`, :pr:`1011`)!
* Live plotting using the new :code:`plots` parameter for
  :meth:`~libertem.api.Context.run_udf` and
  :meth:`~libertem.api.Context.run_udf_iter`, as well as live plotting classes
  documented in :ref:`viz reference`. Pass :code:`plots=True` for simple usage.
  See :ref:`plotting` as well as `an example
  <https://github.com/LiberTEM/LiberTEM/blob/master/examples/live-plotting.ipynb>`_
  for the various possibilities for advanced usage (:issue:`980`, :pr:`1011`).
* Allow some UDF-internal threading. This is mostly
  interesting for ad-hoc parallelization on top of the
  :class:`~libertem.executor.inline.InlineJobExecutor` and live processing that
  currently relies on the :class:`~libertem.executor.inline.InlineJobExecutor`
  for simplicity, but could also be used for hybrid multiprocess/multithreaded
  workloads. Threads for numba, pyfftw, OMP/MKL are automatically
  controlled. The executor makes the number of allowed threads available as
  :attr:`libertem.udf.base.UDFMeta.threads_per_worker` for other threading
  mechanisms that are not controlled automatically (:pr:`993`).
* K2IS: reshaping, sync offset and time series support. Users can now specify a
  :code:`nav_shape`, :code:`sig_shape` and :code:`sync_offset` for a K2IS data
  set, and load time series data (:pr:`1019`, :issue:`911`). Many thanks to
  `@AnandBaburajan <https://github.com/AnandBaburajan>`_ for implementing this
  feature!
* Support for Python >=3.9.3, use Python 3.9 in AppImage (:issue:`914`, :pr:`1037,1039`).

Bugfixes
--------

* UDF: Consistently use attribute access in :code:`UDF.process_*()`, :code:`UDF.merge()`,
  :code:`UDF.get_results()` etc. instead of mixing it with :code:`__getitem__()`
  dict-like access. The previous method still works, but triggers a :class:`UserWarning`
  (:issue:`1000`, :pr:`1003`).
* Also allow non-sliced assignment, for example
  :code:`self.results.res += frame` (:issue:`1000`, :pr:`1003`).
* Better choice of :code:`kind='nav'` buffer fill value outside ROI.

  * String : Was :code:`'n'`, now :code:`''`
  * bool : Was :code:`True`, now :code:`False`
  * integers : Was smallest possible value, now :code:`0`
  * objects : was :code:`np.nan`, now :code:`None` (:pr:`1011`)

* Improve performance for chunked HDF5 files, especially compressed HDF5 files
  which have a chunking in both navigation dimensions. They were causing
  excessive read amplification (:pr:`984`).
* Fix plot range if only zero and one other value are present
  in the result, most notably boolean values (:issue:`944`, :pr:`1011`).
* Fix axes order in COM template: The components in the field are (x, y)
  while the template had them as (y, x) before (:pr:`1023`).

Documentation
-------------

* Update Gatan Digital Micrograph (GMS) examples to work with the current GMS and
  LiberTEM releases and demonstrate the new features. (:issue:`999`,
  :pr:`1002,1004,1011`). Many thanks to Winnie from Gatan for helping to work
  around a number of issues!
* Restructure UDF documentation (:pr:`1034`).
* Document coordinate meta information (:issue:`928`, :pr:`1034`).

Obsolescence
------------

* Removed deprecated blobfinder and :code:`FeatureVecMakerUDF` as
  previously announced. Blobfinder is available as a separate package at
  https://github.com/liberTEM/LiberTEM-blobfinder. Instead of
  :code:`FeatureVecMakerUDF`, you can use a sparse matrix and
  :code:`ApplyMasksUDF` (:pr:`979`).
* Remove deprecated :code:`Job` interface as previously announced.
  The functionality was ported to the more capable UDF interface :pr:`978`.



.. _`v0-6-0`:

0.6.0 / 2021-02-16
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.4543704.svg
   :target: https://doi.org/10.5281/zenodo.4543704

We are pleased to announce the latest LiberTEM release, with many improvements
since 0.5. We would like to highlight the contributions of our GSoc 2020
students `@AnandBaburajan <https://github.com/AnandBaburajan>`_ (reshaping and
sync offset correction) and `@twentyse7en <https://github.com/twentyse7en>`_,
(Code generation to replicate GUI analyses in Jupyter notebooks) who implemented
significant improvements in the areas of I/O and the user interface.

Another highlight of this release is experimental support of NVidia GPUs, both
via CuPy and via native libraries. The API is ready to be used, including support
in the GUI. Performance optimization is still to be done (:issue:`946`).
GPU support is activated for all mask-based analyses (virtual detector and
Radial Fourier) for testing purposes, but will not bring a noticeable
improvement of performance yet. GPU-based processing did show significant
benefits for computationally heavy applications like the SSB implementation in
https://github.com/Ptychography-4-0/ptychography.

A lot of work was done to implement tiled reading, resulting in a
new I/O system. This improves performance in many circumstances, especially
when dealing with large detector frames. In addition, a correction module was
integrated into the new I/O system, which can correct gain, subtract a dark
reference, and patch pixel defects on the fly. See below for the full
changelog!

New features
------------

* I/O overhaul

  * Implement tiled reading for most file formats
    (:issue:`27`, :issue:`331`, :issue:`373`, :issue:`435`).
  * Allow UDFs that implement :code:`process_tile` to influence the tile
    shape by overriding :meth:`libertem.udf.base.UDF.get_tiling_preferences`
    and make information about the tiling scheme available to the UDF through
    :attr:`libertem.udf.base.UDFMeta.tiling_scheme`. (:issue:`554`,
    :issue:`247`, :issue:`635`).
  * Update :code:`MemoryDataSet` to allow testing with different
    tile shapes (:issue:`634`).
  * Added I/O backend selection (:pr:`896`), which allows users to select the best-performing
    backend for their circumstance when loading via the new :code:`io_backend`
    parameter of :code:`Context.load`. This fixes a K2IS performance regression
    (:issue:`814`) by disabling any readahead hints by default. Additionaly, this fixes
    a performance regression (:issue:`838`) on slower media (like HDDs), by
    adding a buffered reading backend that tries its best to linearize I/O per-worker.
    GUI integration of backend selection is to be done.
  * For now, direct I/O is no longer supported, please let us know if this is an
    important use-case for you (:issue:`716`)!

* Support for specifying logging level from CLI (:pr:`758`).
* Support for Norpix SEQ files (:issue:`153`, :pr:`767`).
* Support for MRC files, as supported by ncempy (:issue:`152`, :pr:`873`).
* Support for loading stacks of 3D DM files (:pr:`877`). GUI integration still to be done.
* GUI: Filebrowser improvements: users can star directories in the file browser for easy navigation (:pr:`772`).
* Support for running multiple UDFs "at the same time", not yet exposed in public APIs (:pr:`788`).
* GUI: Users can add or remove scan size dimensions according to the dataset's shape (:pr:`779`).
* GUI: Shutdown button to stop server, useful for example for JupyterHub integration (:pr:`786`).
* Infrastructure for consistent coordinate transforms are added in
  :mod:`libertem.corrections.coordinates` and :mod:`libertem.utils`. See also a
  description of coordinate systems in :ref:`concepts`.
* :meth:`~libertem.api.Context.create_com_analysis` now allows to specify a :code:`flipped y axis`
  and a scan rotation angle to deal with MIB files and scan rotation correctly. (:issue:`325`, :pr:`786`).
* Corrections can now be specified by the user when running a UDF (:pr:`778,831,939`).
* Support for loading dark frame and gain map that are sometimes shipped with SEQ data sets.
* GPU support: process data on CPUs, CUDA devices or both (:pr:`760`, :ref:`udf cuda`).
* Spinning out holography to a separate package is in progress: https://github.com/LiberTEM/LiberTEM-holo/
* Implement CuPy support in :class:`~libertem.udf.holography.HoloReconstructUDF`, currently deactivated due to :issue:`815` (:pr:`760`).
* GUI: Allows the user to select the GPUs to use when creating a new local cluster (:pr:`812`).
* GUI: Support to download Jupyter notebook corresponding to an analysis
  made by a user in GUI (:pr:`801`).
* GUI: Copy the Jupyter notebook cells corresponding to the
  analysis directly from GUI, including cluster connection details (:pr:`862`, :pr:`863`)
* Allow reshaping datasets into a custom shape. The :code:`DataSet` implementations (currently except HDF5 and K2IS)
  and GUI now allow specifying :code:`nav_shape` and :code:`sig_shape`
  parameters to set a different shape than the layout in the
  dataset (:issue:`441`, :pr:`793`).
* All :code:`DataSet` implementations handle missing data
  gracefully (:issue:`256`, :pr:`793`).
* The :code:`DataSet` implementations (except HDF5 and K2IS)
  and GUI now allow specifying a :code:`sync_offset` to
  handle synchronization/acquisition problems (:pr:`793`).
* Users can access the coordinates of a tile/partition slice
  through :attr:`~libertem.udf.base.UDFMeta.coordinates` (:issue:`553`, :pr:`793`).
* Cache warmup when opening a data set: Precompiles jit-ed functions on a single process per node, in a controlled manner,
  preventing CPU oversubscription. This improves further through implementing caching for functions which capture other functions
  in their closure (:pr:`886`, :issue:`798`).
* Allow selecting lin and log scaled visualization for sum, stddev, pick and single mask analyses 
  to handle data with large dynamic range. This adds key :code:`intensity_lin` to
  :class:`~libertem.analysis.sum.SumResultSet`, :class:`~libertem.analysis.sum.PickResultSet`
  and the result of :class:`~libertem.analysis.sd.SDAnalysis`.
  It adds key :code:`intensity_log` to :class:`~libertem.analysis.sum.SingleMaskResultSet`.
  The new keys are chosen to not affect existing keys
  (:issue:`925`, :pr:`929`).
* Tuples can be added directly to :code:`Shape` objects. Right
  addition adds to the signal dimensions of the :code:`Shape`
  object while left addition adds to the navigation
  dimensions (:pr:`749`)

Bugfixes
--------

* Fix an off-by-one error in sync offset for K2IS data (drive-by change in :pr:`706`).
* Missing-directory error isn't thrown if it's due to last-recent-directory not being available (:pr:`748`).
* GUI: when cluster connection fails, reopen form with parameters user submitted (:pr:`735`).
* GUI: Fixed the glitch in file opening dialogue by disallowing parallel browsing before loading is concluded (:pr:`752`).
* Handle empty ROI and extra_shape with zero. Empty result buffers of the appropriate shape are returned if the ROI
  is empty or :code:`extra_shape` has a zero (:pr:`765`)
* Improve internals of :mod:`libertem.io.corrections.detector` and
  :mod:`libertem.io.corrections.corrset` to better support correction
  of many dead pixels. (:pr:`890`, :issue:`889`)
* Handle single-frame partitions in combination with aux data.
  Instead of squeezing the aux buffer, reshape to the correct shape (:issue:`791`, :pr:`902`).
* Libertem-server can now be started from Bash on Windows (:pr:`731`)
* Fix reading without a copy from multi-file datasets. The start offset of the file was
  not taken account when indexing into the memory maps (:issue:`903`).
* Improve performance and reduce memory consumption of point analysis.
  Custom right hand side matrix product to reduce memory consumption and
  improve performance of sparse masks, such as point analysis. See also
  `scipy/13211 <https://github.com/scipy/scipy/issues/13211>`_ (:issue:`917`, :pr:`920`). 
* Fix stability issue with multiple dask clients. :code:`dd.as_completed` needs
  to specify the :code:`loop` to work with multiple :code:`dask.distributed` clients (:pr:`921`).
* GUI: Snap to pixels in point selection analysis. Consistency between point
  selection and picking (:issue:`926`, :pr:`927`).
* Open datasets with autodetection, positional and keyword arguments.
  Handle keyword and positional arguments to :code:`Context.load('auto', ...)`
  correctly (:issue:`936`, :pr:`938`).

Documentation
-------------

* Switched to the readthedocs sphinx theme, improving the overall
  documentation structure. The developer documentation is now in
  a separate section from the user documentation.

Misc
----

* Command line options can also be accessed with shorter alternatives (:pr:`757`).
* Depend on Numba >= 0.49.1 to support setting Numba thread count (:pr:`783`), bumped to 0.51
  to support caching improvements (:pr:`886`).
* libertem-server: Ask for confirmation if the user press ctrl+c. Can immediately stop using
  another ctrl+c (:pr:`781`).
* Included `pytest-benchmark <https://pytest-benchmark.readthedocs.io/en/latest/usage.html>`_
  to integrate benchmarks in the test infrastructure. See :ref:`benchmarking` for details (:pr:`819`).
* The X and Y components for the color wheel visualization in Center of
  Mass and Radial Fourier Analysis are swapped to match the axis convention in
  empyre. This just changes the color encoding in the visualization and not the
  result (:pr:`851`).

Deprecations
------------

* The :code:`tileshape` parameter of :code:`DataSet` implementations is deprecated in
  favor of tileshape negotiation and will be ignored, if given (:issue:`754`, :pr:`777`).
* Remove color wheel code from :code:`libertem.viz` and replace with imports from empyre.
  Note that these functions expect three vector components instead of two (:pr:`851`).
* The new and consistent :code:`nav_shape` and :code:`sig_shape` parameters should be used
  when loading data. The old :code:`scan_size` and :code:`detector_size` parameters,
  where they existed, are still recognized (:pr:`793`).

.. _`v0-5-1`:

0.5.1 / 2020-08-12
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.3982290.svg
   :target: https://doi.org/10.5281/zenodo.3982290

Bugfixes
--------

* Allow installation with latest dask distributed on Python 3.6 and 3.7

.. _`v0-5-0`:

0.5.0 / 2020-04-23
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.3763313.svg
   :target: https://doi.org/10.5281/zenodo.3763313

New features
------------

* In addition to tuples, :class:`~libertem.common.shape.Shape` objects can be used as
  :code:`extra_shape` parameter for :meth:`libertem.udf.base.UDF.buffer` and
  :meth:`libertem.udf.base.UDF.aux_data` now. (:pr:`694`)
* Progress bar support based on :code:`tqdm` that can be enabled by passing
  :code:`progress=True` to :meth:`libertem.api.Context.run_udf`,
  :meth:`libertem.api.Context.run` and :meth:`libertem.api.Context.map`: :ref:`progress bar`. (:pr:`613,670,655`)
* Include explicit support for Direct Electron's DE5 format based on HDF5. (:pr:`704`)
* GUI: Downloadable results as HDF5, NPZ, TIFF, and RAW. See
  :ref:`download results` for details. (:pr:`665`)
* :meth:`libertem.api.Context.load` now automatically detects file
  type and parameters if :code:`filetype="auto"` is passed. (:pr:`610,621,734`)
* Relocatable GUI: Allow LiberTEM to run from different URL prefixes, allowing integration into,
  for example, JupyterLab. (:pr:`697`)
* Run :meth:`~libertem.udf.base.UDFPreprocessMixin.preprocess` also before merge on
  the main node to allocate or initialize buffers, in addition to running on the
  workers (:pr:`624`).
* No need to set thread count environment variables anymore since the thread count
  for OpenBLAS, OpenMP, Intel MKL and pyFFTW is now set on the workers at run-time.
  Numba support will be added as soon as Numba 0.49 is released. (:pr:`685`).

Bugfixes
--------

* A large number of usability improvements (:pr:`622,639,641,642,659,666,690,699,700,704`).
  Thanks and credit to many new contributors from GSoC!
* Fixed the buggy "enable Direct I/O" checkbox of the RAW dataset and
  handle unsupported operating systems gracefully. (:pr:`696,659`)


Documentation
-------------

* Added screenshots and description of ROI
  and stddev features in usage docs (:pr:`669`)
* Improved instructions for installing LiberTEM
  (general: :pr:`664`; for development: :pr:`598`)
* Add information for downloading and generating sample
  datasets: :ref:`sample data`. (:pr:`650,670,707`)

Obsolescence
------------

* Parameters :code:`crop_detector_to` and :code:`detector_size_raw` of
  :class:`libertem.io.dataset.raw.RawFileDataSet` are deprecated and will be removed
  after 0.6.0. Please specify :code:`detector_size` instead or use a specialized DataSet, for example for EMPAD.
* :class:`libertem.udf.feature_vector_maker.FeatureVecMakerUDF` is deprecated
  and will be removed in 0.6.0. Use :class:`~libertem.udf.masks.ApplyMasksUDF`
  with a sparse stack of single pixel masks or a stack generated by
  :meth:`libertem_blobfinder.common.patterns.feature_vector` instead.
  (:pr:`618`)

Misc
----

* Clustering analysis

  + Use a connectivity matrix to only cluster neighboring pixels,
    reducing memory footprint while improving speed and quality (:pr:`618`).
  + Use faster :class:`~libertem.udf.masks.ApplyMasksUDF` to generate feature
    vector (:pr:`618`).

* :class:`~libertem.udf.stddev.StdDevUDF`

  + About 10x speed-up for large frames (:pr:`625,640`)
  + Rename result buffers of :class:`~libertem.udf.stddev.StdDevUDF`,
    :meth:`~libertem.udf.stddev.run_stddev` and
    :meth:`~libertem.udf.stddev.consolidate_result` from :code:`'sum_frame'` to
    :code:`'sum'`, :code:`'num_frame'` to :code:`'num_frames'` (:pr:`640`)
  + Resolve ambiguity between variance and sum of variances in result buffer names of
    :class:`~libertem.udf.stddev.StdDevUDF`,
    :meth:`~libertem.udf.stddev.run_stddev` and
    :meth:`~libertem.udf.stddev.consolidate_result`. (:pr:`640`)

* LiberTEM works with Python 3.8 for experimental use. A context using a remote Dask.Distributed cluster
  can lead to lock-ups or errors with Python 3.8. The default local Dask.Distributed context works.
* Improve performance with large tiles. (:pr:`649`)
* :class:`~libertem.udf.sum.SumUDF` moved to the :mod:`libertem.udf` folder (:pr:`613`).
* Make sure the signal dimension of result buffer slices can be
  flattened without creating an implicit copy (:pr:`738`, :issue:`739`)

Many thanks to the contributors to this release: :user:`AnandBaburajan`,
:user:`twentyse7en`, :user:`sayandip18`, :user:`bdalevin`, :user:`saisunku`,
:user:`Iamshankhadeep`, :user:`abiB27`, :user:`sk1p`, :user:`uellue`

.. _`v0-4-1`:

0.4.1 / 2020-02-18
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.3674003.svg
   :target: https://doi.org/10.5281/zenodo.3674003

This is a bugfix release, mainly constraining the :code:`msgpack` dependency,
as distributed is not compatible to version 1.0 yet. It also contains
important fixes in the HDF5 dataset.

Bugfixes
--------

* Fix HDF5 with automatic tileshape (:pr:`608`)
* Fix reading from HDF5 with roi beyond the first partition (:pr:`606`)
* Add version constraint on msgpack

.. _`v0-4-0`:

0.4.0 / 2020-02-13
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.3666686.svg
   :target: https://doi.org/10.5281/zenodo.3666686

The main points of this release are the :ref:`job deprecation` and restructuring
of our packaging, namely :ref:`extracting the blobfinder module <restructuring-0-4>`.

New features
------------

* :code:`dtype` support for UDFs :ref:`udf dtype` (:issue:`549`, :pr:`550`)
* Dismiss error messages via keyboard: allows pressing the escape key to close all currently open error messages (:issue:`437`)
* ROI doesn't have any effect if in pick mode, so we hide the dropdown in that case (:issue:`511`)
* Make tileshape parameter of HDF5 DataSet optional (:pr:`578`)
* Open browser after starting the server. Enabled by default, can be disabled using --no-browser (:issue:`81`, :pr:`580`)
* Implement :class:`libertem.udf.masks.ApplyMasksUDF` as a replacement of ApplyMasksJob (:issue:`549`, :pr:`550`)
* Implement :class:`libertem.udf.raw.PickUDF` as a replacement of PickFrameJob (:issue:`549`, :pr:`550`)
 
Bug fixes
---------

* Fix FRMS6 in a distributed setting. We now make sure to only do I/O in methods that are running on worker nodes (:pr:`531`).
* Fixed loading of nD HDF5 files. Previously the HDF5 DataSet was hardcoded for
  4D data - now, arbitraty dimensions should be supported (:issue:`574`, :pr:`567`)
* Fix :code:`DaskJobExecutor.run_each_host`. Need to pass :code:`pure=False` to ensure multiple runs of the function (:pr:`528`).

Obsolescence
------------

* Because HDFS support is right now not tested (and to my knowledge also not
  used) and the upstream :code:`hdfs3` project is not actively maintained, remove
  support for HDFS. :code:`ClusterDataSet` or :code:`CachedDataSet` should be used
  instead (:issue:`38`, :pr:`534`).

Misc
----

* Depend on distributed>=2.2.0 because of an API change. (:pr:`577`)
* All analyses ported from Job to UDF back-end. The Job-related code remains for now for comparison purposes (:issue:`549`, :pr:`550`)

.. _`job deprecation`:

Job API deprecation
-------------------

The original Job API of LiberTEM is superseded by the new :ref:`user-defined
functions` API with release 0.4.0. See :issue:`549` for a detailed overview
of the changes. The UDF API brings the following advantages:

* Support for regions of interest (ROIs).
* Easier to implement, extend and re-use UDFs compared to Jobs.
* Clean separation between back-end implementation details and application-specific code.
* Facilities to implement non-trivial operations, see :ref:`advanced udf`.
* Performance is at least on par.

For that reason, the Job API has become obsolete. The existing public
interfaces, namely :meth:`libertem.api.Context.create_mask_job` and
:meth:`libertem.api.Context.create_pick_job`, will be supported in LiberTEM for
two more releases after 0.4.0, i.e. including 0.6.0. Using the Job API will
trigger deprecation warnings starting with this release. The new
:class:`~libertem.udf.masks.ApplyMasksUDF` replaces
:class:`~libertem.job.masks.ApplyMasksJob`, and :class:`~libertem.udf.raw.PickUDF`
replaces :class:`~libertem.job.raw.PickFrameJob`.

The Analysis classes that relied on the Job API as a back-end are already ported
to the corresponding UDF back-end. The new back-end may lead to minor
differences in behavior, such as a change of returned dtype. The legacy code for
using a Job back-end will remain until 0.6.0 and can be activated during the
transition period by setting :code:`analysis.TYPE = 'JOB'` before running.

From :class:`~libertem.job.masks.ApplyMasksJob` to :class:`~libertem.udf.masks.ApplyMasksUDF`
.............................................................................................

Main differences:

* :class:`~libertem.udf.masks.ApplyMasksUDF` returns the result with the first
  axes being the dataset's navigation axes. The last dimension is the mask
  index. :class:`~libertem.job.masks.ApplyMasksJob` used to return transposed
  data with flattened navigation dimension.
* Like all UDFs, running an :class:`~libertem.udf.masks.ApplyMasksUDF` returns a
  dictionary. The result data is accessible with key :code:`'intensity'` as a
  :class:`~libertem.common.buffers.BufferWrapper` object.
* ROIs are supported now, like in all UDFs.

.. testsetup:: jobdeprecation

    import numpy as np
    import libertem
    import matplotlib.pyplot as plt

    def all_ones():
        return np.ones((16, 16))

    def single_pixel():
        buf = np.zeros((16, 16))
        buf[7, 7] = 1
        return buf

Previously with :class:`~libertem.job.masks.ApplyMasksJob`:

.. code-block:: python

    # Deprecated!
    mask_job = ctx.create_mask_job(
      factories=[all_ones, single_pixel],
      dataset=dataset
    )
    mask_job_result = ctx.run(mask_job)

    plt.imshow(mask_job_result[0].reshape(dataset.shape.nav))

Now with :class:`~libertem.udf.masks.ApplyMasksUDF`:

.. testcode:: jobdeprecation

    mask_udf = libertem.udf.masks.ApplyMasksUDF(
      mask_factories=[all_ones, single_pixel]
    )
    mask_udf_result = ctx.run_udf(dataset=dataset, udf=mask_udf)

    plt.imshow(mask_udf_result['intensity'].data[..., 0])

From :class:`~libertem.job.raw.PickFrameJob` to :class:`~libertem.udf.raw.PickUDF`
..................................................................................

:class:`~libertem.job.raw.PickFrameJob` allowed to pick arbitrary contiguous
slices in both navigation and signal dimension. In practice, however, it was
mostly used to extract single complete frames.
:class:`~libertem.udf.raw.PickUDF` allows to pick the *complete* signal
dimension from an arbitrary non-contiguous region of interest in navigation
space by specifying a ROI.

If necessary, more complex subsets of a dataset can be extracted by constructing
a suitable subset of an identity matrix for the signal dimension and using it
with ApplyMasksUDF and the appropriate ROI for the navigation dimension.
Alternatively, it is now easily possible to implement a custom UDF for this
purpose. Performing the complete processing through an UDF on the worker nodes
instead of loading the data to the central node may be a viable alternative as
well.

:class:`~libertem.udf.raw.PickUDF` now returns data in the native :code:`dtype`
of the dataset. Previously, :class:`~libertem.job.raw.PickFrameJob` converted to
floats.

Using :meth:`libertem.api.Context.create_pick_analysis` continues to be the
recommended convenience function to pick single frames.

.. _`restructuring-0-4`:

Restructuring into sub-packages
-------------------------------

We are currently restructuring LiberTEM into packages that can be installed and
used independently, see :issue:`261`. This will be a longer process and changes
the import locations.

* `Blobfinder <https://libertem.github.io/LiberTEM-blobfinder/>`_ is the first
  module separated in 0.4.0.
* See :ref:`packages` for a current overview of sub-packages.

For a transition period, importing from the previous locations is supported but
will trigger a :code:`FutureWarning`. See :ref:`show warnings` on how to
activate deprecation warning messages, which is strongly recommended while the
restructuring is ongoing.

.. _`v0-3-0`:

0.3.0 / 2019-12-12
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.3572855.svg
   :target: https://doi.org/10.5281/zenodo.3572855

New features
------------

* Make OOP based composition and subclassing easier for
  :class:`~libertem.udf.blobfinder.correlation.CorrelationUDF` (:pr:`466`)
* Introduce plain circular match pattern :class:`~libertem.udf.blobfinder.patterns.Circular` (:pr:`469`)
* Distributed sharded dataset :class:`~libertem.io.dataset.cluster.ClusterDataSet` (:issue:`136`, :issue:`457`)
* Support for caching data sets :class:`~libertem.io.dataset.cached.CachedDataSet`
  from slower storage (NFS, spinning metal) on fast local storage (:pr:`471`)
* :ref:`Clustering` analysis (:pr:`401,408` by :user:`kruzaeva`).
* :class:`libertem.io.dataset.dm.DMDataSet` implementation based on ncempy (:pr:`497`)

  * Adds a new :meth:`~libertem.common.executor.JobExecutor.map` executor primitive. Used to concurrently
    read the metadata for DM3/DM4 files on initialization.
  * Note: no support for the web GUI yet, as the naming patterns for DM file series varies wildly. Needs
    changes in the file dialog.

* Speed up of up to 150x for correlation-based peak refinement in
  :mod:`libertem.udf.blobfinder.correlation` with a Numba-based pipeline (:pr:`468`)
* Introduce :class:`~libertem.udf.blobfinder.correlation.FullFrameCorrelationUDF` which
  correlates a large number (several hundred) of small peaks (10x10) on small
  frames (256x256) faster than
  :class:`~libertem.udf.blobfinder.correlation.FastCorrelationUDF` and
  :class:`~libertem.udf.blobfinder.correlation.SparseCorrelationUDF` (:pr:`468`)
* Introduce :class:`~libertem.udf.UDFPreprocessMixin` (:pr:`464`)
* Implement iterator over :class:`~libertem.analysis.base.AnalysisResultSet` (:pr:`496`)
* Add hologram simulation
  :func:`libertem.utils.generate.hologram_frame` (:pr:`475`)
* Implement Hologram reconstruction UDF
  :class:`libertem.udf.holography.HoloReconstructUDF` (:pr:`475`)

Bug fixes
---------

* Improved error and validation handling when opening files with GUI (:issue:`433,442`)
* Clean-up and improvements of :class:`libertem.analysis.fullmatch.FullMatcher` (:pr:`463`)
* Ensure that RAW dataset sizes are calculated as int64 to avoid integer overflows (:pr:`495`, :issue:`493`)
* Resolve shape mismatch issue and simplify dominant order calculation in Radial Fourier Analysis (:pr:`502`)
* Actually pass the :code:`enable_direct` parameter from web API to the DataSet

Documentation
-------------

* Created :ref:`authorship` (:pr:`460,483`)
* Change management process (:issue:`443`, :pr:`451,453`)
* Documentation for :ref:`crystallinity map` and :ref:`clustering` analysis (:pr:`408` by :user:`kruzaeva`)
* Instructions for profiling slow tests (:issue:`447`, :pr:`448`)
* Improve API reference on Analysis results (:issue:`494`, :pr:`496`)
* Restructure and update the API reference for a number of UDFs and
  other application-specific code (:issue:`503`, :pr:`507,508`)

Obsolescence
------------

* The Job interface is planned to be replaced with an implementation based on UDFs in one of the upcoming releases.

Misc
----

* Split up the blobfinder code between several files to reduce file size (:pr:`468`)

.. _`v0-2-2`:

0.2.2 / 2019-10-14
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.3489385.svg
   :target: https://doi.org/10.5281/zenodo.3489385

Point release to fix a number of minor issues, most notably PR :pr:`439` that
should have been merged for version 0.2.

Bug fixes
---------

* Trigger a timeout when guessing parameters for HDF5 takes too long (:issue:`440` , :pr:`449`)
* Slightly improved error and validation handling when opening files with GUI (:commit:`ec74c1346d93eff58d9e2201a7ead5af7aa7cf44`)
* Recognize BLO file type (:issue:`432`)
* Fixed a glitch where negative peak elevations were possible (:pr:`446`)
* Update examples to match 0.2 release (:pr:`439`)

.. _`v0-2-1`:

0.2.1 / 2019-10-07
##################

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.3474968.svg
   :target: https://doi.org/10.5281/zenodo.3474968

Point release to fix a bug in the Zenodo upload for production releases.

.. _`v0-2-0`:

0.2.0 / 2019-10-07
##################

This release constitutes a major update after almost a year of development.
Systematic change management starts with this release.

This is the `release message <https://groups.google.com/g/libertem/c/p7MVoVqXOs0/m/vP_tu6K7CwAJ>`_:

User-defined functions
----------------------

LiberTEM 0.2 offers a new API to define a wide range of user-defined reduction
functions (UDFs) on distributed data. The interface and implementation offers a
number of unique features:

* Reductions are defined as functions that are executed on subsets of the data.
  That means they are equally suitable for distributed computing, for interactive
  display of results from a progressing calculation, and for handling live data¹.
* Interfaces adapted to both simple and complex use cases: From a simple map()
  functionality to complex multi-stage reductions.
* Rich options to define input and output data for the reduction functions, which
  helps to implement non-trivial operations efficiently within a single pass over
  the input data.
* Composition and extension through object oriented programming
* Interfaces that allow highly efficient processing: locality of reference, cache
  efficiency, memory handling

Introduction: https://libertem.github.io/LiberTEM/udf.html

Advanced features: https://libertem.github.io/LiberTEM/udf/advanced.html

A big shoutout to Alex (:user:`sk1p`) who developed it! 🏆

¹User-defined functions will work on live data without modification as soon as
LiberTEM implements back-end support for live data, expected in 2020.

Support for 4D STEM applications
--------------------------------

In parallel to the UDF interface, we have implemented a number of applications
that make use of the new facilities:

* Correlation-based peak finding and refinement for CBED (credit: Karina Ruzaeva :user:`kruzaeva`)
* Strain mapping
* Clustering
* Fluctuation EM
* Radial Fourier Series (advanced Fluctuation EM)

More details and examples: https://libertem.github.io/LiberTEM/applications.html

Extended documentation
----------------------

We have greatly improved the coverage of our documentation:
https://libertem.github.io/LiberTEM/index.html#documentation

Fully automated release pipeline
--------------------------------

Alex (:user:`sk1p`) invested a great deal of effort into fully automating our release
process. From now on, we will be able to release more often, including service
releases. 🚀

Basic dask.distributed array integration
----------------------------------------

LiberTEM can generate efficient dask.distributed arrays from all supported
dataset types with this release. That means it should be possible to use our high-performance file
readers in applications outside of LiberTEM.

File formats
------------

Support for various file formats has improved. More details:
https://libertem.github.io/LiberTEM/formats.html

.. _`v0-1-0`:

0.1.0 / 2018-11-06
##################

Initial release of a minimum viable product and proof of concept.

Support for applying masks with high throughput on distributed systems with
interactive web GUI display and scripting capability.
