See https://github.com/homopolymer/RAMBL for the original repository. Here I change the code to make it work in 2022. Additionally I add an environment and clarify how to install and how to run the tool.

## 1. Git clone and create env through conda

Run:
```
git clone https://github.com/AlessioMilanese/RAMBL.git
cd RAMBL/
conda env create -f rambl_env.yaml python=2.7
```

You need conda (I would use [miniconda](https://docs.conda.io/en/latest/miniconda.html)). In this way all dependecies (except `GCC >= 4.9.1`) can be installed directly.

Finally, activate the env with:
```
source activate RAMBL
```

## 2. Install

Run:
```
export CXX=$(which g++)
python setup.py install --prefix=$PWD
```

We need some other reference files:
```
git clone https://github.com/homopolymer/RAMBL_Test/
cd RAMBL_Test
```

Within this directory there are some files that are needed as input:
- 99_otus.fasta
- 99_otu_taxonomy.txt
- 99_otus_unannotated.tree
- 99_otus.fasta.fai
- 99_otus_aligned.fasta


You need to run `bowtie2-build 99_otus.fasta 99_otus_bowtie` within the `RAMBL_Test` repo.

We will use this repo, I saved it in a variable:
```
RAMBL_DATA=pwd
```

## 3. Run on a metagenomic sample

Given three files from a metagenomic sample:
 - all_reads_forward.fq
 - all_reads_reverse.fq
 - all_reads_single.fq

We need to create a bam file:
```
bowtie2 --sensitive-local -p 8 \
        -x $RAMBL_DATA"/99_otus_bowtie" \
        -1 all_reads_forward.fq" \
        -2 all_reads_reverse.fq" \
        -U all_reads_single.fq" \
        -S result.sam

samtools view -bS result.sam > result.bam
samtools sort result.bam result_sorted
samtools index result_sorted.bam
echo result_sorted.bam" > bams.fofn
```

The last line should actually contain the full path.

Now we need to create a `data_info.txt` file:
```
echo "BamFiles=bams.fofn" > data_info.txt
echo "GeneSeq="$RAMBL_DATA"/99_otus.fasta" >> data_info.txt
echo "GeneTax="$RAMBL_DATA"/99_otu_taxonomy.txt" >> data_info.txt
echo "GeneTree="$RAMBL_DATA"/99_otus_unannotated.tree" >> data_info.txt
echo "GeneIndex="$RAMBL_DATA"/99_otus.fasta.fai" >> data_info.txt
echo "GeneAlign="$RAMBL_DATA"/99_otus_aligned.fasta" >> data_info.txt
```

And can finally run RAMBL with:
```
python $RAMBL -c 8 -v data_info.txt 2>&1 | tee log.txt
```

Where `8` is the number of cores.

## Changes from the original code

- Add a library in StrainCall (f7f78663e0ff647c2f725e18bd415f210d2cc8d6)
