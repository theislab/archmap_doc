Tutorials
=========

Running your mapping locally
----------------------------

To run the mapping process locally, please follow these steps:

1. Clone the repository:

   .. code-block:: bash

      git clone -b mappingjob --single-branch https://github.com/theislab/archmap_data.git
      cd mapping

2. Create the environment:

   .. code-block:: bash

      bash create_env.sh

3. Download the HLCA reference atlas:

   Please follow this `link <https://drive.google.com/drive/folders/1-LUEad1iy5DNDZmKjQoPTz8ehMYDj91l?usp=drive_link>`_ 
   to access all the files needed to run this notebook. Download the folder **"hlca_tutorial"** and ensure that your 
   local folder where the files are stored is also named **"hlca_tutorial"**.

   If the link does not work, please copy and paste the URL into your browser. The `data_only_count.h5ad` file is only 
   needed if you would like to include the reference count data in your final mapped output for downstream analysis. 
   The gene conversion files are only needed if you are using your own mapping and not the provided query data.

4. Run the notebook:

   You can now run the tutorial `tutorial.ipynb`. Please make sure to read the instructions in `tutorial.ipynb` carefully. 

   If you come across an issue, please don't hesitate to contact us through the contact form on the 
   `website <https://www.archmap.bio/#/>`_.






