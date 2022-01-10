See https://github.com/homopolymer/RAMBL for the original repository. Here I change the code to make it work in 2022. Additionally I add an environment and clarify how to install and how to run the tool.


========
1. Git clone and create env through conda
========

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

========
2. Install
========

Run:
```
export CXX=$(which g++)
python setup.py install --prefix=$PWD
```

========
3. Run on a metagenomic sample
========

