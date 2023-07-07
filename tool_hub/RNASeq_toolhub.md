# RNASeq

# RNASeq

RNA sequencing (RNASeq) is a powerful and widely used technique for studying gene expression and transcriptome profiling. It involves sequencing the RNA molecules present in a biological sample, allowing researchers to identify and quantify the RNA transcripts that are present. RNASeq can be used to investigate a range of biological questions, including identifying differentially expressed genes, characterizing alternative splicing events, and detecting novel transcripts.

One of the major advantages of RNASeq is its ability to detect lowly expressed genes and quantify transcript abundance with high accuracy. This makes it a valuable tool for studying rare cell populations or subtle changes in gene expression. RNASeq can also be used to study non-coding RNAs, such as microRNAs and long non-coding RNAs, which have been implicated in a range of biological processes.

RNASeq has become increasingly accessible and affordable in recent years, with a range of commercial and open-source software tools available for analysis. However, RNASeq data analysis can be complex, and requires careful consideration of experimental design, normalization methods, and statistical analysis. Multiple factors affect the tools chosen to carry out analysis, and the workings of these tools have to be well understood to select the best one to carry out analysis. This tool hub will focus on the steps of RNASeq analysis, focusing on the tools available for each step, and the commands to run the steps.

## Datasets

We will use a public dataset from the Galaxy RNASeq tutorial. We use the subsampled data for our steps, but the large datasets are also available for use. The datasets are in fastqsanger format, which is similar to fastq format

```bash
## You can use wget or curl to get the datasets. Or a for loop
https://zenodo.org/record/6457007/files/GSM461177_1_subsampled.fastqsanger
https://zenodo.org/record/6457007/files/GSM461177_2_subsampled.fastqsanger
https://zenodo.org/record/6457007/files/GSM461180_1_subsampled.fastqsanger
https://zenodo.org/record/6457007/files/GSM461180_2_subsampled.fastqsanger
```

## Steps

### 1. Quality control and Trimming

This step involves removing errors introduced during the sequencing process. Different sequence platforms may cause different errors. Trimming is also necessary where adapters are present, and the reads are longer than the fragments sequenced. 

Your input at this point is fastq or fasta reads, and in most cases paired-end reads (_1.fastq and _2 .fastq in our case). The output of the fastqc or multiqc steps are reports that guide on the type of trimming to be done. These two tools are relatively ‘lightweight’ in terms of computational resources, and can run on a local PC.  

- FastQC - Check out the documentation here:  https://github.com/s-andrews/FastQC
- MultiQC - MultiQC can aggregate reports of multiple samples: https://github.com/ewels/MultiQC

The adapter trimming comes after checking the quality of the sequences. 

- Cutadapt - [https://cutadapt.readthedocs.io/en/stable/](https://cutadapt.readthedocs.io/en/stable/)
- TrimGalore - https://github.com/FelixKrueger/TrimGalore

Another quality check is conducted using FastQC or multiQC tools to check if the quality has improved. 

Each of these tools have man pages to guide their installation and use. Here are the basic commands of the tools:

* Quality Control
  
```bash
## Fastqc basic syntax
fastqc input.fastq

# To run quality check on all our sample datasets use wildcards(*) to select all files ending with .fastqsanger
fastqc ./*.fastqsanger

## Multiqc basic syntax
multiqc [directory contain the fastq html reports]

#If the reports are in the current working directory use
multiqc .
```
* Trimming

**Cutadapt** trims  adapter sequences, primers,and any unwanted sequence from your fastq data.

```
#Cutadapt basic syntax
cutadapt -a AACCGGTT -o output.fastq input.fastq
#The sample data used doesnt have adapters.But if the would contain adapters we would run cutadapt as follows

cutadapt -a AACCGGTT -o GSM461177_1_trimmed.fastqsanger GSM461177_1_subsampled.fastqsanger

#-a - sequence of adapter. Replace the AACCGGTT with the adapter sequence in your data
#-o  the output for trimmed reads
```

**TrimGalore** is a perl wrapper around Cutadapt and FastQC.Thus it performs quality check after trimming using cutadapt utilities.
```
#Basic trimgalore syntax
trim_galore [options] <filename(s)>

#Trimgalore run on our sample dataset
trim_galore \
--paired GSM461177_1_subsampled.fastqsanger  GSM461177_2_subsampled.fastqsanger \
--quality 30 \
--fastqc \
--nextera \
--output_dir results/clean_reads

#--paired - 
#--quality - trim low quality ends from reads
#--nextera - 
#--output_dir 
```

* Sample quality plot generated by multiqc

![fastqc_per_base_sequence_quality_plot (4)](https://github.com/Parcelli/Our-Activities/assets/85280870/40a98584-e300-46ee-94b3-be527594e9c4)
```
**Trimmomatic** is a Java executable software used to trim and crop reads.
```
#Trimmomatic basic syntax
java -jar <path to trimmomatic.jar> PE <input 1> <input 2>] <paired output 1> <unpaired output 1> <paired output 2> <unpaired output 2> <options>

#Running trimmomatic on our sample datasets

#basename is a command that strips trailing suffix in a file name
for i in *_1.fastq.gz;
do
    name=$(basename ${i} _1.fastq.gz)
    trimmomatic PE ${i} ${name}_2.fastq.gz\
                   ${name}_1.trim.fastq.gz ${name}_1un.trim.fastq.gz \
                    ${name}_2.trim.fastq.gz ${name}_2un.trim.fastq.gz \
                    HEADCROP:11
done
PE - paired end
HEADCROP -removes the first 11 bases of the reads
```

### 2. Mapping

We need to find the position of each gene on the organisms genome. To do this we ‘map’ or align the reads to the reference genome, if the genome exists. Multiple tools have been designed to do mapping and can be sample specific. These step is usually computationally intensive and in most cases fails on local computers. Cloud computing or running the analysis in a cluster might be a solution if analysis fails on a local PC. 

The input of these tools are FASTA/Q reads and the output is SAM/BAM files. Some common tools include: 

- Bowtie2 - https://github.com/BenLangmead/bowtie2
- SOAP - [http://gaow.github.io/genetic-analysis-software/s/soap/](http://gaow.github.io/genetic-analysis-software/s/soap/)
- STAR - https://github.com/alexdobin/STAR
- HISAT - https://github.com/DaehwanKimLab/hisat2
- TopHat -https://github.com/infphilo/tophat

```bash
## Bowtie2

## SOAP

## STAR

## HISAT2

## TopHat
```

### 3. Analysis of the deferentially expressed genes