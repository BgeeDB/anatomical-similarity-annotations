include Makefile.taxon_info

JAVA := java -Xmx50g
MV   := mv -f
# -s: silent, no progress bar displayed; 
# -S: if silent, still shows error message if it fails; 
# -L: in case of redirection, follow it and redo the request, this is necessary to correctly get modification date; 
# -f: in case of error do not display the document returned.
# -R: make the local file have the same timestamp as remote file
# Sould be used with option -z filename: download remote file only if more recent than the provided file
# And with -o option to choose output file name. 
# See variable APPEND_CURL_COMMAND. 
CURL := curl -s -S -L -f -R
# variable to append to cURL command to download a file only if remote file more recent than targeted file, 
# download in a temp file and move to stable location on sucess; delete temp file on error
# Variable to be recursively expanded. 
# -z filename: download remote file only if last modification date more recent that modification date of downloadfile
# -o filename: store downloaded file into filename
# Redirect stderr of mv to /dev/null in case the file was not redownloaded so that the temp file does not exist
APPEND_CURL_COMMAND = -z $@ -o $@.tmp && mv -f $@.tmp $@ 2>/dev/null || rm -f $@.tmp $@

TMP_DIR                 := tmp/
BGEEPIPELINEJAR         := bgee-pipeline-with-dependencies.jar
CURATOR_FILE            := ../edit/bgee_trans_similarity_annotations_edit.tsv
RAW_FILE                := ../release/raw_similarity_annotations.tsv
SUMMARY_FILE            := ../release/summary_similarity_annotations.tsv
ANCESTORS_FILE          := ../release/ancestral_taxa_homology_annotations.tsv

UBERON_WITH_CONSTRAINTS := $(TMP_DIR)ext.owl
UBERON_COMPOSITE        := $(TMP_DIR)composite-metazoan.owl
HOM_ONT                 := $(TMP_DIR)hom.obo
ECO_ONT                 := $(TMP_DIR)eco.obo
CIO_ONT                 := $(TMP_DIR)cio.owl

all: $(TMP_DIR)release_files.log

# Download the ontologies needed
$(UBERON_WITH_CONSTRAINTS): 
	@$(CURL) http://purl.obolibrary.org/obo/uberon/ext.owl $(APPEND_CURL_COMMAND)
$(UBERON_COMPOSITE): 
	@$(CURL) http://purl.obolibrary.org/obo/uberon/composite-metazoan.owl $(APPEND_CURL_COMMAND)
$(HOM_ONT): 
	@$(CURL) https://raw.githubusercontent.com/BgeeDB/homology-ontology/master/src/ontology/hom.obo $(APPEND_CURL_COMMAND)
$(ECO_ONT): 
	@$(CURL) https://raw.githubusercontent.com/evidenceontology/evidenceontology/master/eco.obo $(APPEND_CURL_COMMAND)
$(CIO_ONT):         
	@$(CURL) https://raw.githubusercontent.com/BgeeDB/confidence-information-ontology/master/src/ontology/cio.owl $(APPEND_CURL_COMMAND)

# generate a file with the taxon IDs present in the curator annotation file, 
# to define for which taxa we will generate taxon constraints
$(TMP_DIR)annotTaxIds.tsv: $(CURATOR_FILE)
	@$(JAVA) -jar $(BGEEPIPELINEJAR) SimilarityAnnotation extractTaxonIds $< $@.tmp
# need to add a header
	@echo "taxon ID" | cat - $@.tmp > $@
	@rm -f $@.tmp

# generate a file containing the taxon IDs present in the curator annotation file, 
# and in Uberon, to define which taxa should be kept in the taxonomy ontology, 
# to later generate taxon constraints.
$(TMP_DIR)allTaxIds.tsv: $(TMP_DIR)annotTaxIds.tsv $(UBERON_WITH_CONSTRAINTS)
	@echo "Extracting all taxon IDs..."
# get the tax IDs used in Uberon
	@$(JAVA) -jar $(BGEEPIPELINEJAR) Uberon extractTaxonIds $(UBERON_WITH_CONSTRAINTS) $(TMP_DIR)uberonTaxIds.tsv.tmp
# add taxa used in overriding taxon constraints
	@taxa="$(OVERRIDING_TAXA)"; for taxon in $$taxa; do echo $$taxon >> $(TMP_DIR)uberonTaxIds.tsv.tmp; done
# get the tax IDs used in the curator annotation file, without the first header line
	@sed 1d $(TMP_DIR)annotTaxIds.tsv > $(TMP_DIR)annotTaxIds.tsv.noheader
# concatenate these 2 files
	@cat $(TMP_DIR)annotTaxIds.tsv.noheader $(TMP_DIR)uberonTaxIds.tsv.tmp | sort | uniq > $@.tmp
# need to add a header
	@echo "taxon ID" | cat - $@.tmp > $@
# remove tmp files
	@rm -f $@.tmp $(TMP_DIR)annotTaxIds.tsv.noheader $(TMP_DIR)uberonTaxIds.tsv.tmp
	@echo "Done extracting all taxon IDs.\n"

# generate a taxonomy ontology, from the NCBI taxonomy, 
# containing only the taxa present in Uberon and in the annotations, along with their ancestors, 
# to later generate taxon constraints.
# name of the source file for the taxonomy
TAXDAT := taxonomy.dat
$(TMP_DIR)ncbitaxon.owl: $(TMP_DIR)allTaxIds.tsv
	@echo "Generating taxonomy ontology..."
	@$(CURL) ftp://ftp.ebi.ac.uk/pub/databases/taxonomy/$(TAXDAT) -z $(TMP_DIR)$(TAXDAT) -o $(TMP_DIR)$(TAXDAT)
	@$(JAVA) -jar $(BGEEPIPELINEJAR) GenerateTaxonOntology $(TMP_DIR)$(TAXDAT) $^ $@
	@rm -f $(TMP_DIR)$(TAXDAT)
	@echo "Done generating taxonomy ontology.\n"

# generate taxon constraints
$(TMP_DIR)taxonConstraints.tsv: $(UBERON_WITH_CONSTRAINTS) $(UBERON_COMPOSITE) $(TMP_DIR)ncbitaxon.owl $(TMP_DIR)annotTaxIds.tsv
	@echo "Generating taxon constraints..."
	@$(JAVA) -jar $(BGEEPIPELINEJAR) TaxonConstraints generateTaxonConstraints $(UBERON_WITH_CONSTRAINTS) $(TMP_DIR)ncbitaxon.owl $(TMP_DIR)annotTaxIds.tsv $(SIMPLIFICATION_STEPS) $(UBERON_COMPOSITE) $(OVERRIDE_TAXON_CONSTRAINTS) $@.tmp
	@$(MV) $@.tmp $@
	@echo "Done generating taxon constraints.\n"

# generate the release files
$(TMP_DIR)release_files.log: $(TMP_DIR)taxonConstraints.tsv $(UBERON_COMPOSITE) $(TMP_DIR)ncbitaxon.owl $(HOM_ONT) $(ECO_ONT) $(CIO_ONT) $(CURATOR_FILE) $(RAW_FILE) $(SUMMARY_FILE) $(ANCESTORS_FILE)
	@echo "Generating release files..."
	@$(JAVA) -jar $(BGEEPIPELINEJAR) SimilarityAnnotation generateReleaseFile $(TMP_DIR)taxonConstraints.tsv - $(UBERON_COMPOSITE) $(TMP_DIR)ncbitaxon.owl $(HOM_ONT) $(ECO_ONT) $(CIO_ONT) $(CURATOR_FILE) $(RAW_FILE) $(SUMMARY_FILE) $(ANCESTORS_FILE) > $@.tmp
	@$(MV) $@.tmp $@
	@echo "Done generating release files.\n"

$(TMP_DIR)release_files_no_constraint_check.log: $(UBERON_COMPOSITE) $(TMP_DIR)ncbitaxon.owl $(HOM_ONT) $(ECO_ONT) $(CIO_ONT) $(CURATOR_FILE) $(RAW_FILE) $(SUMMARY_FILE) $(ANCESTORS_FILE)
	@echo "Generating release files with no taxon constraint check..."
	@$(JAVA) -jar $(BGEEPIPELINEJAR) SimilarityAnnotation generateReleaseFile - - $^ > $@.tmp
	@$(MV) $@.tmp $@
	@echo "Done generating release files with no taxon constraint check.\n"

clean: 
	@rm -f $(TMP_DIR)release_files.log $(TMP_DIR)taxonConstraints.tsv $(TMP_DIR)ncbitaxon.owl $(TMP_DIR)$(TAXDAT) $(TMP_DIR)allTaxIds.tsv $(TMP_DIR)annotTaxIds.tsv $(UBERON_WITH_CONSTRAINTS) $(UBERON_COMPOSITE) $(HOM_ONT) $(ECO_ONT) $(CIO_ONT) $(TMP_DIR)release_files_no_constraint_check.log
