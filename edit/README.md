# Curator files

This directory contains the edit versions of the annotation files, that are edited by curators and used to generate 
all the release files. 

`bgee_trans_similarity_annotations_edit.tsv` is the annotation file used by Bgee curators, 
to annotate transitive similarity relations from the HOM ontology.

DRAFT (just to take notes at this point)

## format
- A line in the file represents an information of similarity; it does *not* capture the ancestral taxon.

## NOT annotations
Different use cases of NOT annotations: 

* contradicting a homology hypothesis (provide example)
* cases of independent evolution (provide example)

In case of NOT annotations for independent evolution: always try to capture all taxa where the structure independently evolved 
(see issue #5)


---
Example case of independent evolution:

 "Amphibians have a three-chambered heart, whereas mammalian, crocodilian and avian hearts have four 
 chambers, two each for pulmonary and systemic circulations. The acquisition of a fully septated ventricle 
 has evolved independently in birds, mammals and crocodilians, and is an important example of convergent 
 evolution."

Annotation for interventricular septum (UBERON:0002094):

* NOT amniota
* positive annotation on Mammalia
* positive annotation on Aves
* positive annotation on Crocodylia 
---

## Multiple-entity annotation

When more than a structure is annotated for homology,

for example

UBERON:1500000|UBERON:0007174	scapular blade|medial border of scapula	

for homology on 32524 Amniota

we annotate also each single entity (usually by ECO:0000355 phylogenetic distribution evidence)

UBERON:1500000	scapular blade --> Aves

UBERON:0007174	medial border of scapula --> Mammalia

There should **always** be single-entity annotations corresponding to each entity in a multiple-entity annotation.

These multiple-entity annotations are used in two cases:
* when some structures evolved from an ancestral structure that does not exist anymore in extant species
* when a structure, still existing in extant species, evolved into a different structure in a taxon

Example 1:
```
cranial placode|siphon primordium -> chordata
cranial placode                   -> vertebrata
siphon primordium                 -> tunicata
```
In that case, it means that the ancestral structure, that the cranial placode and siphon primordium
evolved from, appeared in chordata, and does not exist anymore in extant species (nor the cranial placode
nor the siphon primordium existed in the taxon chordata; the single-entity annotations are all annotated
to taxa different to the multiple-entity annotation).

Example 2:
```
lung|swim bladder -> Gnathostomata
lung              -> Gnathostomata
swim bladder      -> Actinopterygii
```
In that case, the multiple-entity annotation and one of the single-entity annotation (lung) are mapped
to the same taxon (Gnathostomata). It means that lung is the ancestral structure of swim bladder.
The lung structure still exists in extant species.

---

### Choose the correct class

(adapted from C.Mungall, see
https://github.com/BgeeDB/anatomical-similarity-annotations/issues/9)

It is sometimes difficult to choose the correct class to report an annotation when we have a loose concept 
represented by multiple more formally defined classes. For example, if the osculum is homologous to the mouth, 
what is the mouth exactly?

which of these?

    UBERON:0000165 ! mouth
    UBERON:0000166 ! oral opening
    UBERON:0000167 ! oral cavity

We can postulate that for gene expression curation we never want to choose a space. Genes are always initially expressed 
in a cell, which may be located in a space, but not part of it. We can then just ditch all subclasses for space 
for all our curation.