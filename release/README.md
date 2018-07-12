# Annotation files available for download

Please not that currently these annotation files only allow to annotate **transitive** similarity relations 
(such as `HOM:0000007 "historical homology"`), and do not allow to capture non-transitive relations 
(such as `HOM:0000002 "homoplasy"`).

## File types

The similarity annotations can be retrieved in three "flavors":

### raw_similarity_annotations.tsv

These annotation file contains all annotations made by curators, plus automatically inferred annotations. 
Each annotation captures one single evidence, describing a similarity relation, for an (some) anatomical(s) structure(s), 
in a given taxon. Each line thus targets:

* a HOM ID (similarity relation)
* Uberon IDs (targeted anatomicals structures)
* a NCBI taxon ID (the targeted taxon)
* an ECO ID (the type of evidence allowing to create the annotation)
* a CIO ID (term from the Confidence Information Ontology, describing the level of confidence in the annotation)
* a reference ID (the reference where the information comes from)

The raw annotations correspond to the annotations made by curators, as present in the [edit/ folder](../edit/), 
except that names of anatomical structures, taxa, ECO and CIO terms, are retrieved directly from the ontologies used, 
to be kept up-to-date.

The automatic annotations are inferred from logical constraints in the Uberon ontology. For instance, 
if an annotation targets the structure `epithelial cell`, and another annotation targets the structure `pancreas`, 
an automatic annotation can be inferred for the term `epithelial cell of pancreas` (since in the Uberon ontology, 
`epithelial cell of pancreas` formally corresponds to the intersection of the terms `epithelial cell` and `pancreas`). 
The automatic annotations can be easily identified, as they are associated to the ECO term 
`ECO:0000501 "evidence used in automatic assertion"`, and are tagged with the supporting text 
"Annotation inferred from logical constraints using annotations [... (description of the supporting raw annotations follows)]".

### summary_similarity_annotations.tsv

These annotation files summarizes all annotations present in the file `raw_similarity_annotations.tsv` described above, 
by aggregating all evidence lines targeting the same HOM ID, Uberon IDs, taxon ID, in order to provide a global level of confidence 
for this information. Each line thus targets:

* a HOM ID (similarity relation)
* Uberon IDs (targeted anatomicals structures)
* a NCBI taxon ID (the targeted taxon)
* a CIO ID (term from the Confidence Information Ontology, describing the level of confidence in the annotation)

For instance, in the file `raw_similarity_annotations.tsv`, there are three evidence lines providing information about 
the homology of the structure `camera-type eye` in the lineage `Vertebrata`, there are thus three lines describing this information. 
These three lines are summarized in the file `summary_similarity_annotations.tsv` into one single line, providing a global 
level of confidence in this annotation, from all the evidence lines available.

### ancestral_taxa_homology_annotations.tsv

One of the questions that can be answered thanks to the similarity annotations is: 
"in which lineage did that anatomical structure first appear?". This is the information that can be retrieved in the file 
`ancestral_taxa_homology_annotations.tsv`. This file contains annotations targeting the highest lineage 
with a positive and reliable information of historical homology, for each anatomical structure: when there are annotations 
for a same HOM ID and Uberon ID, but different taxon IDs, only the most likely and highest taxon is conserved, 
so that there is at most one line for a given HOM ID and Uberon ID. It thus provides for each anatomical structure 
the lineage in which it likely appeared.

Indeed, the raw and summary annotations can capture evidence lines of homology for an anatomical structure 
at different taxonomical levels. For instance, there are evidence lines that the `urinary bladder` is homologous 
in the `Tetrapoda` lineage, and other evidence lines that it is homologous in the `Amniota` lineage. These evidence lines, 
targeting a same anatomical structure, but at different taxonomical levels, are all present in the files 
`raw_similarity_annotations.tsv` and `summary_similarity_annotations.tsv`.  In the file `ancestral_taxa_homology_annotations.tsv`, 
only the annotation for the highest taxonomical level is present (the annotation targeting the `Tetrapoda` lineage).

### Summary of the three file types

To summarize:

* What makes a line unique in the file `raw_similarity_annotations.tsv` are the fields:
  * HOM ID
  * Uberon IDs
  * taxon ID
  * ECO ID
  * CIO ID
  * reference ID
* What makes a line unique in the file `summary_similarity_annotations.tsv` are the fields:
  * HOM ID
  * Uberon IDs
  * taxon ID
* What makes a line unique in the file `ancestral_taxa_homology_annotations.tsv` are the fields:
  * HOM ID (but it is always targeting the relation "historical homology")
  * Uberon IDs

## File format

### Annotation file fields

The format of this annotation file is inspired from the 
[GO annotation file format](http://www.geneontology.org/GO.format.gaf-2_0.shtml), as well as the procedure to provide 
supporting information is inspired from the [guide to GO evidence codes](http://www.geneontology.org/GO.evidence.shtml).

<table><thead>
<tr><th>Column</th><th>Content</th><th>Cardinality</th><th>Example</th><th>Comment</th></tr>
</thead>
<tbody>
<tr><td>1</td><td><a href='#hom-id-column-1'>HOM ID</a></td><td>1</td><td>HOM:0000007</td><td></td></tr>
<tr><td>2</td><td><a href='#hom-name-column-2'>HOM name</a></td><td>1</td><td>historical homology</td><td></td></tr>
<tr><td>3</td><td><a href='#entity-column-3'>entity ID (|entity ID)</a></td><td>1 or greater</td><td>UBERON:0000019</td><td>Multiple entities are eparated with |</td></tr>
<tr><td>4</td><td><a href='#entity-name-column-4'>entity name (|entity name)</a></td><td>1 or greater</td><td>camera-type eye</td><td>Multiple entities are eparated with |</td></tr>
<tr><td>5</td><td><a href='#qualifier-column-5'>qualifier</a></td><td>0 or 1</td><td>NOT</td><td></td></tr>
<tr><td>6</td><td><a href='#taxon-column-6'>taxon ID</a></td><td>1</td><td>7742</td><td>cardinality might change in the future to handle other relations</td></tr>
<tr><td>7</td><td><a href='#taxon-name-column-7'>taxon name</a></td><td>1</td><td>Vertebrata</td><td>cardinality might change in the future to handle other relations</td></tr>
<tr><td>8</td><td><a href='#cio-id-column-8'>CIO ID</a></td><td>1</td><td>CIO:0000003</td><td></td></tr>
<tr><td>9</td><td><a href='#cio-name-column-9'>CIO name</a></td><td>1</td><td>high confidence assertion from single evidence</td><td></td></tr>
<tr><td>10</td><td><a href='#eco-id-column-10'>ECO ID</a></td><td>1</td><td>ECO:0000067</td><td>Field present only in the file raw_similarity_annotations.tsv</td></tr>
<tr><td>11</td><td><a href='#eco-name-column-11'>ECO name</a></td><td>1</td><td>developmental similarity evidence</td><td>Field present only in the file raw_similarity_annotations.tsv</td></tr>
<tr><td>12</td><td><a href='#reference-id-column-12'>reference ID</a></td><td>1</td><td>ISBN:978-0030223693</td><td>Field present only in the file raw_similarity_annotations.tsv</td></tr>
<tr><td>13</td><td><a href='#reference-title-column-13'>reference title</a></td><td>1</td><td>Liem KF, Bemis WE, Walker WF, Grande L, Functional Anatomy of the Vertebrates: An Evolutionary Perspective (2001) p.429</td><td>Field present only in the file raw_similarity_annotations.tsv</td></tr>
<tr><td>14</td><td><a href='#supporting-text-column-14'>supporting text</a></td><td>1</td><td>...The eye initially develops as a single median evagination of the diencephalon...</td><td>Field present only in the file raw_similarity_annotations.tsv</td></tr>
<tr><td>15</td><td><a href='#assigned-by-column-15'>assigned by</a></td><td>1</td><td>Bgee</td><tdField present only in the file raw_similarity_annotations.tsv></td></tr>
<tr><td>16</td><td><a href='#curator-column-16'>curator</a></td><td>1</td><td>ANN</td><td>Field present only in the file raw_similarity_annotations.tsv</td></tr>
<tr><td>17</td><td><a href='#date-column-17'>date</a></td><td>1</td><td>2013-11-29</td><td>Field present only in the file raw_similarity_annotations.tsv</td></tr>
</tbody></table>


### Definitions and requirements for field contents

#### HOM ID (column 1)

Unique identifier of the similarity concept targeting the `entity` (column 3), in the provided `taxon` (column 6). 
The relations come from the 
[ontology of homology and related concepts](https://raw.githubusercontent.com/BgeeDB/homology-ontology/master/src/ontology/hom.obo) 
(see also the [related publication in *Trends Genet*](http://linkinghub.elsevier.com/retrieve/pii/S0168-9525(10)00002-8), 
and the [project home](https://github.com/BgeeDB/homology-ontology))

See columns 3 and 6 for more details. Required field, cardinality 1.

#### HOM name (column 2)

Name of the similarity concept defined by `HOM ID` (column 1).

#### Entity (column 3)

Unique identifier(s) of the entity(ies) targeted by the `HOM ID` relation (column 1), for the provided `taxon` (column 6).

For instance, for the following values: 
    
column 1: `HOM:0000007` ("historical homology")
column 3: `UBERON:0000019` ("camera-type eye")
column 6: `7742` ("Vertebrata")

The meaning of this annotation is that the structure `UBERON:0000019` "camera-type eye" is believed to originate 
from a common structure, present in the least common ancestor of vertebrates, thus being homologous in the vertebrate clade.

It can be necessary in some cases to provide several `entity` identifiers. This is because it is possible for a structure, 
present in the common ancestor of a clade, to have evolved into different structures, yet homologous. 
It is for instance the case of the lung (UBERON:0002048) and of the swim bladder (UBERON:0006860), 
that are believed to originate from a common ancestral structure present in the ancestor of the *Euteleostomi* 
(see the annotation file for more details). But this ancestral structure is not believed to still exist as such in extant species, 
and there is no term describing such a structure in the Uberon ontology. In that case, to capture the fact that 
the lung and the swim bladder are homologous, both their identifiers are used, separated by a pipe (`|`), in the form: 

    UBERON:0002048|UBERON:0006860

It could be argued that the proper way of providing such an annotation would be to create a new term in Uberon, 
describing this putative ancestral structure. While we agree with this principle, we use the "several identifiers" approach 
for not depending on the modifications of Uberon that would be required, and to not "pollute" Uberon 
with many non-existing structures.


Please note that sometimes, Uberon can use a same term to describe structures that are <strong>not</strong> homologous 
in some lineages, but rather, analogous. This is for instance the case of the structure `UBERON:0000988` "pons": 
while this structure is present in both Aves and Mammalia, it is thought to have appeared independently in each of these lineages, 
notably because this structure does not exist in other Amniota. 

This evolutionary path is captured through 3 different assertions: 
first assertion: 
column 1: `HOM:0000007` ("historical homology")
column 3: `UBERON:0000988` ("pons")
column 6: `8782` ("Aves")
=> The meaning of this assertion  is that the structure `UBERON:0000988` "pons", <strong>as it appears in Aves</strong>, 
is believed to originate from a common structure, present in the least common ancestor of Aves, 
thus being homologous in the Aves clade. This assertion does not mean that all pons in all clades derived from a structure 
present in the Aves least common ancestor. 

Second assertion: 
column 1: `HOM:0000007` ("historical homology")
column 3: `UBERON:0000988` ("pons")
column 6: `40674` ("Mammalia")
=> The meaning of this assertion  is that the structure `UBERON:0000988` "pons", <strong>as it appears in Mammalia</strong>, 
is believed to originate from a common structure, present in the least common ancestor of Mammalia, thus being homologous 
in the Mammalia clade. This assertion does not mean that all pons in all clades derived from a structure present 
in the Mammalia least common ancestor. 

Third assertion (<strong>negative</strong>): 
column 1: `HOM:0000007` ("historical homology")
column 3: `UBERON:0000988` ("pons")
column 5: <strong>`NOT`</strong>
column 6: `32524` ("Amniota")
=> formally states that the generic term `UBERON:0000988` "pons" is not homologous in Amniota, 
see <a href='#qualifier-column-5'>column 5 (qualifier)</a> for more details.


This field is required, cardinality 1 or greater.

#### Entity name (column 4)

Name(s) of the entity(ies) defined by `entity` (column 3).
If several entities are provided in column 3, the pipe (`|`) separator is used, for instance: 

    lung|swim bladder

#### Qualifier (column 5)

Flag used to negate the interpretation of an annotation. If provided, the only accepted value is `NOT`. 
This is used to capture an information **rejecting** a putative relation between structures, that could otherwise seem plausible.

For instance, this qualifier is used to capture annotations stating that hindgut (`UBERON:0001046`) 
is <strong>not</strong> believed to be homologous among <i>Bilateria</i>. Another annotation then states 
that hindgut is believed to be homologous among <i>Vertebrata</i>.

Optional field, cardinality 0 or 1. If cardinality 1, the only accepted value is `NOT`.

#### Taxon (column 6)

The unique identifier of the taxon targeted by the `HOM ID` relation (column 1), for the provided `entity` (column 3). 
These identifiers are integers linking to the [NCBI taxonomy](http://www.ncbi.nlm.nih.gov/Taxonomy/).

See definition of column 3 for an example of use of `taxon`.

Required field, cardinality 1. Note that this cardinality could evolve in the future, 
to allow the use of other types of similarity relations (for instance, to define in which taxa a structure 
is functionally equivalent, as this type of relation would not originate from any common ancestor).

#### Taxon name (column 7)

Name of `taxon`, defined in column 6.

#### CIO ID (column 8)

Unique identifier from the [confidence information ontology](https://github.com/BgeeDB/confidence-information-ontology/blob/master/src/ontology/confidence_information_ontology.obo). 
This experimental ontology is an attempt to provide a mean to capture information about the confidence in an assertion. 
See [project home](https://github.com/BgeeDB/confidence-information-ontology) for more details.

In the file `raw_similarity_annotations.tsv` this confidence code can only belong to the "confidence from single evidence" branch. 
Possible values are then `CIO:0000003` ("high confidence from single evidence"), 
`CIO:0000004` ("medium confidence from single evidence"), and `CIO:0000005` ("low confidence from single evidence").

For the file `summary_similarity_annotations.tsv`, this confidence code is assigned automatically, 
using the "single evidence" confidences provided by curators, and using the Evidence Ontology to try to determine whether 
the evidences used are of a same experimental type, or of different experimental types (which provides a stronger support 
for the assertion, see the [confidence information ontology](https://github.com/BgeeDB/confidence-information-ontology/blob/master/src/ontology/confidence_information_ontology.obo) for more details).

#### CIO name (column 9)

Name of `CIO ID`, defined in column 8.

#### ECO ID (column 10)

Unique identifier from the [Evidence Ontology](https://code.google.com/p/evidenceontology/), capturing 
how the annotation is supported. See the [GO evidence code guide](http://www.geneontology.org/GO.evidence.shtml) 
for more information.

This field is mandatory and present only in the file `raw_similarity_annotations.tsv`.

#### ECO name (column 11)

Name of `ECO ID`, defined in column 10. 

#### Reference ID (column 12)

Unique identifier of a single source, cited as an authority for asserting the relation. Note that only one reference 
can be cited on a single line in the `raw_similarity_annotations.tsv` file (and only one evidence from this reference 
can be cited on a single line).

This field is mandatory and present only in the file `raw_similarity_annotations.tsv`.

#### Reference title (column 13)

Title of the reference defined in `reference ID` (column 12).

#### Supporting text (column 14)

A quote from the reference defined in column 12, supporting the annotation. If possible, it should also support 
the choice of the `ECO ID` (column 10).

This field is mandatory and present only in the file `raw_similarity_annotations.tsv`.

#### Assigned by (column 15)

The database which made the annotation. Used for tracking the source of an individual annotation. 

This field is mandatory and present only in the file `raw_similarity_annotations.tsv`.

#### Curator (column 16)

A code allowing to identify the curator who made the annotation, from the database defined in column 15.

This field is mandatory and present only in the file `raw_similarity_annotations.tsv`.

#### Date (column 17)

Date on which the annotation was made. Format is `yyyy-MM-dd`.

This field is mandatory and present only in the file `raw_similarity_annotations.tsv`.

## Relation between developmental structures

Distinctions based on the developmental state of a same organ can be irrelevant when considering similarity annotations. 
For instance, terms such as ‘future brain’ and ‘brain’, while relevant when considering the developmental lineage of a structure, 
can correspond to a same common ancestral structure.

The annotations provided here always target entities describing the fully-formed structures, unless a distinction 
between developmental structures has to be made. Related developmental structures can be retrieved in Uberon using the relations 
`transformation_of`, and `immediate_transformation_of`.

When using this annotation file, it is recommended to always group entities described in the entity ID field (column 3), 
as well as any entity **not annotated**, and related to them by a `transformation_of` or `immediate_transformation_of` relation.