# *de novo* assembly of RAD tags (without a genome) for a STRUCTURE Analysis

Once you have your cleaned and demultiplexed data, you can proceed to assemble RAD tags into a **catalog**
First you need to have the FastQ file unziped, so you can use UNIX commands to count the number of reads you have per sample

```
gunzip lib01_samp01.fq.gz
gunzip lib02_samp01.fq.gz
```

Now you can use `grep` or `wc -l` to count the number of reads that are in each file. Example:

```
grep -c "^@" lib01_samp01.fq
wc -l lib01_samp01.fq
```

Now you can proceed to run the `ustacks` command. This command will take some time, so you can use `/usr/bin/time -v --output time.txt` to save the parameters of the run and compare between samples.
For the `ustacks` be sure to enable the parameters you'll need. You have to specify the file type `-t fastq` , and make a directory to save the output, so you can specify the path `-o ./path/output_directory/` , then choose an id for the samples output `-i 1` , enable the Removal algorithm and the the Deleveraging algorithm (used for resolving over merged tags). There are other parameters you can change like the number of mismatches allowed between stacks, minimum coverage, or even the number of threads to use, but for more information see the manual:

[*ustacks* Manual](http://catchenlab.life.illinois.edu/stacks/comp/ustacks.php)

In this example the complete command will be like this:

```
/usr/bin/time -v --output time.txt ustacks -t fastq -f ./samples/lib01_samp01.fq -o ./stacks/ -i 1 -d -r -p 15
# This is using the defaults -m 2 -M 2 -N (M+2)
```

