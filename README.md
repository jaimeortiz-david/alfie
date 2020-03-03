# alfie <img src="alfie/data/alfie.jpeg" height="140" align="right" alt="Alfie"/>
## alignment free identification of eDNA

Alfie is an alignment-free, kingdom level taxonomic classifier for DNA barcode. Alfie classifies sequences using a neural network which takes k-mer frequencies (default k = 4) as inputs and makes kingdom level classification predictions. At present, the program contains trained models for classification of cytochrome c oxidase I (COI) barcode sequences to the taxonomic level: kingdom. The program is effective at classifying sequences >200 base pairs in length, and no alignment information is needed. 

Alfie can be deployed from the command line for rapid file-to-file classification of sequences. This is an effective means of separating contaminant sequences in a DNA metabarcoding or environmental DNA dataset from sequences of interest. 

For increased control, Alfie can also be deployed as a module from within Python. The alfie module contains functions that can aid a user in the training and application of a custom alignment-free classifier, which allows the program to be applied to different DNA barcodes (or genes) or on different taxonomic levels. 


## Installation

Alfie in a python3 program that depends on the python packages: `numpy`(version >= 1.18.1) and `tensorflow`(version>=2.0.0). If you do not have these installed, it is recommended that you install python and the required packages via [anaconda](https://www.anaconda.com/distribution/).

To install alfie, download and unzip this repository. From the terminal, enter the downloaded repository and then run the following command:
```
python3 setup.py install
```
To check that the installation was successful, open a new terminal and run the following command (it should pop up the alfie help menu).
```
alfie -h
```


## Usage 
### Command line interface
Alfie can be run as a stand alone command line interface, just specify an input `.fasta` or `.fastq` file using the `-f` flag, and alfie will conduct classification, splitting the input file into five sub files with sequences from the five kingdoms. The output will be a folder named `alfie_out`. The output folder will contain five files (names same as the input, with a prefix indicating the kingdom) that respectively contain the sequence records corrsponding to the kingdom indicated in the file prefix.

You can test this out using the example files shipped with aflie.
```
#from within the alfie folder
alfie -f alfie/data/example_data.fasta

# This will create a folder: alfie_out
# with the files: 
# animalia_example_data.fasta bacteria_example_data.fasta fungi_example_data.fasta plantae_example_data.fasta protista_example_data.fasta

```

For very large files, the input sequence file may need to be processed in a batch fashion. This will run more slowly, but less sequences will be held in memory at once. The batch size (number of sequences) is specified with the `-b` flag. This flag isn't required, and should be used only if the program is crashing (finding the optimal value for your own machine will require some trial and error).
```
alfie -f alfie/data/example_data.fastq -b 100
```

By default, alignment free classification is performed using the default feature set (4mer frequencies) and the corresponding, pre-trained neural network (trained on `COI-5P` sequence fragments of varying lengths). A user can pass an alternative neural network to make predictions with using the `-m` flag. If this option is exercised and the model has not been trained on 4mers, then the `-k` flag must be used to ensure the proper set of kmer features are generated to match the neural network input structure.

```
#example using the 6mer model that ships with alfie, note the -k 6 option is required
alfie -f alfie/data/example_data.fastq -m alfie/data/dnn_model_6mers -k 6
```

### The alfie library

For a control, the alfie module can be deployed from within Python. The module contains a classification function, functions for fasta and fastq input and output, and helper functions to aid a user is training and deploying a customized variant of the classifier.

Deploying alife as a kingdom-level classifier from within python is simple! 

```
# example data included in module
from alfie import example_fasta

example_fasta[0]

{'name': 'seq1_plantae',
 'sequence': 'TTCTAGGAGCATGTATATCTATGCTAATCCGAATGGAATTAGCTCAACCAGGTAACCATTTGCTTTTAGGTAATCACCAAGTATACAATGTTTTAATTACAGCACATGCTTTTTTAATGATTTTTTTTATGGTAATGCCTGTAATGATTGGTGGTTTTGGTAATTGGTTAGTTCCTATTATGATAGGAAGTCCAGATATGGCTTTTCCTAGACTAAATAACATATCTTTTTGACTTCTTCCACCTTCTTTATGTTTACTTTTAGCTTCTTCAATGGTTGAAGTAGGTGTTGGAACAGGATGAACTGTTTATCCTCCCCTTAGTTCGATACAAAGTCATTCAGGCGGAGCTGTTGATTTAGCAATTTTTAGCTTACATTTATCTGGAGCTTCATCGATTTTAGGAGCTGTCAATTTTATTTCTACGATTCTAAATATGCGTAATCCTGGGCAAAGCATGTATCGAATGCCATTATTTGTTTGATCTATTTTTGTAACGGCA'}
 #fastq dict will have 'strand' and 'quality' as well
```

```
#import classifier
from alfie.classify import classify_records

#classify the sequences in example_fasta
seq_records, predictions = classify_records(example_fasta)

```

```
from alfie.classify import decode_predictions

kingdom_labels = decode_predictions(predictions)
kingdom_labels[:5]

```

For a more detailed demonstration of the alfie module's functionality please [consult the jupyter notebook included with this repository](https://github.com/CNuge/alfie/blob/master/example/custom_alfie_demo.ipynb). The notebook covers sequence IO and kingdom-level classification in more detail and also provides examples of how to train and deploy a custom, alignment-free classifier with alfie. Custom classifiers can be implemented for any taxonomic level or DNA barcode - just bring your own training data!






### Acknowledgements

This program is dedicated to my Dad's dog, Alfie (pictured in readme). He is a good boy.

