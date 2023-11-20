# Lantern_Pharma_Datathon

# Resources Used in the workflow:

> 1. Data Extraction : The research articles were extracted using API services available on Pubmed Central and BIORXIV. A total of approximately 24648 papers were attempted to be extracted as JSON from Pubmed Central. All research articles published on BIORXIV since 2014-01-01 till 2023-11-10 were attempted to be extracted as JSON. 
> 2. Data Pre-processing : Drugbank was referenced for extracting commonly known inhibitors and substrates of BCRP/ABCG2. Using this, the dataset was initially automatically annotated. 
> 3. Model Architecture/Word vectors : For Named Entity Recognition of Drugs, a two phased approach was used, first to extract using the Python library drug-named-entity-recognition which is trained on Drugbank etc databases, and secondly using Named Entity recognition spacy model trained on the BC5CDR corpus.
     For deriving word vector representations, BioWordVec and BioSentVec pre-trained embeddings for biomedical words and sentences were used. 
     
# Method of document searching and collection:
  
> 1. The data extraction pipeline implemented, was completely automated. PubMed central APIs and BIORXIV APIs were studied and utilized for automatically deriving relevant documents using search keywords, collecting these documents, extracting JSON/XML objects for the documents, and finally extracting text, tables from the given documents. A total of 24648 published papers were attempted to be extracted on PubMed and a total of 293996 published papers were matched with keywords on BIORXIV (out of which 4561 were found to have matching keywords). The numbers were confirmed on PubMed Central using a manual search to ensure the accuracy of collected articles. 
> 2. The relevant documents were then processed through an XML/JSON extractor based on the format for extracting text from relevant sections and processing the same. The documents were processed in batches of 200 for optimal efficiency. 
> 3. This automated pipeline makes the process adaptable to use for extracting relevant articles and processing them for text and tables for any given search word!

# Workflow for Extracting Drug Names

>1. To extract drug names various entity recogition techniques were studied. Finally, a combination of using the drug_name_entity_recognition python library and named entity recognition spacy model trained on BD5CDR biomedical text corpus, were used. This led to added noise but also ensured the extraction of drugs like ko143 which were initially ignored by drug_name_entity_recognition due to its limited vocab. A total of 211889 mentions of drugs in context of BCRP/ABCG2 were extracted from all the documents combined. This dataset was then annotated using well known inhibitors and substrates of BCRP (Drugbank). Initially a rule based approach for extracting relationships was implemented but due to high noise and low accuracy, a more sophisticated approch was adopted.
>2. Gaining inspiration from previous research in this domain, each mention of entity and drug were encoded in each record with special characters <> and tags e1, e2 etc
>3. Pre-trained word embeddings from Biowordvec embeddings (biomedical word and sentence embeddings trained using PubMed and the clinical notes from MIMIC-III Clinical Database. ) were loaded for maximum contextual representation. 
>4. This is a relation extraction task and hence, a BiLSTM approach was used for model architecture. The Model Architecture was experiemented with and finally a CNN, stacked BiLSTM layers + Attention performed the best. CNN helps capture local patterns might be especially useful for relation extraction where the relation between two entities can be local to a small window of text. BiLSTM stacking can help the model to learn hierarchical representations of the sequences.
>5. The model was trained using the annotated dataset (Annotated earlier using well known inhibitors and substrates) for 10 epochs to prevent overfitting. Performance on Test set : 81.03 %. 
>6. The model performance can be improved with a better annotated dataset. Furthermore, it can be really helpful in treating this as a multiclass classification as to open the room for a drug to have no relation or non-Inhibitor/Substrate relationship with a protein but still be mentioned in the text thereby reducing noise and improving accuracy. (This was experimented with using 100 manually annotated records where the drug had none of inhibitor/substrate relationship with BCRP/ABCG2) 
>7. SMILE notations were extracted for each drug and a reverse methodology was used to eliminate drugs with no SMILE string as it is likely that it was not a drug in the first place but rather an element in biomedical notations which our NER model extracted. This will help reduce noise in the output. 

# Checking the validity of results

>1. Since the dataset used for training the model was annotated using Drugbank as a source, the model can be pertained to have highly validated results with the test accuracy being 81.03%. This is a more automated approach which was required as there are more than 200K individual records in the dataset and it is highly unfeasible/inefficient to manually annotate records for training/testing. 
>2. Automating further provides the scope of reproducing this mechanism for any other protein/drug interactions. 

# Adaptability to other projects

>1. Since the entire process has been automated from Data Extraction to model training, it can be applied to a range of different Proteins for extracting Inhibitors/Substrates of proteins using NLP. Documents will be automatically downloaded based on provided keywords and processed for extracting drugs and protein mentions. Further more, a portion of the extracted data can be easily annotated using well known inhibitors and substrates for any given protein.  This annotated data can be used to train a custom model for any given protein specifically garnered to the protein and increasing performance by adjusting hyperparameters and model architecure based on performance. Furthermore, the automated SMILES extractor can be used to get the SMILE notations for each extracted drug. 
