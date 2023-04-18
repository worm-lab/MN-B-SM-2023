# Genome assembly

## Short-read assembly

## Long-read assembly

### Preliminary analyses 

*k*-mer analyses: [KAT](https://github.com/TGAC/KAT)
```sh
kat hist -t 2 -o kat_hist hifi_reads.fasta.gz
kat gcp -t 2 -o kat_gcp hifi_reads.fasta.gz
```

Distribution: [Nanoplot](https://github.com/wdecoster/NanoPlot)
```sh
conda create -n nanoplot_env 
conda activate nanoplot_env 
conda install -c bioconda nanoplot_env
```
```sh
NanoPlot -o nanoplot_hifi -f png --fasta hifi_reads.fasta.gz -t 2
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
