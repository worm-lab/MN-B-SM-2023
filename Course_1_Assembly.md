# Genome assembly

## Short-read assembly

### Quality control and adapter trimming 

[FastQC](https://github.com/s-andrews/FastQC)
```sh
conda create -n fastqc_env 
conda activate fastqc_env
conda install -c bioconda fastqc
```
```sh
mkdir fastqc_raw
fastqc -o fastqc_raw SRR11849223_1.fastq.gz SRR11849223_2.fastq.gz
```

[TrimGalore](https://github.com/FelixKrueger/TrimGalore)
```sh
conda create -n trimgalore_env
conda activate trimgalore_env
conda install -c bioconda trim-galore
```
```sh
trim_galore --paired --fastqc --gzip -j 8 SRR11849223_1.fastq.gz SRR11849223_2.fastq.gz
```

### Genome assembly

[idba](https://github.com/loneknightpy/idba)
```sh
conda create -n idba_env
conda activate idba_env
conda install -c bioconda idba
```
```sh
gunzip -k SRR11849223_1_val_1.fq.gz
gunzip -k SRR11849223_2_val_2.fq.gz

fq2fa --merge SRR11849223_1_val_1.fq SRR11849223_2_val_2.fq SRR11849223.fasta

idba -l SRR11849223.fasta -o idba_SRR11849223 --num_threads 8
```

## Long-read assembly

### Preliminary analyses 

*k*-mer analyses: [KAT](https://github.com/TGAC/KAT)
```sh
kat hist -t 8 -o kat_hist hifi_reads.fasta.gz
kat gcp -t 8 -o kat_gcp hifi_reads.fasta.gz
```

Distribution: [Nanoplot](https://github.com/wdecoster/NanoPlot)
```sh
conda create -n nanoplot_env 
conda activate nanoplot_env 
conda install -c bioconda nanoplot_env
```
```sh
NanoPlot -o nanoplot_hifi -f png --fasta hifi_reads.fasta.gz -t 8
```

### Genome assembly

[hifiasm](https://github.com/chhylp123/hifiasm)
```sh
git clone https://github.com/chhylp123/hifiasm
cd hifiasm && make

# or

conda create -n hifiasm_env 
conda activate hifiasm_env
conda install -c bioconda hifiasm
```
```sh
hifiasm -t 2 --n-hap 1 -o hifiasm.assembly hifi_reads.fasta.gz
awk '$1 ~/S/ {print ">"$2"\n"$3}' hifiasm.assembly.bp.p_ctg.gfa > hifiasm.assembly.fasta
```

[Flye](https://github.com/fenderglass/Flye) 
```sh
conda create -n flye_env
conda activate flye_env
conda install -c bioconda flye
```
```
flye -t 2 -o flye_default --pacbio-hifi hifi_reads.fasta.gz 
cp flye_default/assembly.fasta flye_assembly.fasta
```
