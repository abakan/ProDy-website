.. _signdy-class:

Classification using sequence, structure and dynamics distances
===============================================================================

We can compare the dynamics of individual proteins using the spectral overlap, 
also known as covariance overlap. The arccosine of this value provides a distance 
metric. Calculating this for all pairs in a mode ensemble gives us the spectral distance 
matrix, which can be used to calculate a dynamics-based "phylogenetic" tree. This can be 
compared against matrices and trees calculated using sequence and structure distances.

Again here are the imports if you need them.

.. ipython:: python

    from prody import *
    from pylab import *
    ion()

Load PDBEnsemble and ModeEnsemble
-------------------------------------------------------------------------------

We first load the :class:`.PDBEnsemble`:

.. ipython:: python

    ens = loadEnsemble('LeuT.ens.npz')

Then we load the :class:`.ModeEnsemble`:

.. ipython:: python

    gnms = loadModeEnsemble('LeuT.modeens.npz')

Spectral overlap and distance
-------------------------------------------------------------------------------

We calculate the spectral overlap matrix, calculate a tree from its arccosine and 
reorder the spectral overlap matrix using the tree as follows: 

.. ipython:: python

    so_matrix = calcEnsembleSpectralOverlaps(gnms[:,:1])
    labels = gnms.getLabels()
    so_tree = calcTree(names=labels, 
                       distance_matrix=arccos(so_matrix), 
                       method='upgma')

    reordered_so, new_so_indices = reorderMatrix(so_matrix, 
                                                 so_tree, 
                                                 names=labels)


We can show the original and reordered spectral distance matrices and the tree as follows.
:func:`.showTree` has multiple *format* options. Here we show the output of using *plt*.
This layout allows us to directly compare against the output from :func:`.showMatrix`
using the option *origin='upper'*.

.. ipython:: python

    @savefig ens_gnms_so_matrix.png width=4in
    showMatrix(arccos(so_matrix), origin='upper')
	close()
	
    @savefig ens_gnms_so_tree.png width=4in
    showTree(so_tree, format='plt')
	close()
	
    @savefig ens_gnms_so_reordered_so_matrix.png width=4in
    showMatrix(arccos(reordered_so), origin='upper')
	close()


Sequence and structural distances
-------------------------------------------------------------------------------

The sequence distance is given by the Hamming distance, which is calculated by 
subtracting the percentage identity (fraction) from 1, and the structural distance 
is the RMSD. We can also calculate and show the matrices and trees for these from 
the PDB ensemble.

.. ipython:: python

    seqid_matrix = buildSeqidMatrix(ens.getMSA())
    seqd_matrix = 1. - seqid_matrix
    @savefig ens_gnms_seqd_matrix.png width=4in
    showMatrix(seqd_matrix, origin='upper')
	close()

    # plt.figure();
    seqd_tree = calcTree(names=labels, 
                         distance_matrix=seqd_matrix, 
                         method='upgma')
    @savefig ens_gnms_seqd_tree.png width=4in
    showTree(seqd_tree, format='plt')
	close()

    reordered_seqd, indices = reorderMatrix(seqd_matrix, seqd_tree, 
                                            names=labels)
    #plt.figure();
    @savefig ens_gnms_seqd_reordered_seqd_matrix.png width=4in
    showMatrix(reordered_seqd, origin='upper');

.. ipython:: python

    rmsd_matrix = ens.getRMSDs(pairwise=True)
    @savefig ens_gnms_rmsd_matrix.png width=4in
    showMatrix(rmsd_matrix, origin='upper')
	close()

    # plt.figure()
    rmsd_tree = calcTree(names=labels, 
                         distance_matrix=rmsd_matrix, 
                         method='upgma')
    @savefig ens_gnms_rmsd_tree.png width=4in
    showTree(rmsd_tree, format='plt')
	close()

    # plt.figure()
    reordered_rmsd, indices = reorderMatrix(rmsd_matrix, rmsd_tree, 
                                            names=labels)
    @savefig ens_gnms_rmsd_reordered_rmsd_matrix.png width=4in
    showMatrix(reordered_rmsd, origin='upper')
	close()


Comparing sequence, structural and dynamic classifications
-------------------------------------------------------------------------------

We can reorder the seqd and sod matrices by the RMSD tree too to compare them:

.. ipython:: python

    reordered_seqd, indices = reorderMatrix(seqd_matrix, rmsd_tree, 
                                            names=labels)
    reordered_sod, indices = reorderMatrix(so_matrix, rmsd_tree, 
                                           names=labels)

.. ipython:: python

    @savefig ens_gnms_rmsd_reordered_seqd_matrix.png width=4in
    showMatrix(reordered_seqd, origin='upper')
	close()
	
    @savefig ens_gnms_rmsd_reordered_rmsd_matrix.png width=4in
    showMatrix(reordered_rmsd, origin='upper')
	close()
	
    @savefig ens_gnms_rmsd_reordered_sod_matrix.png width=4in
    showMatrix(arccos(reordered_sod), origin='upper')
	close()


This analysis is quite sensitive to how many modes are used. As the number of modes approaches the full number, 
the dynamic distance order approaches the RMSD order. With smaller numbers, we see finer distinctions. This is 
particularly clear in the current case where we used just one mode.