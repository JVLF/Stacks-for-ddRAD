# Demultiplexing and cleaning 

1. Decompress file to a working directory: `$ tar -xvf /path/to/file/lane1.tar -C /path/to/directory/`
2. See inside some files *.fq: `$ less *.fq.gz`
3. Copy or write the file with the list of barcodes into the working directory. See if the barcode is located inline or indexed.
4. Clean, discard low quality reads and recover some others whith *process_radtags* command:
`$ process_radtags -P -p ./Tutorial/working/clean/lane2/ -b ./Tutorial/working/clean/lane2/lane2_barcodes -o ./Tutorial/working/clean/lane2/ddsamples/ --renz_1 nlaIII --renz_2 mluCI -i gzfastq -c -q -r --inline_index`
[command instructions](http://catchenlab.life.illinois.edu/stacks/comp/process_radtags.php)
5. Concatenate each sets of 4 output files (for each pair of barcodes)and rename the *.fq files to use a more meaningful name. 

> Write a file with the original output names:
`$ ls *.gz | sed -E 's/^(sample_)([ACGT\-]+).+/\2/' | uniq -d | cat > indv_samples`

> Concatenate each set of files (those with the same pair of barcodes are one set and represent one individual):
```
$ mkdir samples_concat`
$ for i in `cat indv_samples`; do cat sample_$i* > concat_samples_$i.fq.gz; done
```
Change the name of the files for a more meaningful name (For exapamle: PP01 o SF22)
```
for ((i=01; i<=48; i++)); do mv concat_samples/*.fq.gz concat_samples/indv_$i.fq.gz; done
ls | cat -n | while read n f; do mv "$f" "indv_$n.fq.gz"; done
for ((i=01; i<=9; i++)); do mv "indv_$i.fq.gz" "indv_0$i.fq.gz"; done
```
