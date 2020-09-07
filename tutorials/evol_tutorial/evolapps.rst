.. _evolapps:

Evol Application
===============================================================================

Evol applications have similar fuctionality as the python API. We can ``search``
Pfam, ``fetch`` from Pfam and also ``refine MSA``, ``merge`` two or more MSA
and calculate ``conservation`` and ``coevolution`` properties and also
``rankorder`` results from mutual information to get top-ranking pairs.

All ``evol`` functions and their options can be obtained using the -h option.
We should be in /prody/scripts directory to run the following commands::

    evol -h
    evol search -h
    evol search 2W5IB
    evol fetch PF00074

Using the above we can search and fetch MSA. Next we can refine the MSA::

    evol refine -h
    evol refine PF00074_full.sth -l RNAS1_BOVIN -s 0.98 -r 0.8

Next we can calculate conservation using shannon entropy and coevolution using
mutual information with correction and also save the plots.::

    evol conserv PF00074_full_refined.sth -S
    evol coevol PF00074_full_refined.sth -S -F png -c apc --cmin 0.0

We can rank order the residues with highest covariance and apply filters like
reporting only those pairs that are at a separation of at least 5 residues
sequentially or are 15 Ang apart in structure. The residues may be numbered
based on a PDB file, such as the one we made earlier::

    evol rankorder -h
    evol rankorder PF00074_full_refined_mutinfo_corr_apc.txt -q 5 -p 2W5IB_3-121.pdb
    evol rankorder PF00074_full_refined_mutinfo_corr_apc.txt -u -t 15 -p 2W5IB_3-121.pdb

We can also provide a PDB ID and chain if we provide an MSA to match it against::

    evol rankorder PF00074_full_refined_mutinfo_corr_apc.txt -q 5 -p 2W5IB -m PF00074_full_refined.sth

Or even use the MSA directly if it has start and end in the labels::

    evol rankorder PF00074_full_refined_mutinfo_corr_apc.txt -q 5 -m PF00074_full_refined.sth -l RNAS1_BOVIN
    