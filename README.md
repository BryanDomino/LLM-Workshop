# Domino Hands-On Retrieval-Augmented Generation Workshop

#### In this workshop you will tailor a RAG Application to your own use case -

* Leverage Domino's AI Hub templates
* Utilise Domino to self-service GPUs
* Read in documents from a live source
* Configure a Qdrant Vector Database
* Download and configure the Meta Llama2 model in Domino
* Deploy model to a containerized API endpoint
* Deploy a web-app frontend for end-user consumption

# Preparation
- Ensure you can log in to the workshop deployment at (https://ws.domino-eval.com). Your username and password will be emailed to you. The Deployment will *NOT* be accessible until the workshop day.
- Create a Personal access token (Token Classic) here (https://github.com/settings/tokens)
- Add the Token to your account settings inside the workshop deployment according to these instructions [Git Token](https://github.com/BryanDomino/LLM-Workshop/tree/main?tab=readme-ov-file#lab-11---git)

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

From here we can see a list of the data sources we have access to. Click on the **-Workshop** S3 connector. 

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

This template uses an out of the box document from Domino - but we want to use the documents from our S3 bucket. In order to do that we need to add a new cell to the Notebook, click the **+** in the top left:

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

Finally we want to load our document pages processing them with the embeddings and storing them in the Qdrant Vector Database. **Note:** This will take several minutes to populate the database.

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
We can now test our model! Run the following cell and ask a question of your documents. For the our use case this would be questions about the EU AI Regulation. For example: *"NEED A QUESTION HERE"*

<p align="center">
<img src = images/question_test.png width="800">
</p>

Run the cell and after a few moments the model will return an answer. Note you can rerun this cell several times with different questions if you wish.

<p align="center">
<img src = images/question_answer.png width="800">
</p>

# Section 3: Model API Setup And Deployment
Now that we have set up our model and our Qdrant vector database we want to wrap this as an API so we can embed this document search into our downstream applications.

To do this we have to make a small change to our template application code to point to the new Qdrant collection we created and then deploy the model as an API in Domino.

### Lab 3.1 - Model API Configuration
First we need to open the **model.py** file on the left hand panel. Review this code briefly. This code will initialise our model as we did in the notebook (lines 1-123) and has a python function called *generate* on line 134 that wraps our model to take a string input of the question and an optional int input of the number of tokens to return. We will point Domino at this function later to wrap it as an API:

<p align="center">
<img src = images/generate.png width="800">
</p>

In order to make this work with our documents we simply need to verify the name of the Qdrant collection that the code is looking for on line 69. Ensure it states **mlops**:

<p align="center">
<img src = images/model_api_collection.png width="800">
</p>

Before we go to deploy the model we want to sync all our code changes to the Git repository. to do this click on **files changes** on the left hand menu, enter a commit message and then click **Sync All Changes**. This will commit and push our code changes to Git.

<p align="center">
<img src = images/sync_files.png width="800">
</p>

We are now finished with our workspace so we can stop it and release the GPU back in to the pool of resources, saving ourself money:

<p align="center">
<img src = images/stop_workspace.png width="800">
</p>

Once the workspace has stopped click **Back to Domino** to return to the main Domino UI:

<p align="center">
<img src = images/back_to_domino.png width="800">
</p>

### Lab 3.2 - Model API Deployment
Next we will deploy our *generate* function as a Model API in Domino. Domino takes away the complexity of deploying APIs and with a few clicks we can have our model up and running as an API.

First click on the **Model APIs** button on the left menu and then **Create Model API**:

<p align="center">
<img src = images/create_model_api.png width="800">
</p>

We need to give our model a name, please include your initials e.g. "RAG_Model_ABC". Then click **Next**

<p align="center">
<img src = images/model_name.png width="800">
</p>

On the following page we need to point to our *generate* function that we want to deploy.

We need to input the file first: **model.py**

Then the name of our function: **generate**

We will need to deploy this model onto a GPU so select **GPU-Model-API** as the Deployment Target

Now click **Create Model API** to start the deployment process:

<p align="center">
<img src = images/model_deploy.png width="800">
</p>

Domino will now wrap your function in an API wrapper, package up the code and configuration into a Docker container and deploy that onto the platform. Note that this will take roughly 10 mins to deploy the model. We will move on and set up our App and then come back to check on the API later.

<p align="center">
<img src = images/model_preparing.png width="800">
</p>

# Section 4: Application Setup and Deployment

### Lab 4.1 - Reopen Workspace
To tailor the app to our use case we need to re-open our Workspace. But this time we won't need a GPU as we are doing simple development. 

Navigate back to your project by clicking **Projects** and then your project name:

<p align="center">
<img src = images/back_project.png width="800">
</p>

Navigate back to the Workspaces and then we can change the settings on the Workspace by clicking **Settings**, then **Edit Settings**:

<p align="center">
<img src = images/workspace_settings.png width="800">
</p>

Change the Hardware Tier to **Small** as we won't much compute to just edit code:

<p align="center">
<img src = images/workspace_restart.png width="800">
</p>

Wait for the Workspace to restart again and then open "API_streamlit_app.py".

We have three things in this app we want to change:
1. Add a header image for the EU AI ACT
2. Add a EU logo to the chat interface
3. Connect our app to the Model API we deployed

### Lab 4.2 - Add Header Image
To add a header image replace the image on line 41 with the following:

```
https://futurium.ec.europa.eu/sites/default/files/2020-06/eu-ai-alliance-3_0.png
```

<p align="center">
<img src = images/app_header_image.png width="800">
</p>

### Lab 4.3 - Add Logo Image
Similarly replace the image on line 90 with the following:

```
https://ec.europa.eu/regional_policy/images/information-sources/logo-download-center/eu_flag.jpg
```

<p align="center">
<img src = images/app_logo.png width="800">
</p>

### Lab 4.4 - Connect to Model API

To add in the code for connecting the app to the API endpoint we can get the python code snippet from the Domino UI. Go back to the main Domino UI in your other tab and navigate back to your APIs by clicking **Model APIs** and then click on the name of your Model API:

<p align="center">
<img src = images/model_running.png width="800">
</p>

At this point your model should be running. If it is not you can contact your instructor who can share with you another running API to use.

We can quickly test our model API using the following JSON in the **Request** window and click **Send**. Note the model may take a couple of seconds to send a response.

```json
{
  "data": {
    "input_text": "how do I change the battery in the key fob?",
    "max_new_tokens": 200
  }
}
```

<p align="center">
<img src = images/api_test.png width="800">
</p>

From here you can click on the **Python** to show a snippet of the code that you can move into your app. Copy lines 3-7:

<p align="center">
<img src = images/copy_response.png width="800">
</p>

Navigate back to your Workspace tab and paste the API connection string to overwrite lines 64-68:

<p align="center">
<img src = images/app_api.png width="800">
</p>

Domino has a open framework for deploying web applications, so we can deploy different types of web application frameworks. In this example we have written a Streamlit app. In order to run this app we have to tell Domino what app file to run and set the ports to be compatible with the platform. To do this we have defined a script to run this. Open and review ```app.sh```. This code could be changed if we wanted to deploy a different type of app, but in this case we do not need to make any changes.

<p align="center">
<img src = images/app_sh2.png width="800">
</p>

Save the app file and sync our files again. Click on **files changes** on the left hand menu, enter a commit message and then click **Sync All Changes**. This will commit and push our code changes to Git.

<p align="center">
<img src = images/sync_files.png width="800">
</p>

### Lab 4.5 - Deploy App 
Now we can deploy our app! Domino makes this easy to, we can simply point Domino to our application file and the platform will containerise and deploy the application for us to use. In the main Domino UI navigate click on **App** on the left hand menu and then click **Yes, I have app.sh in my code directory.**

<p align="center">
<img src = images/app_sh.png width="800">
</p>

Next give the app a name incorporating your initials again, e.g. **EU AI QA**:

<p align="center">
<img src = images/app_publish.png width="800">
</p>

This will take several minutes for the container to start. The *App Status* will change from **Pending** to **Running** at which point you can click **View App** to load the application:

<p align="center">
<img src = images/app_status.png width="800">
</p>

Now we have our working application!

<p align="center">
<img src = images/working_app.png width="800">
</p>

You can now ask questions about the AI EU act. Note the model will take a few seconds to respond:

<p align="center">
<img src = images/app_answer.png width="800">
</p>

If you want to you can also change the number of output characters (tokens) that the model returns, this can be helpful if the model is returning longer bodies of text from your documents. click on the arrow on the left of the app and update the Number of Output Tokens:

<p align="center">
<img src = images/change_tokens.png width="800">
</p>

We have now completed the workshop hands on.


# Summary
In this workshop you have taken a Domino AI Hub Project Template and tailored it to be a QA bot for the EU AI act. You have used Domino to:
* Leverage GPUs
* Connect to your S3 data
* Vectorise it and store it in Qdrant
* Download and run the Llama2 model from HuggingFace
* Deploy your model as an API
* Publish a web app to share your model with your end users

Domino makes it easy to deliver complex use cases by abstracting away unnecessary complexity!
