.. _pca-nmr:

NMR Models
===============================================================================


This example shows how to perform principal component analysis (PCA) of an
ensemble of NMR models. The protein of interest is :wiki:`ubiquitin`, and
for illustration puposes, we will repeat the calculations for the ensemble
of ubiquitin models that were analyzed in [AB09]_.

A :class:`.PCA` object that stores the covariance matrix and principal modes that
describe the dominant changes in the dataset will be obtained. :class:`.PCA`
and principal modes (:class:`.Mode`) can be used as input to functions in
:mod:`.dynamics` module for further analysis.


Notes
-------------------------------------------------------------------------------

Note that this example is only slightly different from that in the :ref:`tutorial`. 
Also, note that this example applies to any PDB file that contains multiple
models.

Prepare ensemble
-------------------------------------------------------------------------------

We start by importing everything from the ProDy package:

.. ipython:: python

   from prody import *
   from pylab import *
   ion()


We parse only Cα atoms using :func:`.parsePDB` (note that it is possible to
repeat this calculation for all atoms):

.. ipython:: python

   ubi = parsePDB('2k39', subset='ca')

We use residues 1 to 70, as residues 71 to 76 are very mobile and including
them skews the results.

.. ipython:: python

   ubi = ubi.select('resnum < 71').copy()

.. ipython:: python

   ensemble = Ensemble('Ubiquitin NMR ensemble')
   ensemble.setCoords( ubi.getCoords() )

Then, we add all of the coordinate sets to the ensemble, and perform an
iterative superposition:

.. ipython:: python

   ensemble.addCoordset( ubi.getCoordsets() )
   ensemble.iterpose()

We can then make a new Atomgroup object where we replace the coordinates 
with the ones from the ensemble as follows:

.. ipython:: python

   ubi_copy = ubi.copy()
   ubi_copy.delCoordset(range(ubi_copy.numCoordsets()))
   ubi_copy.addCoordset(ensemble.getCoordsets())

PCA calculations
-------------------------------------------------------------------------------

Performing :class:`.PCA` is only three lines of code:

.. ipython:: python

   pca = PCA('Ubiquitin')
   pca.buildCovariance(ensemble)
   pca.calcModes()
   repr(pca)


**Faster method**

Principal modes can be calculated faster using singular value decomposition:

.. ipython:: python

   svd = PCA('Ubiquitin')
   svd.performSVD(ensemble)

For heterogeneous NMR datasets, both methods yields identical results:

.. ipython:: python

   abs(svd.getEigvals()[:20] - pca.getEigvals()).max()
   abs(calcOverlap(pca, svd).diagonal()[:20]).min()

Write NMD file
-------------------------------------------------------------------------------

Write principal modes into an :ref:`nmd-format` file for NMWiz using
:func:`.writeNMD` function:

.. ipython:: python

   writeNMD('ubi_pca.nmd', pca[:3], ubi)


Print data
-------------------------------------------------------------------------------
Let's print fraction of variance for top ranking 4 PCs (listed in Table S3):

.. ipython:: python

   for mode in pca[:4]:
       print calcFractVariance(mode).round(3)


Compare with ANM results
-------------------------------------------------------------------------------

We set the active coordinate set of ubi_copy to model 79, which is the one 
that is closest to the mean structure (note that indices start from 0 in Python 
so we give it 78).
Then, we perform ANM calculations using :func:`.calcANM` for the active
coordset:

.. ipython:: python

   ubi_copy.setACSIndex(78)
   anm, temp = calcANM(ubi_copy)
   anm.setTitle('Ubiquitin')

We calculate overlaps between ANM and PCA modes (presented in Table 1).
:func:`.printOverlapTable` function is handy to print a formatted overlap
table:

.. ipython:: python

   printOverlapTable(pca[:4], anm[:4])
