# Workflow for assembling a gene assembly
Basic Assembly Workflow

## Part 1: Source File Trimming and Checking
- Create an untrimmed folder and use fastq on the source files
	- Source files (fastq for example) may be obtained through public repos like NCBI sequence read archive, European Nucleotide Archive, etc. Or directly given to you
	- Example command:```
```
fastqc -o untrimmedq -f fastq $sourcedir/Av_R1.cp.fastq $sourcedir/Av_R2.cp.fastq
```
	- Fastqc is a quality control tool for sequence data
	-  In this example R1 (read 1) is forward read and R2 (read 2) is reverse read

- Then use bbduk to trim the files
```
bbduk.sh in1=$sourcedir/Av_R1.cp.fastq in2=$sourcedir/Av_R2.cp.fastq ref=/mnt/s-ws/everyone/bbmap/resources/adapters.fa  ktrim=r k=23 mink=11 hdist=1 tpe tbo ftm=5 out1=Av_R1.cp.trimmed.fastq out2=Av_R2.cp.trimmed.fastq
```
	- Specify the kmer count using k= ... , you need to use adapters.fa which is found in the bbduk package.
	- Dont forget to rename the file as trimmed
- Run Fastq again on the trimmed files and save to a trimmed folder
```
fastqc -o trimmedq -f fastq Av_R1.cp.trimmed.fastq Av_R2.cp.trimmed.fastq
```

- Assemble the files with Spades with the trimmed reads
```
spades -o spades -1 Av_R1.cp.trimmed.fastq -2 Av_R2.cp.trimmed.fastq --cov-cutoff 20
```
