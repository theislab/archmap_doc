# Beta Features

Upon request, ArchMap users can upload their own atlases. Additionally, a benchmarking pipeline should be run post-upload to compare the integration of the uploaded atlases with other integration methods.  
Once successful benchmarking has taken place, users can download their benchmarking results and send them to the ArchMap team via the contact form on the website to complete the revision stage.  
After the atlas revision stage is completed, the atlas builder has the option to make their atlas public for other users to map to (by default, the atlas is only accessible to the uploader). These steps are outlined in more detail below.

## For Atlas Uploaders

### Steps to upload atlases to ArchMap:

1. Go to [https://archmap.bio](https://archmap.bio)  

   ![Homepage](../_static/beta_feature/homepage.png)  

2. Sign up for an account by clicking on **Sign Up**.  

   ![Signup](../_static/beta_feature/signup_button.png)  

3. Click on **Request for Beta Feature**.  

   ![Request Beta Access](../_static/beta_feature/request_beta_access.png)  

4. Once requested, please wait for our review. After approval, you will be able to access the **Upload Atlas** tab. Now, log in at [https://archmap.bio](https://archmap.bio) and click on the **Log In** button at the top right corner.  

   ![Login](../_static/beta_feature/signup_button.png)  

5. Click on **Upload Atlas** in the sidebar. Note that you will only see this option if we have accepted your request. Here, you can see all the atlases you have uploaded. If you have not uploaded any, the page will display "No atlases uploaded".  

   ![Upload Atlas](../_static/beta_feature/upload_atlas.png)  

6. To upload an atlas, click **Upload Atlas**. Fill in the required details. Ensure that the `.h5ad` file you upload has the count data stored in the `.X` attribute. This is crucial for benchmarking during the revision stage.  

   ![Upload Atlas Form](../_static/beta_feature/upload_atlas_form.png)  

7. You can upload the atlas either by selecting a file or by providing a link to the atlas.  

   ![Upload Options](../_static/beta_feature/upload_atlas_form_option.png)  

8. To add a compatible model, select it and click **Upload**.  

   ![Upload Model](../_static/beta_feature/upload_atlas_form_model.png)  

9. Along with the atlas and model files, the following information is required:  

   - **Atlas Name**: The display name of your atlas.  
   - **Batch Covariate Key**: The `.obs` column name in your AnnData object containing batch covariate labels used during integration.  
   - **Cell Type Key**: The cell type label used if you integrated with **semi-supervised models (scANVI or scPoli)**. If **scVI** was used, provide one of the cell type labels in your `.obs` dataframe.  
   - **Cell Type for Label Transfer**: The cell type key to be used for label transfer when mapping query data. This must match one of the labels in your `.obs` dataframe.  
   - **Number of Cells**: The total number of cells in your atlas.  
   - **Species**: The species modeled by your atlas.  
   - **Set Atlas to Private**: If `true` (default), your atlas remains private post-revision. If `false`, all ArchMap users can access and map to it.  

10. Once the atlas and model files are uploaded, you can see your atlas in the list of uploaded atlases.  

   ![Uploaded Atlas](../_static/beta_feature/uploaded_atlas.png)  

---

## Atlas Revision Stage  

After uploading your atlas, its status will be **"In Revision"**. Follow these steps to complete the revision stage:

### How do I benchmark my atlas?

1. Navigate to the **Search** page in the sidebar. Under the **ATLASES** tab, search for your atlas and hover over its card. Click **ATLAS BENCHMARK**.  

   ![Benchmark Search](../_static/beta_feature/benchmark_search.png)  

2. An info box will appear. Click **START BENCHMARK**. If successful, you will receive the message **"Benchmarking job triggered successfully!"**. If you see **"Failed to trigger the benchmarking job. Please try again."**, attempt the benchmark again. If the issue persists, contact the ArchMap team via our contact form on the website.  

   ![Benchmark Start](../_static/beta_feature/benchmark_start.png)  

3. Benchmarking duration depends on the atlas size but should not exceed **12 hours**. Once completed, return to the benchmark page, where you will see a **DOWNLOAD BENCHMARK RESULTS** button. This will provide a report including classifier performance for label transfer.  

   ![Download Benchmark Results](../_static/beta_feature/benchmark_download_results.png)  

---

## The Benchmarking Procedure Explained  

### Why we benchmark  

Benchmarking ensures quality control for atlases uploaded to ArchMap. The process verifies that an atlas' integration performance is comparable to other integration methods.  
We do **not** re-integrate the atlas using the user's method but train separate models and compare embeddings using existing integration benchmarking metrics (scib-metrics).  
For more details on the benchmarking process, refer to the scripts:  
 
- [Benchmarking pipeline](https://github.com/theislab/archmap_data/blob/benchmark3/mapping/scarches_api/test.py) 
- [Benchmarking pipeline function definitions](https://github.com/theislab/archmap_data/blob/benchmark3/mapping/scarches_api/benchmark_atlas_upload.py)  

### Benchmarking Pipeline Steps  

1. **Benchmarking Atlas Integration**  
   - Two additional integration models are trained based on the original model.  
   - For example, if **scVI** was used, we train **scANVI and scPoli** for comparison. In this case, since **scVI** does not take into account cell type label info, we train two separate models of **scPoli** - one using and one excluding cell type info - for better comparison.   
   - To accommodate large atlases and adhere to resource limits on Google Cloud, the benchmarking process subsets the dataset to **200,000 cells** while preserving all cell types and their proportions. Due to subsampling, batch effects may be unintentionally removed which may result in higher scores of batch correction metrics for the newly trained models compared to the user-integrated model. This is taken into account when revising the uploaded atlas

2. **Atlas Minification**  
   - The reference embedding is stored in the AnnData object.  
   - Count data is removed from the AnnData object and stored separately.  
   - This step is used to speed up the query-to-reference mapping pipeline.  

3. **Classifier Training for Label Transfer**  
   - **XGBoost** and **KNN** classifiers are trained on 20% held-out reference data.  
   - The classifiers are stored in Google Cloud Storage along with the atlas.  

---

## Benchmarking Output  

Once benchmarking is complete, users can download the results from ArchMap. The output includes:  

1. **scib-metrics Report**  
   - Compares newly trained integration models with the user-provided model.  
   - Includes min-max scaled benchmarking metric results.  

2. **Classifier Validation Results**  
   - Performance evaluation of **XGBoost and KNN** classifiers.  
   - Trained on 80% of the reference dataset, validated on 20%.  

---

If you have any questions or comments regarding the atlas upload process, feel free to contact the ArchMap team!
