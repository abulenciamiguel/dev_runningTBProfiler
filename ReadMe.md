# How to run TBProfiler?

## 1.   Data transfer
After sequencing, copy the raw files from the local workstation to HPC. </br>
-   Assume that the raw files in the local workstation are located in `C:\data\tb_ont_FEB292023_batch69`
-   Assume that the raw files will be transferred to HPC's `~/` directory
-   Open an ubuntu terminal
```
rsync -aPvz /mnt/d/tb_batch69 -e 'ssh -p 1945' ufuomababatunde@192.420.69:~/raw/
```


## 2.   Decompression (skip if Illumina)
Access the HPC
```
ssh -p 1945 ufuomababatunde@192.420.69
```

Assume that the fastq files are located in `~/tb_ont_FEB292023_batch69` as shown below. </br>
<img src="https://github.com/abulenciamiguel/dev_runningTBProfiler/blob/main/assets/tbprofiler_1.jpg" width="500"> </br>
Create a new directory where the raw files will be copied into
```
mkdir -p ~/tb_batch69/raw

cp ~/tb_ont_FEB292023_batch69/no_sample/somethinggibberish/fastq_pass/* ~/tb_batch69/raw

cd ~/tb_batch69/raw

gunzip */*
```


## 3.   Combining *fastq*  (skip if Illumina)
Each barcode corresponds to a sample. </br>
Combine all `fastq` files of a sample into one. </br>
Assume that `Barcode69` corresponds to `TV_5` sample.

```
cd ~/tb_batch69/raw

cat Barcode69/*fastq > TV_5.fastq
```


## 4.   Sample sheet preparation
### For ONT
####    To save filenames as ID, you can use `sed` to remove file types.

```
cd ~/tb_batch69/raw

ls *.fastq > ID.txt

sed -i 's/.fastq//g' ID.txt
```


### For Illumina
####    To save filenames as ID, you can use sed to remove file types.
####    Assume that `_L001_R1_001.fastq.gz` is the pattern for the forward reads.

```
cd ~/tb_batch69/raw

ls *_L001_R1_001.fastq.gz > ID.txt

sed -i 's/_L001_R1_001.fastq.gz//g' ID.txt
```

## 5.   Running TBProfiler
### For ONT
```
conda activate tbprofiler_env

cd ~/tb_batch69
```

``` bash
while read line
do
    tb-profiler profile \
    --platform nanopore \
    -t 4 \
    -1 raw/${line}.fastq \
    -p $line \
    --dir output
done < raw/ID.txt
```

### For Illumina
```
conda activate tbprofiler_env

cd ~/tb_batch69
```

``` bash
while read line
do
    tb-profiler profile \
    -t 4 \
    -1 raw/${line}_L001_R1_001.fastq.gz \
    -2 raw/${line}_L001_R2_001.fastq.gz \
    -p $line \
    --dir output
done < raw/ID.txt
```

## 6. Collating results
```
conda activate tbprofiler_env

cd ~/tb_batch69

tb-profiler collate
```


## 6.   Check coverage and add headers
```
mkdir -p ~/tb_batch69/output/coverage

cd ~/tb_batch69/output
```

``` bash
while read sample
do
samtools coverage -H bam/${sample}.bam > coverage/${sample}.coverage.tsv
done < ~/tb_batch69/raw/ID.txt


cat coverage/*coverage.tsv > combined.tsv

sed -i '1 i\#rname\tstartpos\tendpos\tnumreads\tcovbases\tcoverage\tmeandepth\tmeanbaseq\tmeanmapq' combined.tsv
```


## 7.   Result transfer
Open another terminal. </br>
Assume that you will be copying the results in the `C:\tbresults`
```
rsync -aPvz ufuomababatunde@192.420.69:~/tb_batch69 -e 'ssh -p 1945' /mnt/c/tbresults
```


