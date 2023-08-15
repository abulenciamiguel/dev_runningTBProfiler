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

Assume that the fastq files are located in `~/raw/tb_batch69/no_sample/fastq_pass`
```
cd ~/raw/tb_batch69/no_sample/fastq_pass

unzip */*
```


## 3.   Combining *fastq*
Each barcode corresponds to a sample. </br>
Combine all `fastq` files of a sample into one. </br>
Assume that `Barcode69` corresponds to `TV_5` sample.

```
cd ~/raw/tb_batch69/no_sample/fastq_pass

cat Barcode69/*fastq > TV_5.fastq
```


## 4.   Sample sheet preparation
To save filenames as ID, you can use sed to remove file types.

```
cd ~/raw/tb_batch69/no_sample/fastq_pass

ls *.fastq > ID.txt

sed -i 's/.fastq//g' ID.txt
```

To save the paths
```
cd ~/raw/tb_batch69/no_sample/fastq_pass

find $(pwd)/*.fastq > path.txt
```

Combine both files column-wise with tab as the delimiter
```
cd ~/raw/tb_batch69/no_sample/fastq_pass

paste ID.txt path.txt > sample_sheet.csv
```

Change the delimiter to comma
```
cd ~/raw/tb_batch69/no_sample/fastq_pass

sed -i 's/\t/,/g' sample_sheet.csv
```

Add non-negotiable headers (i.e., `id,read1`) to the `sample_sheet.csv` </br>
```
cd ~/raw/tb_batch69/no_sample/fastq_pass

nano sample_sheet.csv
```


## 5.   Running


## 6.   Result transfer


# How to run TBProfiler: Illumina?
