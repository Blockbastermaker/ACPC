ACPC v1.2
=========

![Logo](ACPC_logo.png?raw=true)

Don't hesitate to contact Francois Berenger in case you have problems running
the software or discover a bug.

Installation
------------

To install ACPC, you first need to install and configure
the OCaml package manager (OPAM). Cf. http://opam.ocaml.org/

Once this is done, you can automatically install ACPC
with the following command:

    $ opam install acpc

If you want a specific version:

    $ opam install acpc.1.1

or

    $ opam install acpc.1.2

Recommended usage (protocol)
----------------------------

ACPC was designed to be rotation and translation invariant.
ACPC is not invariant to the conformer of a molecule,
neither to the charge model that was used to assign partial charges to it.

The following protocol has been validated:
the query molecule(s) _AND_ the database to screen must be prepared in
the same way. The same software with same parameters must be used
to assign partial charges and generate conformers for _ALL_ molecules.

The recommended charge models are:
MOE's MMFF94x or as a fallback Open Babel's Gasteiger.

Reading the related research article is highly recommended:
"A rotation-translation invariant molecular descriptor of
partial charges and its use in ligand-based virtual screening".
Citing the article is kindly asked from users of the software.

ACPC installs several executables, all prefixed by "acpc_".
'acpc' is the basic program to rank-order a database of molecules
against a query molecule.
'acpc_par' is almost the same program but allows to use several CPUs
at the same time.
'acpc_consrank' allows to do consensus ranking of *.scores files
generated by 'acpc' with the -v option.
'acpc_scorer' allows to merge *.scores files by giving a weight to
each feature space.
If ACPC is working on MOL2 files, then it is working in the electrostatic
space. If working on PQR files, then it is working in the steric space.
The PL files (*.pl) are for the hydrophobic space, cf. bin/to_pl_file.sh
and https://github.com/UnixJunkie/openbabel/tree/logP_contrib_per_heavy_atom
to produce PL files.

ACPC 1.1 was validated on this dataset:
http://www.riken.jp/zhangiru/software/DUD_ACPC_1.0_validation.tar.xz
(MOL2 files).

ACPC 1.2 was validated on this dataset:
http://www.riken.jp/zhangiru/software/DUDE_ACPC_1.2_validation.tar.bz2
(MOL2, PQR and PL files).

'acpc_ertool' computes enrichment rate of score-label files.
'acpc_auctool' computes AUC of score-label files.
'acpc_codec' can preprocess MOL2/PQR/PL files to store them in *.bin files.

Examples
--------

1) one query on a database

    $ acpc -q query.mol2 -db database.mol2

2) same but storing the top 10 molecules

    $ acpc -q query.mol2 -db database.mol2 -top 10 -o ten_best.mol2

4) separate each molecule from a mol2 file into separate files

    $ acpc_mol2tool some_molecules.mol2

5) pre-parse a MOL2 file (*.bin files are read faster by ACPC)

    $ acpc_codec -i database.mol2 -o database.mol2.bin

Get some help
-------------

    $ acpc -h
    Example: acpc -q query.mol2 -db database.mol2[.bin]
      -a float                   kernel parameter (default depends on considered feature space and was optimized on DUD-E)
      -brbn                      break the query molecule along its rotatable bonds
      -db db.{mol2|pqr|pl}[.bin] database
      -er x                      enrichment rate parameter; e.g. 0.01 --> ER_1%
      -nopp                      don't rm duplicate molecules (based on names)
      -q query.{mol2|pqr|pl}     query
      -scan out_file             scan delta AUC per atom in the query and create a new query molecule
      -top int                   number of top scoring molecules to output (only works for MOL2 files)
      -o out_file                output file for -top
      -v                         dump scores out
      -help                      Display this list of options
      --help                     Display this list of options

    $ acpc_par -h
    Example: acpc_par -np 2 -q query.mol2 -db database.mol2
      -a float                   kernel parameter (default was optimized on DUD-E and depends on considered feature space)
      -db db.{mol2|pqr|pl}[.bin] molecules database
      -o output_file             to store name, index and scores (default is stdout)
      -q query.{mol2|pqr|pl}     query molecule
      -np int                    max number of cores to use
      -help                      Display this list of options
      --help                     Display this list of options

WARNING
-------

Don't do two queries at the same time on the same computer or on top of NFS
with a same query molecule file (-q SOME_QUERY.mol2), this may overwrite
result files in a strange way (SOME_QUERY.scores).
