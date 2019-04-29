# BMS-320-project
The Pipeline goes as follow:

	1. Downloading and Indexing the Reference Human Genome
	work_dir="$(pwd)"
	mkdir $work_dir/genome-data && cd $work_dir/genome-data

# download ref.
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/001/405/GCA_000001405.28_GRCh38.p13/GCA_000001405.28_GRCh38.p13_genomic.fna.gz

# unzipping
gunzip GCA_000001405.28_GRCh38.p13_genomic.fna.gz
ref_fna="$work_dir/genome-data/GCA_000001405.28_GRCh38.p13_genomic.fna"

# hisat indexing
mkdir idx
hisat2-build $ref_fna idx/grch38
index="$work_dir/genome-data/idx/grch38"

cd $work_dir
2. Downloading the sample of Interest from NCBI-SRA
fastq-dump -I --split-files $sample
3. Mapping the sample to the indexed reference human genome
hisat2 -q --phred33 \
	-x $index \
	-1 $sample_1.fastq \
	-2 $sample_2.fastq 
	-S $sample.sam \
	--met-file map.met \
	> map.log
  
4. Converting SAM to BAM
samtools view -hbo $sample.bam $sample.sam 

5. Extracting Records from the BAM file for Different Purposes
Insert some description here!

mkdir parsed
Purpose 4: getting the records with multiple mismatch (CIGAR)
