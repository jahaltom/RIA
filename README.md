****Prerequisites****

Using Conda 4.10.3, create the conda enviroment and activate:
```
conda env create -f environment.yml
conda activate Ancestry
```


****Data Preparation****

**1000 Genomes Project:**
The snakemake script "Prepare_1KGP" downloads chr(1-22) level VCF files from 1000 Genomes Project phase 3 on GRCh38 (https://doi.org/10.12688/wellcomeopenres.15126.2) and filters out INDELs along with indexing the resulting VCF. It also creates the interval lists needed fot the analysis. 
```
snakemake -j 22 -s Prepare_1KGP --cluster "sbatch -t 00:30:00 -c 4 -N 1"
```

**GRCh38 Reference Genome**
The bash script "Prepare_Reference_Genome" will download the GRCh38 fasta,gtf, and create a seqence dictionary and index file for the fasta. It also creates a STAR index.
```
sbatch Prepare_Reference_Genome
```

****Raw data retrieval from SRA, QC, and STAR 2-Pass****

This snakemale script takes in a list of run accession IDs and fetches the raw fastq files from SRA and then uses Trimgalore for QC. The reads are then ran through STAR 2-Pass mode for enhanced novel SJ detection. The SJ.out.tab file for the 2nd pass is made by combining all SJ.out.tab files from the first pass and removing SJ's that are supported by 2 or less unique mappers. 

These may need to be ran befor running the snakemake script.

```
export OMP_NUM_THREADS=3
export GIT_PYTHON_REFRESH=quiet 
```

```
snakemake -j 50 -k -s STAR_SRA --cluster "sbatch -t 10:00:00 -c 30 -N 1"
```




