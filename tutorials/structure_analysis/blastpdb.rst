.. _blastpdb:


Blast Search PDB
===============================================================================

This example demonstrates how to use Protein Data Bank blast search function,
:func:`.blastPDB`.

:func:`.blastPDB` is a utility function which can be used to check if
structures matching a sequence exist in PDB or to identify a set of related
structures for :ref:`pca`.

We will used amino acid sequence of a protein, e.g.
``ASFPVEILPFLYLGCAKDSTNLDVLEEFGIKYILNVTPNLPNLF...YDIVKMKKSNISPNFNFMGQLLDFERTL``

The :func:`.blastPDB` function accepts sequence as a Python :func:`str`.

Output will be :class:`.PDBBlastRecord` instance that stores PDB hits and
returns to the user those sharing sequence identity above a user specified
value.

Blast search
-------------------------------------------------------------------------------

We start by importing everything from the ProDy package:

.. ipython:: python

   from prody import *

Let's search for structures similar to that of MKP-3, using its sequence:

.. ipython::
   :verbatim:

   In [1]: blast_record = blastPDB('''ASFPVEILPFLYLGCAKDSTNLDVLEEFGIKYILNVTPNL
      ...: PNLFENAGEFKYKQIPISDHWSQNLSQFFPEAISFIDEAR
      ...: GKNCGVLVHSLAGISRSVTVTVAYLMQKLNLSMNDAYDIV
      ...: KMKKSNISPNFNFMGQLLDFERTL''')
      ...:

:func:`.blastPDB` function returns a :class:`.PDBBlastRecord`. It is a good
practice to save this record on disk, as NCBI may not respond to repeated
searches for the same sequence. We can do this using Python standard library
:mod:`pickle` as follows:

.. ipython:: python

   import pickle

Record is save using :func:`~pickle.dump` function into an open file:

.. ipython::
   :verbatim:

   In [10]: pickle.dump(blast_record, open('mkp3_blast_record.pkl', 'wb'))


Then, it can be loaded using :func:`~pickle.load` function:

.. ipython:: python

   blast_record = pickle.load(open('mkp3_blast_record.pkl', 'rb'))


Best match
-------------------------------------------------------------------------------

To get the best match, :meth:`.PDBBlastRecord.getBest` method can be used:

.. ipython:: python

   best = blast_record.getBest()
   best['pdb_id']
   best['percent_identity']


PDB hits
-------------------------------------------------------------------------------

.. ipython:: python

   hits = blast_record.getHits(percent_identity=90, percent_overlap=70)
   list(hits)

This results in only MKP-3 itself, since percent_identity argument was set
to 90:

.. ipython:: python

   hits = blast_record.getHits(percent_identity=50)
   list(hits)
   hits = blast_record.getHits(percent_identity=40)
   list(hits)


This resulted in more hits, including structures of MKP-2, MKP-4, and MKP-5
More information on a hit can be obtained as follows:

.. ipython:: python

   hits['1zzw']['percent_identity']
   hits['1zzw']['align-len']
   hits['1zzw']['identity']

To obtain all hits, simply run the function without specifying parameters:

.. ipython:: python

   all_hits = blast_record.getHits()

Download hits
-------------------------------------------------------------------------------

PDB hits can be downloaded using :func:`.fetchPDB` function::

  filenames = fetchPDB(hits.keys())
  filenames
