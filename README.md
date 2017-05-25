# lncnet
Functional characterization of lncRNA by integrative network analysis.

## Installation

### Get lncnet
```
git clone git@github.com:zhoujj2013/lncnet.git
```

### Install require python packages

lncnet written by [PERL](https://www.perl.org/). It require python 2.7.X or above and several python packages: matplotlib, networkx, numpy, scikit-learn, scipy, statsmodels etc. These python packages can be installed by pip.

If you don't have pip, please download [get-pip.py](https://bootstrap.pypa.io/get-pip.py), then type:

```
python get-pip.py
```

Install python packages one by one using pip module:

```
cd lncnet
python -m  pip install -r  $INSTALL_DIR/python.package.requirement.txt --user 
```

You can also can run it in python virtual environment, if you don't have superuser privilege.

```
# install virtualenv
pip install virtualenv

cd my_project_folder
virtualenv venv
source venv/bin/activate
python -m  pip install -r  $INSTALL_DIR/python.package.requirement.txt
```


## Build database

We need to prepare dataset for each genome, lncnet analysis rely on these datasets. To this end, we provide BuildDb module that can download dataset from UCSC, NCBI, EBI, mirBase and prepare these dataset automatically.

### Build reference database without denovo lncRNA assembly

At present, we support preparing dataset for mouse(mm9) and human(hg19).

```
cd $INSTALL_DIR
mkdir data
cd data
perl ../BuildDb/BuildDb.pl mm9 ./ > mm9.log
# this program will download the reference data from public databases.
```

### Build reference database with denovo lncRNA assembly

At present, we support preparing dataset for mouse(mm9) and human(hg19).

```
cd ./lncFNTK
mkdir data
cd data
perl ../BuildDb/BuildDb.pl mm9 ./ novel.final.gtf newdb >mm9.log 2>mm9.err
```

## Run testing

Run demo to check whether the package work well.

### Create database

If you haven't built database for mouse(mm9), please run:

```
cd data
perl ../BuildDb/BuildDb.pl mm9 ./
# around 20 mins
```

### Prepare test data for demo

```
cd $INSTALL_DIR
cd demo
cd demo/test_data
# prepare test dataset
sh prepare.sh
cd ..
# around 3 seconds
```

### Write the configure file

You should replace $DBDIR with the directory you create reference database in config.txt.
```
vim config.txt
# replace $DBDIR with the directory you create reference database
```

### Run testing

```
perl ../run_lncnet.pl config.txt
# then make the file
make
# around 15 mins.
```

## Input files

We need to place the input files into configure file. We need to provide expression profile (expr.lst), TFs binding profiles(tf.chipseq.lst), a list of express miRNA (MirRNA_expr_refseq.lst) and potential miRNA binding profile (miRNA.binding.potential.bed). The format for these files should be prepared as follow:

### expr.lst

Contain expression profile for different stages.

```
stage1<tab>geneexpr.table1
stage2<tab>geneexpr.table2
...
stageN<tab>geneexpr.tableN
```

The file format for expression table file:
```
geneid1<tab>rpkm1
geneid2<tab>rpkm2
...
geneidN<tab>rpkmN
```

### tf.chipseq.lst

Contain key transcription factor binding profiles.

```
TF1_gene_symbol<tab>TF1.binding.peaks.bed
TF2_gene_symbol<tab>TF2.binding.peaks.bed
...
TFn_gene_symbol<tab>TFn.binding.peaks.bed
```

The input binding profile is in [bed format](https://genome.ucsc.edu/FAQ/FAQformat.html#format1), the fourth column should be unique binding IDs.

### MirRNA_expr_refseq.lst

Contain the express microRNAs.

The format showed as follow:

```
miRNA1_symbol<tab>refseq_id1
miRNA2_symbol<tab>refseq_id2
...
miRNAn_symbol<tab>refseq_idn
```
### miRNA.binding.potential.bed

A list of potential miRNA binding site in bed format.(Note: the fourth column should be unique.)

```
# chrom<tab>start<tab>end<tab>id
chr1  234 289 mESCs_001
chr1  2834 2890 mESCs_002
...
```

## Output

### Co-expression network

Contain co-expression network.

```
01CoExprNetwork/prefix.CoExpr.int
```

Format:

```
gene1<tab>gene2<tab>interaction_type<tab>score<tab>evidence
...
```

### TF regulatory network

Contain tf regulatory netowrk information.

```
02TfNetwork/TfNetwork.int
```

The format is the sample as Co-expression network.

### MiRNA-gene regulatory network

Contain microRNA-gene interactions.
```
03MirnaNetwork/prefix.MirTargetGeneLevel.txt
```

The format is the sample as Co-expression network.

### Integrative gene regulatory network

Contain all the interactions between 2 genes.
```
04NetworkConstruction/04NetworkStat/mESCs.int.txt
```

The format is the sample as Co-expression network.

### Predicted functional lncRNA and nonfunctional lncRNAs

Predict functional lncRNAs and corresponding FIS. 

```
05FunctionalityPrediction/functional.lncrna.lst
05FunctionalityPrediction/nonfunctional.lncrna.lst
```

The format:

```
id1<tab>FIS1
id1<tab>FIS1
...
idN<tab>FISN
```

### lncRNA annotation

GO annotation result for each predicted functional lncRNAs.
```
06FunctionalAnnotation/lncFunNet.GO.enrich.result.txt
```

The format:

```
id1<tab>FIS1
```
