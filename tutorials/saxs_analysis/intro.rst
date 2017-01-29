Introduction
===============================================================================

This tutorial shows how to analyze solution structure of proteins starting from
a known protein structure and an experimental  Small Angle X-ray Scattering
(SAXS) profile. 


Required Programs
-------------------------------------------------------------------------------

Latest version of ProDy_ is required.

Recommended Programs
-------------------------------------------------------------------------------

List any recommended programs, such as NAMD_, etc.

.. _NAMD: http://www.ks.uiuc.edu/Research/namd/

Getting Started
-------------------------------------------------------------------------------

To follow this tutorial, you will need the following files:

.. files.txt will be automatically generated

.. literalinclude:: files.txt


We recommend that you will follow this tutorial by typing commands in an
IPython session, e.g.::

  $ ipython

or with pylab environment::

  $ ipython --pylab


First, we will make necessary imports from ProDy and Matplotlib
packages.

.. ipython:: python

   from prody import *
   from pylab import *
   ion()

We have included these imports in every part of the tutorial, so that
code copied from the online pages is complete. You do not need to repeat
imports in the same Python session.
