JAVA := java -Xmx10g -Dorg.slf4j.simpleLogger.defaultLogLevel=error -Djava.util.logging.config.file=jdkLogConfig.properties
WGET := wget -N -nv
MV   := mv -f

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

OVERRIDE_TAXON_CONSTRAINTS := HsapDv\:/9606,MmusDv\:/10090,ZFS\:/7955,XAO\:/8364,FBdv\:/7227,GgalDv\:/9031,GgorDv\:/9593,MmulDv\:/9544,MdomDv\:/13616,OanaDv\:/9258,PtroDv\:/9598,PpanDv\:/9597,PpygDv\:/9600,BtauDv\:/9913,RnorDv\:/10116,AcarDv\:/28377,TnigDv\:/99883,SscrDv\:/9823,WBls\:/6239,PdumDv\:/6358,UBERON\:0000069/6239--7227--7955--8364,AAO\:/8364,ABA\:/10090,EHDAA2\:/9606,EMAPA\:/10090,FBbt\:/7227,FMA\:/9606,HAO\:/7399,MA\:/10090,MFO\:/8089,SPD\:/6893,TADS\:/6939,TAO\:/7955--99883,TGMA\:/44484,WBbt\:/6239,ZFA\:/7955,EHDAA\:/9606,EV\:/9606,CEPH\:/6605,CTENO\:/10197,PORO\:/6040

all: $(TMP_DIR)release_files.log

# Download the ontologies needed
$(UBERON_WITH_CONSTRAINTS): 
	@$(WGET) http://purl.obolibrary.org/obo/uberon/ext.owl -O $@.tmp && $(MV) $@.tmp $@
$(UBERON_COMPOSITE): 
        @$(WGET) http://purl.obolibrary.org/obo/uberon/composite-metazoan.owl -O $@.tmp && $(MV) $@.tmp $@
$(HOM_ONT): 
        @$(WGET) https://raw.githubusercontent.com/BgeeDB/homology-ontology/master/src/ontology/hom.obo -O $@.tmp && $(MV) $@.tmp $@
$(ECO_ONT): 
        @$(WGET) http://evidenceontology.googlecode.com/svn/trunk/eco.obo -O $@.tmp && $(MV) $@.tmp $@
$(CIO_ONT):         
        @$(WGET) https://raw.githubusercontent.com/BgeeDB/confidence-information-ontology/master/src/ontology/cio.owl -O $@.tmp && $(MV) $@.tmp $@

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
	@$(WGET) ftp://ftp.ebi.ac.uk/pub/databases/taxonomy/$(TAXDAT) -O $(TMP_DIR)$(TAXDAT).tmp && $(MV) $(TMP_DIR)$(TAXDAT).tmp $(TMP_DIR)$(TAXDAT)
	@$(JAVA) -jar $(BGEEPIPELINEJAR) GenerateTaxonOntology $(TMP_DIR)$(TAXDAT) $^ $@
	@rm -f $(TMP_DIR)$(TAXDAT)
	@echo "Done generating taxonomy ontology.\n"

# generate taxon constraints
$(TMP_DIR)taxonConstraints.tsv: $(UBERON_WITH_CONSTRAINTS) $(TMP_DIR)ncbitaxon.owl $(TMP_DIR)annotTaxIds.tsv
	@echo "Generating taxon constraints..."
	@$(JAVA) -jar $(BGEEPIPELINEJAR) TaxonConstraints generateTaxonConstraints $^ $@.tmp
	@$(MV) $@.tmp $@
	@echo "Done generating taxon constraints.\n"

# generate the release files
$(TMP_DIR)release_files.log: $(TMP_DIR)taxonConstraints.tsv $(OVERRIDE_TAXON_CONSTRAINTS) $(UBERON_COMPOSITE) $(TMP_DIR)ncbitaxon.owl $(HOM_ONT) $(ECO_ONT) $(CIO_ONT) $(CURATOR_FILE) $(RAW_FILE) $(SUMMARY_FILE) $(ANCESTORS_FILE)
	@echo "Generating release files..."
	@$(JAVA) -jar $(BGEEPIPELINEJAR) SimilarityAnnotation generateReleaseFile $^ | tee $@.tmp
	@$(MV) $@.tmp $@
	@echo "Done generating release files.\n"

clean: 
	@rm -f $(TMP_DIR)release_files.log $(TMP_DIR)taxonConstraints.tsv $(TMP_DIR)ncbitaxon.owl $(TMP_DIR)$(TAXDAT) $(TMP_DIR)allTaxIds.tsv $(TMP_DIR)annotTaxIds.tsv $(UBERON_WITH_CONSTRAINTS) $(UBERON_COMPOSITE) $(HOM_ONT) $(ECO_ONT) $(CIO_ONT)
