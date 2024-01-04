Project Title: 
Prediction of biofilm-forming Staphylococcus aureus isolates based on the presence/absence of kmers from biofilm-related genes in the isolate genomes.

Models to be built:
1- RandomForest using the full kmer presence/absence matrix of dimensions (120, 22345)
2- Support Vector Machine using the full kmer presence/absence matrix of dimensions (120, 22345)
3- Support Vector Machine using a filtered kmer presence/absence matrix of dimensions (120, 1365) of the kmers used to build the RandomForest model only.



Project Phases:
Phase 0: Data collection
	- Obtaining genome assembly fasta files for the 120 genomes to analyzed. These files can be obtained from https://drive.google.com/drive/folders/1yHg9BbKE4wBZ_OdCaE4qT4meI9pWBfkD
	- Renaming genome assembly files so that they all have consistent naming "CA_[0-9]{3}.fasta" (i.e. CA_001.fasta, CA_010.fasta, CA_100.fasta, etc.).
	This is done using the script in Source/DSK_file_preparation.txt

Phase 1: Preprocessing
1- Obtaining the kmers: this is done using a kmer-counting package (DSK in Linux) to extract the kmers in the 12 biofilm-related genes and in the 120 isolate genomes.
This is done using the script Source/DSK_script_for_genomes.txt file.
	Inputs: 
		- 120 isolate genome fasta files. 
		The script used on these files can be found in Source/DSK_script_for_genomes.txt file.

		- 12 gene fasta files along with the gene_list.txt file in Data/Genes.
		The gene_list.txt file contains the gene fasta filenames and is used as input to the DSK kmer counting package so that it combines the kmers
		of all these genes in 1 output file. 

	Outputs: 
		- 120 .txt files containing the kmers present in each isolate on its own (Data/Genome_Kmers).
		- 1 .txt file containing the kmers of the 12 biofilm-related genes together (Data/Genes/all_gene_kmers.txt).

2- Creating the kmer presence/absence matrix using the python script in the Source/Lydia_Project_Preprocessing.ipynb file
	Inputs: 
		- 120 genome kmer .txt files in Data/Genome_Kmers
		- all_gene_kmers.txt file in Data/Genes

	Outputs: 
		- FullGeneKmerMatrix.csv file in the Data subdirectory. This file is the kmer presence/absence matrix which will be used as input to the RandomForest and the 
		first Support Vector Machines model to be built in Phases 2 & 3 of the project (see below). The kmer matrix has the isolate IDs (=120) as rows, kmers (=22435) as columns and the Labels are 0 
		(non-biofilm forming) and 1 (biofilm-forming).


Phase 2: RandomForest model for prediction and feature (kmer) selection. This was done using the Source/Lydia_Project_RandomForest_model.ipynb python script
	Inputs:
		- Data/FullGeneKmerMatrix.csv file.
	
	Outputs:
		- ImportantKmers.csv file. 
		NOTE: This file is NOT the filtered kmer presence/absence matrix. It is an intermediate file created by the script that contains the selected kmers and 
		their corresponding importances which will be used to build the filtered kmer presence/absence matrix (FilteredKmerMatrix.csv) for the last SVM model.


Phase 3: Support Vector Machines model for prediction using the full kmer presence/absence matrix. This was done using the Source/Lydia_Project_SVM_model_NoFS.ipynb
	Inputs:
		- Data/FullGeneKmerMatrix.csv file.

Phase 4: Support Vector Machines model for prediction using the filtered kmer presence/absence matrix. This was done using the Source/Lydia_Project_SVM_model_FS.ipynb
	Inputs:
		- ImportantKmers.csv file. 
		- Data/FullGeneKmerMatrix.csv file.
		These two files were used to build the new reduced kmer presence/absence matrix to be used as input to this last SVM model.

	Outputs:
		- Data/FilteredKmerMatrix.csv file.

NOTE: To run the 3 models, only the FullGeneKmerMatrix.csv and FilteredKmerMatrix.csv files in the Data subdirectory along with the the python scripts:
1- Source/Lydia_Project_RandomForest_model.ipynb
2- Source/Lydia_Project_SVM_model_NoFS.ipynb
3- Source/Lydia_Project_SVM_model_FS.ipynb starting step 3
Therefore, the DSK code .txt files, the Source/Lydia_Project_Preprocessing.ipynb python script and the first 2 steps in Source/Lydia_Project_SVM_model_FS.ipynb are only needed for preprocessing
and input preparation to the models.
