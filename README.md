# Similarity annotations between anatomical structures

## Presentation

The similarity annotations are used to define evolutionary relations between anatomical entities. The annotations
are currently focused toward the concept of **historical homology**, meaning that they try to capture which structures
are believed to derive from a common ancestral structure, in a common ancestral taxon.

These annotations could cover any transitive similarity relations (such as, "functional equivalence"),
but currently covers only the concept of historical homology (a homology that is defined by common descent).

## Availability

* The annotations can be retrieved in [the release folder](release/). These annotations include the raw annotations
captured by curators, and also infered annotations derived from the curated annotations. They are provided
in different "flavors", fiting different use cases. See the README in this directory for more details.
* The raw annotations, as captured by curators, are present in [the edit folder](edit/). Most users don't need to look at them,
these files are meant to be used by curators.

## Directories

* [release/](release/): the processed annotations available for download.
* [edit/](edit/): the raw annotations as captured by annotators.
* [utils/](utils/): files allowing to launch the processing of the raw annotations, to generate the files available for download.
