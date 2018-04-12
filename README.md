# SciTests

Provide tests and test runner harnesses to test scientific artifacts.
The idea for `scitests` is to provide a collection of fingerprints and tests for different
types of data and fields of study, and test harnesses so they could be used
to compare and test results for correctness across different testing frameworks.

## Background

Related and "inspirational" projects to harvest for ideas, code, and
possibly integration:

- [Testkraut] - a framework for testing results from processing pipelines.
  To some degree, this project is just a revisit to [Testkraut] idea, while
  trying to pull out the actual "fingerprints" and tests for the results,
  while leaving behind the tracing and call graph creation and analysis.
  Later on testkraut could make use of the `scitests` or get absorbed within

- [ITK]

- [numbers] - a relatively unknown but ingenious project to find "bugs"
  in numbers or numerical sequences found in a given text (which could be codebase)

- [statcheck] -


## Goals

- be able to "prescribe" expected properties of the data, so diversion
  from the expectation would be characterized as a failure, although
  data itself might be allowed to change

- provide a "portable" summary of the data at hands so to be able
  to verify later on validity of the new data given only the summary
  (see [Testkraut])

- ... TODO ...


## Qualitative testing

Terminology to define

- `test` - some might just be checks (take 1 input), some might take 2
  or more (where first one is the target), and verify that estimates
  "satisfy" the target
- `check` - validate that input passes coded in checks/restrictions
- `fingerprint` - analyses the data and returns a record representing
  it
- `comparators` - given two (or more) fingerprints provide comparison
  possibly allowing for numeric differences in specific fields etc

Any/all of them might be parametric to define options how to augment
their behavior.

### Checks

#### Neuroimaging

- `skul_stripped` - could be as basic as verifying that outsides are all
  0ed out and then there is a single blob/cluster inside
- `face_stripped` - a bit more sophisticated test to determine if
  facial features were stripped. (`defaced` could mean an obfuscated
  facial features, and could be even more difficult/sophisticated test)
- `motion_corrected` - fail whenever any subsequent volume has significant
  displacement from any other.

#### Machine learning

if we come up with some unified API or adapters:

- `valid_classifier` - should perform adequately on a set of simplistic
  datasets (thus we could provide characterization on where a given
  classifier would fail) we provide (possibly varying SNR etc)
  - `resilient_to_disbalance` - if set, should not flip into predicting
    "majority of training samples"
  - `multiclass`
  - `multilabel`


### Generic checks on data

Given any data array, common tests could be established based on the nature of the
data.  Data could be "extracted" by domain-specific IO library.

Here is ideas for some tests:

- `data_population_percent` -- `[0..100]`
- `data_population_ratio` -- `[0..1]`
- `data_stats_p` -- `[0..1]`
  - `model_free` -- given number of samples must not be lower than `1/N+1`
- `data_non0` -- `any(data!=0)`

## Fingerprints

### Domain agnostic

- `checksums` -- checksums (some or all MD5, SHA...)
  for the input.  So useful only for the exact matching
- `data_stats` -- gross mean, std, ...
- `

### Benchmarking

Fingerprints could also be used to establish "benchmarking" across
different "inputs".  E.g. if `valid_classifier` is also a fingerprint
returning a record with some metrics on quality of classification, we
could use it to compare different classifiers.

`motion_corrected` or `aligned` could return mean displacement
against target which could also then serve as a benchmark to compare
different algorithms.


## Composite tests

TODO: since inspired by testkraut, migrate embedded there use-cases here

Arrange multiple checks/tests into a sequence (set/collection?) of tests
encapsulating our expectations about the data, e.g.

### `myoutput_statistical_map`

I decided to specify that all my stats results should pass a set of checks:

- `skull_stripped` -- I did not forget to skull strip it (if that is what
  I wanted)
- `data_non0` -- I do have results!

And then create some additional ones which would reuse
`myoutput_statistical_map`

- `myoutput_correct_pmap`
  - `myoutput_statistical_map`
  - `data_stats_p`


## Usage usage cases

- given a data file, sweep tests known to possibly be applicable for this
  file format, and report which qualitative tests pass/fail

    - e.g. given a neuroimaging data file (e.g. `.nii.gz`) be able to tell
      that it passes
        - `data_population_percent`
        - `data_population_ratio`
        - `data_stats_p`
        - TODO: consider a hierarchical relation here, or providing some
          "likelihood" for a test to be representative for the provided
          data. E.g. if it passes `data_stats_p` or `data_population_ratio`
          then unlikely to be `data_population_percent`.  Or if `data_non0`
          fails, then any of the above should also be "considered"

- use established tests within Python/bash/... tests. So conveniently expose
  tests in cmdline, Python, ...

- validate results for being correct

## Related publications

[Testkraut]: https://github.com/neurodebian/testkraut
[numbers]: http://www.coding-guidelines.com/numbers
[statcheck]: http://statcheck.io

- [BA88](https://pdfs.semanticscholar.org/1f46/0a72b22acad5f69f79326bd4fb82da1f04e9.pdf)
  *Misleading Statistics: Errors in Textbooks, Software and Manuals*
  J MARTIN BLAND AND DOUGLAS G ALTMAN
