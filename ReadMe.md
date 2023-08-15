# How to run TBProfiler: ONT?
## 1.   Data transfer
After sequencing, copy the raw files from the local workstation to HPC. </br>
-   Assume that the raw files in the local workstation are located in `C:\data\tb_batch69`
-   Assume that the raw files will be transferred to HPC's `~/raw` directory
-   Open an ubuntu terminal
```
rsync -aPvz /mnt/d/tb_batch69 -e 'ssh -p 2022' ufuomababatunde@192.420.69:~/raw/
```


## 2.   Decompression
## 3.   Combining *fastq*
## 4.   Sample sheet
## 5.   Running
## 6.   Result transfer