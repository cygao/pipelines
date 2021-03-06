# Treehouse RNA Seq Pipeline
# See https://github.com/BD2KGenomics/cgl-docker-lib/tree/master/rnaseq-cgl-pipeline

SAMPLES = $(shell ls -d samples/* | sed -e 's/samples/\/samples/g')

all: download run verify

download:
	echo "Downloading reference files into inputs/"
	mkdir -p inputs
	wget -N -P inputs http://hgdownload.soe.ucsc.edu/treehouse/reference/kallisto_hg38.idx
	wget -N -P inputs http://hgdownload.soe.ucsc.edu/treehouse/reference/starIndex_hg38_no_alt.tar.gz
	wget -N -P inputs http://hgdownload.soe.ucsc.edu/treehouse/reference/rsem_ref_hg38_no_alt.tar.gz
	(cd inputs && md5sum -c inputs.md5)

run:
	# Run the pipeline on all files listed in SAMPLES
	echo "Running pipeline on $(SAMPLES)"
	docker run \
		-v $(shell pwd)/outputs:$(shell pwd)/outputs \
		-v $(shell pwd)/samples:/samples \
		-v $(shell pwd)/inputs:/inputs \
		-v /var/run/docker.sock:/var/run/docker.sock \
		quay.io/ucsc_cgl/rnaseq-cgl-pipeline:2.0.8 \
		--save-wiggle --save-bam \
		--star /inputs/starIndex_hg38_no_alt.tar.gz \
		--rsem /inputs/rsem_ref_hg38_no_alt.tar.gz \
		--kallisto /inputs/kallisto_hg38.idx \
		--samples $(SAMPLES)

verify:
	echo "Verifying output of test file"
	tar -xOzvf outputs/TEST.tar.gz TEST/RSEM/rsem.genes.norm_counts.tab | md5sum -c outputs/TEST.md5
