Models and workflow
=================


Single-cell integration models combine data from different single-cell RNA sequencing studies or samples, correcting for technical differences between batches. This allows researchers to more accurately cluster cells and identify cell types across various datasets.
Conditional neural networks have been shown to be competitive in correcting for batch effects and integrating large-scale reference atlases. These atlases in turn have been successfully utilized as references to gain insight into new experiments. 
In particular, methods have been developed to map new datasets onto reference atlases in order to transfer existing knowledge, such as cell type labels, from these references to the newly mapped data. ScArches is an example of such a tool that enables query-to-reference mapping, and is used as the backbone for ArchMap. 
ScArches is a deep learning python framework that is novel in its ability to extend to any conditional neural network-based data-integration method.

We currently support the following models for mapping: scVI, scANVI and scPoli. 

scVI
---------
Single-cell Variational Inference (scVI) is an unsupervised deep learning model that does not use cell type labels for training. Generally, it takes less time to train in comparison 
to scANVI and scPoli.

scANVI
---------
single-cell ANnotation using Variational Inference (scANVI) is a semi-supervised variant of scVI designed to leverage any available cell type annotations. This model has been shown to perform better 
integration if cell type labels are partially/fully available.

scPoli
---------
Single-cell population level integration (scPoli) is a semi-supervised conditional generative model that has been shown to be competitive in single cell data integration and cell type label transfer with other models inclduign scVI, scANVI, and Seurat v3. scPoli incorporates built-in cell type label classification and uncertainty estimation. scPoli learns both cell type and sample embeddings, allowing for better scalability with respect to number of samples to be integrated.

Workflow
---------
All atlases on the ArchMap website have been integrated with either scVI, scANVI, or scPoli. These pre-trained integration models are used to map new query datasets uploaded by the user to the reference atlas associated with the model. The mapping results are then evaluated and a UMAP of the combined latent representation for the query and reference is generated. 
Unlabeled cell types from the query are classified using labeled cell types from the reference atlas. Post-mapping, the combined query and reference can be visualized using ArchMap's cellxGene plugin and the combined results can be downloaded in .h5ad format.


Mapping evaluation
=================
During processing, performance metrics to quantify the quality of the mapping are calculated. These metrics include the percentage of reference genes in the query data, the percentage of cells labelled “Unknown”, the percentage of query cells with anchors, and the cluster preservation score. 

For the percentage of reference genes in the query data, a value less than 85% may contribute to poor mapping quality. The percentage of cells that are labelled as "Unknown" during cell type label transfer is based on the Euclidean uncertainty score for each query cell. A query cell is classified as having an "Unknown" cell type if its Euclidean uncertainty score is above 0.5. 
The percentage of query cells with anchors represents query cells that have at least one mutual nearest neighbour among the cells of the reference dataset. These mutual nearest neighbours are termed anchors. A lower score may be returned for query datasets from the same tissue as the reference, but with a large proportion of cells from diseased donors. For example, mapping a dataset consisting of samples from healthy tissue to the HLCA gives a percentage of query cells with anchors of 63%. However, mapping a dataset consisting of samples from diseased tissue to the HLCA gives a percentage of query cells with anchors of 54%. 
The cluster preservation score assesses how well Leiden clustering of the query dataset is preserved after the mapping process. The mean entropy of cluster labels within each neighbourhood is computed for both the original and integrated query. The median of the differences in mean entropy between the original and integrated query is then calculated. Scores are scaled between 0 and 5 with 5 being the best.

