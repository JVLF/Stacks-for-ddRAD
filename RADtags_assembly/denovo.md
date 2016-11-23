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

## Understanding *de novo* assembly of RAD tags

For the `ustacks` be sure to enable the parameters you'll need. You have to specify the file type `-t fastq` , and make a directory to save the output, so you can specify the path `-o ./path/output_directory/` , then choose an id for the samples output `-i 1` , enable the Removal algorithm and the the Deleveraging algorithm (used for resolving over merged tags). There are other parameters you can change like the number of mismatches allowed between stacks, minimum coverage, or even the number of threads to use, but for more information see the manual:

[**_ustacks_ Manual**](http://catchenlab.life.illinois.edu/stacks/comp/ustacks.php)

In this example the complete command will be like this:

```
/usr/bin/time -v --output time.txt ustacks -t fastq -f ./samples/lib01_samp01.fq -o ./stacks/ -i 1 -d -r -p 15
# This is using the defaults -m 2 -M 2 -N (M+2)
```

To learn more about the meaning of each parameter see this Tutorial:
[How do the major Stacks parameters control the de novo formation of stacks and loci?](http://catchenlab.life.illinois.edu/stacks/param_tut.php)

To see what do the columns of the outputs mean see the [**Stacks Manual**](http://catchenlab.life.illinois.edu/stacks/manual/index.php#files) 

To answer these questions you can run the command following each one.
- How many repetitive reads are removed?
  Raw reads that are placed in a stack are refered to as primary reads, if you substract the reads in the stack to the raw reads in the   initial file, you'll have the total removed.
  
  ```
  a=`grep -c "^@" ./samples/lib01_samp01.fq`
  b=`grep -c "primary" ./stacks/lib01_samp01.tags.tsv`
  c=`grep -c "secondary" ./stacks/lib01_samp01.tags.tsv`
  expr $a - $b - $c
  ``` 
  
- How many putative alleles are formed in each sample?
  
  ```
  wc -l ./stacks/lib01_samp01.alleles.tsv
  # Minus 1 because the first line is the title
  ``` 
  
- How many loci are formed from putative alleles?
  
  `awk '{print $3" "$9" "$10}' ./stacks/lib01_samp01.alleles.tsv | uniq -c | wc -l` 
  
- How many of the secondary reads are aligned? Raw reads that are set aside are referred to as secondary reads. 
  
  `grep -c "secondary" ./stacks/lib01_samp01.tags.tsv`
  
- How does the depth of coverage change after each stage of the algorithm?

## *de novo* assembly of RAD tags without a reference genome

This example is with a set of files, each one contain the reads for one individual. Once you have them in a directory (i.e. */denovo/samples/*) you can proceed to generate a file named popmap, this will specify to which population each individual belong. The information must be presented like this:

*\<sample file prefix>\<tab>\<population ID>*

```
ls ./samples/ | sed -r 's/^([a-z]+)_([0-9]{4})\.([0-9]+)\.fa/\1_\2\.\3 \1/' > popmap 
```

For better understanding, i'll quote Julian from the Stacks manual for the [denovo_map.pl command](http://catchenlab.life.illinois.edu/stacks/comp/denovo_map.php):

> *The __denovo_map.pl__ program will execute the Stacks pipeline by running each of the Stacks components individually. It is the simplest way to run Stacks and it handles many of the details, such as sample numbering and loading data to the MySQL database, if desired. The program performs several stages, including: __ustacks__, __cstacks__, __sstacks__ and, if specified, __population__ and __index_radtags.pl__*

Continue...
