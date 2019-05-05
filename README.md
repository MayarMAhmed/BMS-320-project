# BMS-320-project
The Pipeline goes as follow:

1. Downloading SAM file of the sample from the NCBI-SRA website
https://trace.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?run=SRR5083364
		
2. Converting SAM to BAM
	
        samtools view -h -b -o sample.sam > sample.bam 

3. Extracting Records from the BAM file for multiple mismatch (CIGAR)
        
		
		#To add the MD tag
		samtools calmd -u -A -r sample.bam sequence.fasta > mdsa.bam 
		
		#To extract the MD tag with the original CIGAR from the new file. To investigate the difference
		samtools view -h mdsa.bam | cut -f 6,10,16
		
		#The original file
		samtools view -h mdsa.bam | cut -f 6,16 | wc -l
		#its results 8219
		
		#An attempt to get the MD tags with the mismatches only
		samtools view -h mdsa.bam | cut -f 6,16 | grep  -e 'MD:Z:**'
		
		#first attempt
		 samtools view -h mdsa.bam | cut -f 6,16 | grep  -e 'MD:Z:**' | wc -l 
		8107
		
		#To final attempt to get the MD with the mismatch
		samtools view -h mdsa.bam | cut -f 6,16 | awk 'length($2) >7 ' 
		
		#The number of reads with mismatches 
		 samtools view -h mdsa.bam | cut -f 6,16 | awk 'length($2) >7 ' | wc -l
		659
		
		#To sort the file with the MD tag to be easier to extract the positions
		samtools sort mdsa.bam > sorted_mdsa.bam
		
		#To index the file to maybe used in an attempt to visualize the mismatches
		samtools index -b sorted_mdsa.bam
		
		#To extract the position and inforation about reads with mismatch like the read name, reference name, size , location , CIGAR string , sequence and MD tag 
		samtools view -h mdsa.sorted.bam | awk 'length($16) > 7' | cut -f 1,3,4,6,7,10,16 > info_mismatchreads.txt
		
		#To extract reads with single mismatch with their location and their number
		samtools view mdsa.bam | cut -f 4,6,16 |awk 'length($3)== 9 || length($3) == 10' > single_mismatch.txt
		samtools view mdsa.bam | cut -f 4,6,16 |awk 'length($3)== 9 || length($3)==10' | wc -l
		527

		#To extract reads with double mismatch with their location and their number
		samtools view mdsa.bam | cut -f 4,6,16 |awk 'length($3)> 10 && length($3)<14' > double_mismatch.txt
		samtools view mdsa.bam | cut -f 4,6,16 |awk 'length($3)> 10 && length($3)<14' | wc -l
		101

		

		
		#To extract reades with 3 or 4 mismatches with their location and count them 
		samtools view mdsa.bam | cut -f 4,6,16 |awk 'length($3)> 14' > multiple_mismatch.txt
		 samtools view mdsa.bam | cut -f 4,6,16 |awk 'length($3)> 14' | wc -l
		17










		
		


	

