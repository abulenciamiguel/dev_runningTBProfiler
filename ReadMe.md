# How to run TBProfiler: ONT?

## 1.   Data transfer
After sequencing, copy the raw files from the local workstation to HPC. </br>
-   Assume that the raw files in the local workstation are located in `C:\data\tb_batch69`
-   Assume that the raw files will be transferred to HPC's `~/raw` directory
-   Open an ubuntu terminal
```
rsync -aPvz /mnt/d/tb_batch69 -e 'ssh -p 1945' ufuomababatunde@192.420.69:~/raw/
```


## 2.   Decompression
Access the HPC
```
ssh -p 1945 ufuomababatunde@192.420.69
```

Assume that the fastq files are located in `~/raw/tb_batch69/no_sample/fastq_pass`. </br>
Create a new directory where the raw files will be copied into
```
mkdir -p ~/tb_batch69/raw

cp ~/raw/tb_batch69/no_sample/fastq_pass/* ~/tb_batch69/raw

cd ~/tb_batch69/raw

unzip */*
```


## 3.   Combining *fastq*
Each barcode corresponds to a sample. </br>
Combine all `fastq` files of a sample into one. </br>
Assume that `Barcode69` corresponds to `TV_5` sample.

```
cd ~/tb_batch69/raw

cat Barcode69/*fastq > TV_5.fastq
```


## 4.   Sample sheet preparation
To save filenames as ID, you can use sed to remove file types.

```
cd ~/tb_batch69/raw

ls *.fastq > ID.txt

sed -i 's/.fastq//g' ID.txt
```

To save the paths
```
cd ~/tb_batch69/raw

find $(pwd)/*.fastq > path.txt
```

Combine both files column-wise with tab as the delimiter
```
cd ~/tb_batch69/raw

paste ID.txt path.txt > sample_sheet.csv
```

Change the delimiter to comma
```
cd ~/tb_batch69/raw

sed -i 's/\t/,/g' sample_sheet.csv
```

Add non-negotiable headers (i.e., `id,read1`) to the `sample_sheet.csv` </br>
```
cd ~/tb_batch69/raw

nano sample_sheet.csv
```


## 5.   Running and Collating results
```
conda activate tbprofiler_env

cd ~/tb_batch69

tb-profiler batch --csv raw/sample_sheet.csv --dir output

cd output

tb-profiler collate
```


## 6.   Check coverage and add headers
```
mkdir -p ~/tb_batch69/output/coverage

cd ~/tb_batch69/output


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

# How to run TBProfiler: Illumina?
