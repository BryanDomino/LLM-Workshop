# Domino Hands-On Retrieval-Augmented Generation Workshop

#### In this workshop you will tailor a RAG Application to your own use case -

* Leverage Domino's AI Hub templates
* Utilise Domino to self-service GPUs
* Read in documents from a live source
* Configure a Qdrant Vector Database
* Download and configure the Meta Llama2 model in Domino
* Deploy model to a containerized API endpoint
* Deploy a web-app frontend for end-user consumption


# Section 1: Project Set Up
First we need to start a new project based on a Domino AI Hub template. This involves configuring our Git credentals in Domino and adding our external data source, in this case S3. Then we will start a development environment using GPU infrastructure.

### Lab 1.1 - Git
We need to add our Git credentials to our Domino account. To do this click on your name in the bottom left and then on **Account Settings**:

<p align="center">
<img src = images/account_settings.png width="800">
</p>

Navigate to the **Git Credentials** section:
<p align="center">
<img src = images/git_credentials.png width="800">
</p>

Click on **Add Credentials**:
<p align="center">
<img src = images/add_credentials.png width="800">
</p>

Set a nickname of your choice, choose *Github* as your provider, select *Personal Account Token* as the access type and then enter your PAT token:
<p align="center">
<img src = images/git_token.png width="800">
</p>

### Lab 1.2 - Project Template
Now that we have our Git credentials configured in the platform we can use Domino's AI Hub templates to quick start our AI projects! We want to find a template that will allow us to create a document search engine using Retrieval-Augmented Generation. In this case we want to keep all of our data in our control and so don't want to leverage third party services and so we want to use an open source model that we can run directly in Domino.

Let's navigate to the Templates to see what we can utilise. Click on **Projects** on the left hand side then **Templates**:

<p align="center">
<img src = images/projects_templates.png width="800">
</p>

Here we can see a list of templates that have been prepared for us. There are templates for common activities such as anomaly detection in images or time series data, chatbots and forecasting models.

We want to do interrogate our documents using an open source model so select the **Open Source Enterprise Q+A over your docs** template:

<p align="center">
<img src = images/llm_template.png width="800">
</p>

Here we are given some background on this template. It gives us a description of the purpose of the template, indicates which model(s) will be used, licenses as well as the hardware and compute environment (container with libraries) that is required to run the code.

Click **Create Project** to create our own copy of this template:

<p align="center">
<img src = images/create_project.png width="800">
</p>

Firstly, give your project a name. For the sake of simplicity set this to *workshop_RAG_* then your initials. E.g. *workshop_RAG_ABC*. Next ensure that the project visibility is **Private**:

<p align="center">
<img src = images/new_project1.png width="800">
</p>

We then need to configure which Git Repository our code will be saved to. To do this we need to give the repository a name, then select our Git credentials from Section 1 and choose the individual/organisation that will own the new repository. You must ensure you choose a Git individual/organisation that you will have full write access to.

Finally, click **Create** to create our project:

<p align="center">
<img src = images/new_project2.png width="800">
</p>

We will now be taken to the new project where we have a new menu on the left that shows us the data, code, workspaces and other assets that belong in this project. Next we will want to connect to our S3 bucket to get our documents.
<p align="center">
<img src = images/project_overview.png width="800">
</p>

### Lab 1.3 - Data Source Connection
In order to access our documents we will want to add a connection to our S3 bucket to our project. Luckily our IT Admin team has already set this up for us, ensuring good data security practises. In this case we can simply add the data source to our project.

To do this click on **Data** on the left hand menu and then **Add Data Source**:

<p align="center">
<img src = images/data_source1.png width="800">
</p>

From here we can see a list of the data sources we have access to. Click on the **Nissan-Workshop** S3 connector. 

To simplify things in this workshop the data sources have been added with service accounts, but in many organisations you would have to put in your individiual credentials to connect to the data source.

Click **Add to Project** to add this data source connector to our project.
<p align="center">
<img src = images/AddS3.png width="800">
</p>

### Lab 1.4 - Interactive Workspace
Now that we have our template code and our data connection we want to start and interactive development environment, or Workspace. 

To do this click on **Workspaces** on the left hand side. We are working with Python notebooks today so will want to use JupyterLab - click on the **Jupyterlab** button:

<p align="center">
<img src = images/workspace1.png width="800">
</p>

This opens up a configuration window where we can choose the resources that we will need for this development. Note that the project environment is *Project Hub Gpu Environment*, which was configured in the template we chose. Environments in Domino are docker containers under the covers that have different libraries installed. In this case all the libraries we need to run our code are preinstalled in the template, so we don't need to install anything or change any settings for our code to work.

What we will need though is a GPU to run our model on. In the *Hardware Tier* section choose the **GPU** tier. Then click **Launch** to start our workspace:

<p align="center">
<img src = images/workspace2.png width="800">
</p>

This may take a few minutes to start, but note that we haven't had to interact with any of the complexities of the cloud - Domino is provisioning these resources and moving our code and data accross at the click of a button: 

<p align="center">
<img src = images/workspace3.png width="800">
</p>

When your JupyterLab environment is up and running, as shown below, we can move on to running our code.

<p align="center">
<img src = images/workspace4.png width="800">
</p>

# Section 2: Guided Notebook

### Lab 2.1 - Notebook + Data Source
We can now customise the notebook to our use case. We will need to:
* Open the Notebook
* Fetch and Process the Documents
* Initialise the Vector Store
* Fetch and Initialise the Llama2 Model
* Create the QA chain and test it!

Double click on **Llama_Qdrant_RAG.ipynb** notebook in the left panel:

<p align="center">
<img src = images/notebook1.png width="800">
</p>

Run the first cell to load all the library dependencies by pressing *Shift + Enter*. 

This template uses an out of the box document from Domino - but want to use the Nissan documents from our S3 bucket. We need to add a new cell to the Notebook, click the **+** in the top left:

<p align="center">
<img src = images/notebook2.png width="800">
</p>

To connect to the S3 bucket we can get the connection details from the Domino UI. Click on **Data** in the menu on the left. Then the **Copy** symbol next to the *Nexus-Workshop* and then **Python**:

<p align="center">
<img src = images/notebook_datasource1.png width="800">
</p>

This will copy the connection details to our clipboard. Paste this into the new notebook cell and run it:

<p align="center">
<img src = images/notebook_datasource2.png width="800">
</p>

We now want to download the files we need from this S3 bucket. To simplify things create a new cell in the notebook, and copy the following snippet of code, paste it into the new cell:

```python
dataset_path = "copy your dataset in here"

for my_bucket_object in objects:
    object_store.download_file(my_bucket_object.key, dataset_path+"/"+my_bucket_object.key)
```

Before we run this we need to get the Domino Dataset location that we will save these documents into. To find this click on **Data** again on the left, then **Datasets** then the **Copy** button next to the *Dataset Path*:

<p align="center">
<img src = images/notebook_dataset1.png width="800">
</p>

Paste this into the cell you've just added and the cell below as depicted below. This will download the documents into that dataset location and then read them and split them into chunks ready to process.

<p align="center">
<img src = images/notebook_dataset2.png width="800">
</p>

Run the next three cells to split up the document into pages:

<p align="center">
<img src = images/notebook_docs.png width="800">
</p>

In order to use these document pages we also need to download the appropriate embeddings to work with the Llama2 model. Run the cell:

<p align="center">
<img src = images/notebook_embeddings.png width="800">
</p>

Finally we want to load our document pages processing them with the embeddings and storing them in the Qdrant Vector Database. We want to change the name of the collection. Change **mlops** to **nissan**, then run the cell. **Note:** This will take several minutes to populate the database.

<p align="center">
<img src = images/notebook_qdrant_nissan.png width="800">
</p>

### Lab 2.2 - Configure Model
Now we have the documents stored in the Qdrant Vector Database we want to configure our Llama2 model. Firstly we can specify the prompt that we will use. Note that prompt engineering is typically a work item in it's own right and can take several iterations. For the purpose of this workshop we will simply use the one provided here. In a real scenario you could come back and iterate on this later.

Run the prompt cell:

<p align="center">
<img src = images/setup_prompt.png width="800">
</p>

Ahead of downloading the model we want to configure the bitsandbytes configuration to ensure we have the right quantisation. Run the following cell:
<p align="center">
<img src = images/bitsandbytes.png width="800">
</p>

Now we can download the Llama2 model from hugging face to test it. Run the following cell:
<p align="center">
<img src = images/download_model.png width="800">
</p>

Lastly we can configure our pipeline with the model, tokenizer and Qdrant. Run the following cell:
<p align="center">
<img src = images/pipeline.png width="800">
</p>

### Lab 2.2 - Test Model
We can now test our model! Run the following cell and ask a question of your documents. For the Nissan use case this would be questions about the Nissan Aryia. For example: *"how do I change the battery in the key fob?"*

<p align="center">
<img src = images/question_test.png width="800">
</p>

Run the cell and after a few moments the model will return an answer. Note you can rerun this cell several times with different questions if you wish.

<p align="center">
<img src = images/question_answer.png width="800">
</p>

# Section 3: Model API Set Up And Deployment
Now that we have set up our model and our Qdrant vector database we want to wrap this as an API so we can embed this document search into our downstream applications.

To do this we have to make a small change to our template application code to point to the new Qdrant collection we created and then deploy the model as an API in Domino.

### Lab 3.1 - Model API Configuration
First we need to open the **model.py** file on the left hand panel. This code will initialise our model as we did in the notebook (lines 1-123) and has a python function called *generate* on line 134 that wraps our model to take a string input of the question and an optional int input of the number of tokens to return.

In order to make this work with our Nissan documents we simply need to change the name of the Qdrant collection that the code is looking for on line 69. Here change **mlops** to **nissan**:

<p align="center">
<img src = images/model_api_collection.png width="800">
</p>

This is the only change we needed to make. Before we go to deploy the model we no want to sync all our code changes to the Git repository. to do this click on **files changes** on the left hand menu, enter a commit message and then click **Sync All Changes**. This will commit and push our code changes to Git.

<p align="center">
<img src = images/sync_files.png width="800">
</p>

We are now finished with our workspace so we can stop it and release the GPU back in to the pool of resources, saving ourself money:

<p align="center">
<img src = images/stop_workspace.png width="800">
</p>

### Lab 3.2 - Model API Deployment
text

# Section 4: Application Setup and Deployment

### Lab 4.1 - Tailor App 
text

<p align="center">
<img src = images/app_header_image.png width="800">
</p>
'''
https://poctemppublic.s3.us-west-2.amazonaws.com/n_car.png
'''

<p align="center">
<img src = images/app_api_endpoint.png width="800">
</p>
'''python
response = requests.post("https://ws.domino-eval.com:443/models/65bcac2e5f71323e384cec13/latest/model",
                auth=(
                    "JbvObvS7MDxXZrBn0tUGd603CTpVTzyH3bmbMV4yEv82EZfiJYOsCVbieciwsE64",
                    "JbvObvS7MDxXZrBn0tUGd603CTpVTzyH3bmbMV4yEv82EZfiJYOsCVbieciwsE64"
                ),
'''

<p align="center">
<img src = images/app_logo.png width="800">
</p>

'''
https://poctemppublic.s3.us-west-2.amazonaws.com/n_logo.png
'''

### Lab 4.2 - Deploy App 
text

# Summary