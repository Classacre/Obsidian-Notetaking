# Workflow for assembling a gene assembly
![[Assembly worksheet.txt]]
![[SCIEM401 Assembly Workflow.canvas|SCIEM401 Assembly Workflow]]
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
- use GetOrganelle for chloroplast-specific assembly

```
/mnt/s-ws/everyone/GetOrganelle/get_organelle_from_reads.py -1 Av_R1.cp.trimmed.fastq -2 Av_R2.cp.trimmed.fastq -t 1 -o Av_GetOrganelle -F embplant_pt -R 15 -k 21,45,65,85,105,125
```

- View assembly graph options

```
grep ">" Av_GetOrganelle/*.fasta
```

- Select and rename preferred assembly (usually option 2)

```
cp Av_GetOrganelle/embplant_pt.K125.complete.graph1.2.path_sequence.fasta Av.cp.assembly.fasta
sed -i -E 's/>.+/>Av.cp.assembly/' Av.cp.assembly.fasta
```

## Part 2: Assembly Verification

- Map reads back to assembly using BBMap

```
bbmap.sh in1=Av_R1.cp.trimmed.fastq in2=Av_R2.cp.trimmed.fastq ref=Av.cp.assembly.fasta ambiguous=random mappedonly out=Av.bam basecov=read_coverage.txt
```

- Sort and index BAM file

```
samtools sort -T tmp -o Av.sorted.bam Av.bam
samtools index Av.sorted.bam
```

- Generate coverage plot with gnuplot

```
gnuplot << EOF
set terminal svg
set output "read_coverage.svg"
plot 'read_coverage.txt' using 3 with lines
quit
EOF
```

## Part 3: Final Assembly Polish

- Use Pilon for final verification and corrections

```
pilon --genome Av.cp.assembly.fasta --frags Av.sorted.bam
```

- Rename final assembly

```
mv pilon.fasta Av.cp.final.fasta
sed -i s/assembly_pilon/final/ Av.cp.final.fasta
```

### Optional Tools:

- Bandage: For assembly graph visualization (useful after GetOrganelle)
- IGV: For visual inspection of read mapping (load Av.cp.assembly.fasta and Av.sorted.bam)
- Tadpole: For quick assembly testing (alternative to Spades)

```
tadpole.sh in1=Av_R1.cp.trimmed.fastq in2=Av_R2.cp.trimmed.fastq out=Av.contigs.fa k=75 mincr=20 overwrite=true
```




# Workflow for Annotating The Gene
![[Annotation worksheet.txt]]
