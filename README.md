[![DOI](https://zenodo.org/badge/DOI/10.1093/nar/gkae1118.svg)](https://doi.org/10.1093/nar/gkae1118)
[![DOI](https://zenodo.org/badge/DOI/10.1093/nar/gkaa793.svg)](https://doi.org/10.1093/nar/gkaa793)
[![Bluesky](https://img.shields.io/badge/-Bluesky-3686f7?style=social&label=Follow%20bgeedb&logo=bluesky&logoColor=blue&labelColor=white&domain=https%3A%2F%2Fbsky.app)](https://bsky.app/profile/bgee.org)
[![Mastodon](https://img.shields.io/mastodon/follow/109308703977124988?style=social&label=Follow%20%40bgeedb&domain=https%3A%2F%2Fgenomic.social)](https://genomic.social/%40bgeedb)

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
