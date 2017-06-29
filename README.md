# lncFunTK
LncRNA Functional annotation toolkit with integrative network analysis.

## Installation

### Get lncFunTK
```
git clone https://github.com/zhoujj2013/lncfuntk.git
```

### Install require python packages

lncFunTK written by [PERL](https://www.perl.org/) and [python](https://www.python.org/). It require python 2.7.X or above and several python packages: matplotlib, networkx, numpy, scikit-learn, scipy, statsmodels etc. These python packages can be installed by [pip](https://pypi.python.org/pypi/pip).

If you don't have pip, please download get-pip.py from https://bootstrap.pypa.io/get-pip.py, then type:

```
wget --no-check-certificate https://bootstrap.pypa.io/get-pip.py
python get-pip.py
```

Install python packages one by one using pip module:

```
cd lncfuntk
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


## Obtain supporting dataset

Additional dataset is needed for lncFunTK analysis. Those dataset were downloaded from from UCSC, NCBI, EBI, mirBase and were preprocessing these dataset automatically by BuildDB module. You can obtain supporting dataset from [mm9](http://137.189.133.71/zhoujj/lncfuntk/mm9.tar.gz), [mm10](http://137.189.133.71/zhoujj/lncfuntk/mm10.tar.gz), [hg19](http://137.189.133.71/zhoujj/lncfuntk/hg19.tar.gz), [hg38](http://137.189.133.71/zhoujj/lncfuntk/hg38.tar.gz) from our server and you also can get the newest version from public databases by BuildDB module.

### Download supporting dataset

At present, we support downloading dataset for mouse(mm9, mm10) and human(hg19, hg38).

```
cd $INSTALL_DIR
cd data
wget http://137.189.133.71/zhoujj/lncfuntk/mm9.tar.gz
tar xzvf mm9.tar.gz
```

### Build reference database with denovo lncRNA assembly

At present, we support preparing dataset for mouse(mm9) and human(hg19).

```
cd ./lncFNTK
mkdir data
cd data
perl ../bin/BuildDb/BuildDb.pl mm9 ./ novel.final.gtf newdb >mm9.log 2>mm9.err
```

### Create the latest supporting data by yourself

We will update supporting data in our server every month. If you want to download the latest supporting dataset, you can build it by yourself with BuildDB module.

```
cd data
# if mm9 directory is exists.
rm -r mm9
perl ../bin/BuildDb/BuildDb.pl mm9 ./ > mm9.log
# this program will download the reference data from public databases.
# Be patient, at least 30 mins are needed for this step.
```

## Run testing

After you installed the lncFunTK package, you can run demo to check whether the package work well (the data was well prepared in ./demo).

### Create database

If you haven't built database for mouse(mm9), please run:

```
cd $INSTALL_DIR
cd data
wget http://137.189.133.71/zhoujj/lncfuntk/mm9.tar.gz
tar xzvf mm9.tar.gz
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
perl ../run_lncfuntk.pl config.txt
# then make the file
make
# around 15 mins.
# you can check the report (index.html) in 07Report.
firefox ./index.html
```

## Input files

We need to provide the input files in configure file. We listed the files as follow: 
1. Gene expression profile (gene.expr.lst, from time serise RNA-seq analysis);
2. TFs binding profiles(tf.chipseq.lst, from multiple TF chipseq analysis);
3. Potential miRNA binding profile (miRNA.binding.potential.bed, from Ago CLIP-seq analysis);
3. a list of express miRNA (MirRNA_expr_refseq.lst).

The format for these files should be prepared as follow:

```
./
├── config.txt
└── test_data
    ├── GeneExpressionProfiles
    │   ├── CM.expr
    │   ├── CP.expr
    │   ├── EM.expr
    │   ├── gene.expr.lst
    │   └── mESCs.expr
    ├── MirnaBindingProfiles
    │   └── miRNA.binding.potential.bed
    ├── MirRNA_expr_refseq.lst
    ├── prepare.sh
    └── TfBindingProfiles
        ├── Brd4.bed
        ├── Esrrb.bed
        ├── Klf4.bed
        ├── Nanog.bed
        ├── Nr5a2.bed
        ├── Pou5f1.bed
        ├── Prdm14.bed
        ├── Smad3.bed
        ├── Sox2.bed
        ├── Stat3.bed
        ├── Tcf3.bed
        ├── tf.chipseq.lst
        └── Tfcp2l1.bed
```

### Gene expression profiles (GeneExpressionProfiles/gene.expr.lst)

Contain expression profiles from different stages.

```
stage1<tab>geneexpr.table1 (the corresponding expression profile for stage1)
stage2<tab>geneexpr.table2
...
stageN<tab>geneexpr.tableN
```

The file format for expression table file:

```
geneid1<tab>rpkm1 (the corresponding expression level for geneid1)
geneid2<tab>rpkm2
...
geneidN<tab>rpkmN
```

### TF binding profiles (TfBindingProfiles/tf.chipseq.lst)

Contain key transcription factor binding profiles.

```
TF1_gene_symbol<tab>TF1.binding.peaks.bed
TF2_gene_symbol<tab>TF2.binding.peaks.bed
...
TFn_gene_symbol<tab>TFn.binding.peaks.bed
```

The input binding profile is in [bed format](https://genome.ucsc.edu/FAQ/FAQformat.html#format1), the fourth column should be unique binding IDs.

### Mirna binding profiles (MirnaBindingProfiles/miRNA.binding.potential.bed)

A list of potential miRNA binding site in bed format.(Note: the fourth column should be unique.)

```
# chrom<tab>start<tab>end<tab>id
chr1  234 289 mESCs_001
chr1  2834 2890 mESCs_002
...
```

### Express miRNA list (MirRNA_expr_refseq.lst)

Contain the express microRNAs.

The format showed as follow:

```
miRNA1_symbol<tab>refseq_id1
miRNA2_symbol<tab>refseq_id2
...
miRNAn_symbol<tab>refseq_idn
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
07Report/GeneRegulatoryNetwork.interaction.txt
```

The format is the sample as Co-expression network.

### Predicted functional lncRNAs and their annotation

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

GO annotation result for each predicted functional lncRNAs.
```
07Report/FunctionalLncRNA.txt
```

The format:

```
id1<tab>FIS1<tab>GoTermId<tab>GO DESC<tab>pvalue<tab>adjust-pvalue<tab>neighbor genes
```
