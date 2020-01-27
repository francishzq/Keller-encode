# Keller-encode
Code related to Keller's conjecture

## Quick Start Guide
The wrapper Python script requires the `python-igraph` package, you
can install it with

```bash
$ pip3 install --user python-igraph
```

To compile all the necessary tools, run

```bash
$ make all
```

To generate the files and proofs for the cases s = {3,4,6}, special
targets can be invoked, e.g.:

```bash
$ make s3
```

## Compilation Details
One can specify the location of the Boost installation in order to
compile the `pprsearch` tool:

```bash
$ make tools/pprsearch/pprsearch BOOST_ROOT=/path/to/boost
```

## Toolchain Details

The verification targets s<n> for n∊{3,4,6} run a number of steps
verifying the addition of symmetry breaking clauses to the encoding of
the clique problem for the corresponding Keller graphs. These steps
are also make targets which can be invoked separately:

* s<n>-python: run the Keller.py script and generate the files for the
  verification step. This step generates several CNF and DRAT files
  numbered in sequence from the initial file s<n>.0.cnf, going through
  intermediate CNF formulas with some symmetry breaking clauses added,
  up to s<n>.cnf which is the final version of the CNF. This last
  version contains all the symmetry breaking clauses and is the
  formula passed to the SAT solver. Along with each formula, a DRAT
  file justifying the addition of the next batch of symmetry breaking
  clauses is generated. This target also creates a file s<n>.dnf which
  is a DNFtautology containing all the assignments that need to be
  tried by a SAT solver in order to prove the formula
  unsatisfiable. The output CNF and DNF files of this call are
  recorded in the outputs directory.
* s<n>-drat-trim: This target verifies the DRAT files generated by the
  s<n>-python target. The verification is sequential.
* s<n>-tautology: This target verifies that the DNF file output by
  s<n>-python is indeed a tautology.

Of these 3 steps of the verification process, the first two can take a
significant amount of time (days or even weeks). In order to speed up
the verification process, we provide a set of scripts that allow for
the generation and checking of the proof files to happen in parallel.

## ACL2 Verified Checker

The proofs of unsatisfiability can be verified using
drat-trim. Alternatively, you can use the ACL2 verified checker for
these proofs. To build the ACL2 checker you will need a Common Lisp
implementation. This build has been tested with Steel Bank Common Lisp
(sbcl) and Clozure (ccl).

Targets are provided for getting the sources, certifying the checker
and building a binary for it:

```bash
$ make acl2-checker
```

The default Common Lisp implementation is sbcl. You can specify your
own implementation:

```bash
$ make acl2-checker LISP=ccl
```

You can test this installation with the provided examples:

```bash
$ acl2-8.2/books/projects/sat/lrat/cube/run.sh examples/s3-12345.cnf examples/s3-12345.clrat examples/s3-12345-out.cnf
```
