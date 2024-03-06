# Azure_Cognitive_Search
Azure Cognitive Search: Utilizando AI Search para indexação e consulta de Dados
Explore an Azure AI Search index (UI)
Let’s imagine you work for Fourth Coffee, a national coffee chain. You’re asked to help build a knowledge mining solution that makes it easy to search for insights about customer experiences. You decide to build an Azure AI Search index using data extracted from customer reviews.

In this lab you’ll:

Create Azure resources
Extract data from a data source
Enrich data with AI skills
Use Azure’s indexer in the Azure portal
Query your search index
Review results saved to a Knowledge Store
Azure resources needed
The solution you’ll create for Fourth Coffee requires the following resources in your Azure subscription:

An Azure AI Search resource, which will manage indexing and querying.
An Azure AI services resource, which provides AI services for skills that your search solution can use to enrich the data in the data source with AI-generated insights.

Note Your Azure AI Search and Azure AI services resources must be in the same location!

A Storage account with blob containers, which will store raw documents and other collections of tables, objects, or files.
Create an Azure AI Search resource
Sign into the Azure portal.

Click the + Create a resource button, search for Azure AI Search, and create a Azure AI Search resource with the following settings:

Subscription: Your Azure subscription.
Resource group: Select or create a resource group with a unique name.
Service name: A unique name.
Location: Choose any available region.
Pricing tier: Basic
Select Review + create, and after you see the response Validation Success, select Create.

After deployment completes, select Go to resource. On the Azure AI Search overview page, you can add indexes, import data, and search created indexes.

Create an Azure AI services resource
You’ll need to provision an Azure AI services resource that’s in the same location as your Azure AI Search resource. Your search solution will use this resource to enrich the data in the datastore with AI-generated insights.

Return to the home page of the Azure portal. Click the ＋Create a resource button and search for Azure AI services. 
Select create an Azure AI services plan. You will be taken to a page to create an Azure AI services resource. Configure it with the following settings:
Subscription: Your Azure subscription.
Resource group: The same resource group as your Azure AI Search resource.
Region: The same location as your Azure AI Search resource.
Name: A unique name.
Pricing tier: Standard S0
By checking this box I acknowledge that I have read and understood all the terms below: Selected
Select Review + create. After you see the response Validation Passed, select Create.

Wait for deployment to complete, then view the deployment details.
Create a storage account
Return to the home page of the Azure portal, and then select the + Create a resource button.

Search for storage account, and create a Storage account resource with the following settings:
Subscription: Your Azure subscription.
Resource group: The same resource group as your Azure AI Search and Azure AI services resources.
Storage account name: A unique name.
Location: Choose any available location.
Performance: Standard
Redundancy: Locally redundant storage (LRS)
Click Review and then click Create. Wait for deployment to complete, and then go to the deployed resource.

In the Azure Storage account you created, in the left-hand menu pane, select Configuration (under Settings).
Change the setting for Allow Blob anonymous access to Enabled and then select Save.
Upload Documents to Azure Storage
In the left-hand menu pane, select Containers.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/3f18aba4-728e-4f87-9b85-3d170aa0a806)

Select + Container. A pane on your right-hand side opens.

Enter the following settings, and click Create:
Name: coffee-reviews
Public access level: Container (anonymous read access for containers and blobs)
Advanced: no changes.
In a new browser tab, download the zipped coffee reviews from https://aka.ms/mslearn-coffee-reviews, and extract the files to the reviews folder.

In the Azure portal, select your coffee-reviews container. In the container, select Upload.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/01609985-4b6f-4737-864d-6d46f02dc4f9)

In the Upload blob pane, select Select a file.

In the Explorer window, select all the files in the reviews folder, select Open, and then select Upload.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/36ce6fd9-5c6d-41f7-be91-3efd3dec3668)

After the upload is complete, you can close the Upload blob pane. Your documents are now in your coffee-reviews storage container.
Index the documents
After you have the documents in storage, you can use Azure AI Search to extract insights from the documents. 
The Azure portal provides an Import data wizard. With this wizard, you can automatically create an index and indexer for supported data sources. 
You’ll use the wizard to create an index, and import your search documents from storage into the Azure AI Search index.

In the Azure portal, browse to your Azure AI Search resource. On the Overview page, select Import data.
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/78383d87-fd59-4fd7-b548-ba6d44515cd2)

On the Connect to your data page, in the Data Source list, select Azure Blob Storage. Complete the data store details with the following values:
Data Source: Azure Blob Storage
Data source name: coffee-customer-data
Data to extract: Content and metadata
Parsing mode: Default
Connection string: *Select Choose an existing connection. Select your storage account, select the coffee-reviews container, and then click Select.
Managed identity authentication: None
Container name: this setting is auto-populated after you choose an existing connection.
Blob folder: Leave this blank.
Description: Reviews for Fourth Coffee shops.
Select Next: Add cognitive skills (Optional).

In the Attach Cognitive Services section, select your Azure AI services resource.

In the Add enrichments section:
Change the Skillset name to coffee-skillset.
Select the checkbox Enable OCR and merge all text into merged_content field.
Note It’s important to select Enable OCR to see all of the enriched field options.

Ensure that the Source data field is set to merged_content.
Change the Enrichment granularity level to Pages (5000 character chunks).
Don’t select Enable incremental enrichment
Select the following enriched fields:

Cognitive Skill	Parameter	Field name
Extract location names	 	locations
Extract key phrases	 	keyphrases
Detect sentiment	 	sentiment
Generate tags from images	 	imageTags
Generate captions from images	 	imageCaption
Under Save enrichments to a knowledge store, select:
Image projections
Documents
Pages
Key phrases
Entities
Image details
Image references

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/5fe42b33-caf4-4a19-a6fb-48bc3fc18d49)

Select Azure blob projections: Document. A setting for Container name with the knowledge-store container auto-populated displays. Don’t change the container name.

Select Next: Customize target index. Change the Index name to coffee-index.

Ensure that the Key is set to metadata_storage_path. Leave Suggester name blank and Search mode autopopulated.

Review the index fields’ default settings. Select filterable for all the fields that are already selected by default.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/12f6f654-9467-41e5-9363-5a649559b046)

Select Next: Create an indexer.

Change the Indexer name to coffee-indexer.

Leave the Schedule set to Once.

Expand the Advanced options. Ensure that the Base-64 Encode Keys option is selected, as encoding keys can make the index more efficient.

Select Submit to create the data source, skillset, index, and indexer. The indexer is run automatically and runs the indexing pipeline, which:
Extracts the document metadata fields and content from the data source.
Runs the skillset of cognitive skills to generate more enriched fields.
Maps the extracted fields to the index.
Return to your Azure AI Search resource page. On the left pane, under Search Management, select Indexers. Select the newly created coffee-indexer. 
Wait a minute, and select &orarr; Refresh until the Status indicates success.

Select the indexer name to see more details.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/feda25bc-ad4c-4294-bc6d-56d72a3b4957)


Query the index
Use the Search explorer to write and test queries. 
Search explorer is a tool built into the Azure portal that gives you an easy way to validate the quality of your search index. 
You can use Search explorer to write queries and review results in JSON.

In your Search service’s Overview page, select Search explorer at the top of the screen.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/70bbc34c-2da3-431a-b3d6-42b30a99d01d)

Notice how the index selected is the coffee-index you created. Below the index selected, change the view to JSON view.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/9c6d1a67-2d0d-4f2e-a513-be81d73e3318)

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/d471bf35-25f7-423d-92f0-609d84d34020)

----------------------------------------------------------------------------------------------------------------------------------

Review the knowledge store
Let’s see the power of the knowledge store in action. When you ran the Import data wizard, you also created a knowledge store. Inside the knowledge store, you’ll find the enriched data extracted by AI skills persists in the form of projections and tables.

In the Azure portal, navigate back to your Azure storage account.

In the left-hand menu pane, select Containers. Select the knowledge-store container.
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/8e2d0a5a-8097-412e-b673-e04dc1de27ce)

Select any of the items, and then click the objectprojection.json file.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/0c93ba7f-7b0f-4847-ae94-e4b887f3aedf)



Select Edit to see the JSON produced for one of the documents from your Azure data store.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/ce045e37-c827-4930-a5e6-4bfbab40412f)

Select the storage blob breadcrumb at the top left of the screen to return to the Storage account Containers.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/dfeaec75-85dd-47f8-8d2d-a5e92a27d12f)

In the Containers, select the container coffee-skillset-image-projection. Select any of the items.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/912444cd-e192-4c6f-b1a3-3c04220a50b3)

Select any of the .jpg files. Select Edit to see the image stored from the document. Notice how all the images from the documents are stored in this manner.

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/a988489e-b338-426f-8df6-f1973ee941c4)

Select the storage blob breadcrumb at the top left of the screen to return to the Storage account Containers.

Select Storage browser on the left-hand panel, and select Tables. There’s a table for each entity in the index. Select the table coffeeSkillsetKeyPhrases.

Look at the key phrases the knowledge store was able to capture from the content in the reviews. Many of the fields are keys, so you can link the tables like a relational database. 

The last field shows the key phrases that were extracted by the skillset.

Learn more
This simple search index only some of the capabilities of the Azure AI Search service. To learn more about what you can do with this service, see the Azure AI Search service page.

Documentação oficial Microsoft :

link: https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/11-ai-search.html#azure-resources-needed


Descrição da Aula LAB900 - Azure Cognitive Search: Utilizando AI Search para indexação e consulta de Dados
Neste LAB, aplicaremos técnicas de organização de documentos e conduziremos pesquisas eficientes através da ingestão de dados,
seguindo três passos essenciais: ingestão de conhecimento de IA, criação do índice correspondente e exploração dessas funcionalidades. 
Ao final, ganharemos uma visão prática das potencialidades oferecidas por essas ferramentas na mineração de conhecimento.

Iniciando
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/bc6faff7-9033-4e5f-935e-55ef93172b7e)

Create AI Services	
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/5f3d241a-0681-450c-9510-18def1e91265)

Dar um nome esclusivo para este servidor	
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/8d4d8ef7-f57f-425a-9831-487bc1ab7600)

Princing Tier – selecionar nivel básico 
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/8a40d2ae-6c34-4e08-9db4-aed1d61bde2e)

Basics 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/2e65c939-ba21-46b1-81de-f92fcd22a563)

Clic em rewiew e aguarde a criação do servidor 
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/1a7c5787-4f38-40cd-999b-bdf95314ebe7)


Create Resourse  - > AI + Machine Learning 
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/80acf077-c76c-457d-b483-a3efec132e9a)

AI + Machine Learning 

AZURE AI SERVICES  - > Create 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/e8350d40-f315-428b-80cb-8040cc3b647b)

Preencher os dados 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/cf901a9e-6d52-4412-acba-7ae8271cf0fe)

Marcar checkbox

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/7b2305bb-a163-4575-a4f7-6fac68c20341)

Azure AI Services   

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/1e10496b-c904-4957-983b-c620cef03663)

AI Search  

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/26a4259f-2ec2-481a-b254-dde487517613)

Criar um storage account 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/c7bd4c3a-d0da-4076-a5a1-cf2473f620b2)


Create a storage account

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/f7ceef0d-c1da-4786-92a7-ba667377e623)

Assinatura :  
Resource group : Nome do Lab IA900
nome :  Para storage até 24 caracteres 
US
Standard 
LRS

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/a259a857-3fb9-4055-b980-dbb9a3f0eac4)
Clica em Rewiew

Storage 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/24eec9ff-42e7-4d22-99b7-d027b0f4cd23)

Settings Configuration 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/a722179c-7646-4901-97f2-dac8b7c2bb68)

Crie com nome Storage 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/f3f1f6ce-a6db-4687-bda5-45f5e5ab0ee0)

Crie com nome Storage 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/110ac752-8bb2-4883-b8c5-55ae127ac4d6)

Criado 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/58ed60ef-1514-4510-bbf5-a51623a3de60)

Acessando os documentos :	

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/8169c564-ecbc-406c-b367-e0444ce0245f)

click no documento:

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/7c9fd685-c1cf-4cc8-8ba3-69215ecc07e5)

Clicar em upload:

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/a09ba99c-5c12-4356-a6b8-aaea2e200de8)

Storage Acounts 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/740930e7-cef1-4db4-9ac7-4d36b764e42b)

Storage  acount 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/37293512-a496-4d93-a2e0-db0fffc38edf)

Storage  acount 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/4c961544-0a93-410b-916a-9cd7c8ccd461)

Mecanismo de busca Ai Search 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/9c6b4f9d-8b49-40e2-b164-9a6285e55fab)

Azure Ai Services | AI Search 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/2c83fb3c-1b98-4f9c-8eb7-bdebb45ae595)

Import data	

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/519165a1-feb3-46f7-a5bd-7beec4c21c16)

Import data	
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/8c417976-dc2b-4dc4-8d1d-c8164be5789f)

Servidor de Dados 

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/165398da-f5e0-4887-abe1-92db4abd0a08)

Pesquisas no servidor:
![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/5aab0fc5-8f22-45eb-81d1-2738fda9717a)

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/6354a44b-f313-4a04-a0f4-8a4feb3a8dae)


![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/a536154c-600f-4b22-9186-b2f1668f61ec)


![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/2c6a01a5-ab76-4d06-9f2c-865137bf9ff2)


![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/20e67050-c9ca-442c-b3c6-5e561420908a)

Search Explorer

![image](https://github.com/edudias1972/Azure_Cognitive_Search/assets/80340034/432bfe29-81ae-4af2-ae78-be5ca88b5583)
