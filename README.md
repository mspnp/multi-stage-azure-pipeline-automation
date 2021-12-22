# Using Logic App to Automate Azure DevOps Multistage Pipelines 

This repository contains an Azure Resource Group Project
that provisions an Azure Logic App. The Logic App interacts with Azure DevOps API to automate the provisioning of Multi Stage Pipelines

The source code contains the following files

- **LogicApp.json** - ARM template that provisions the logic app
- **LogicApp.parameters.json** - Parameter file that is passed as inputs to ARM template to provision the Logic App

>Replace the parameters with relevant values for azure devops organization, project and the PAT token.

>After Development phase, be sure to save ARM template secrets as securestring. Most importantly fetch those secret from azure keyvault

## Prerequisites

You need the following pre-requisites to deploy the project

- An Azure Subscription with some credits
- An Azure DevOps Account with project administrator role
- An active Azure DevOps Project
- [PAT token](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) from Azure DevOps
- Visual Studio 2022 or latest

## Deploy Azure Resources

You can choose to deploy this project from *Visual Studio* or using *Azure Cloud Shell*
The steps below show how to deploy ARM template from within Visual Studio

 - [Creating and deploying Azure resource groups through Visual Studio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/create-visual-studio-deployment-project)
  
Alternatively, you can also choose to run the deployment script, from within [Azure Cloud Shell](https://shell.azure.com), clone this repository to your local machine, switch to the `multistage-azure-pipeline-automation-logicapp` folder and upload the template & paramter json files to clouddrive

![Azure Cloud Shell](screenshots/azure-cloud-shell.png)

Now run the following commands :

```shell
az group create \
  --name azdevops-multistage-pipelines \
  --location "West Europe"
az deployment group create \
  --name prodenvironment \
  --resource-group azdevops-multistage-pipelines \
  --template-file LogicApp.json \
  --parameters LogicApp.parameters.json
```

****

>Read more about how to use parameter files to deploy your ARM template [here](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/template-tutorial-use-parameter-file?tabs=azure-cli#deploy-template)

## Adding LogicApp as Service Hook to Azure DevOps project

- Once the Logic App is provisioned, copy the trigger URL
- Go to Project Settings in Azure Devops and select service hooks
- On [Code Pushed](https://docs.microsoft.com/en-us/azure/devops/service-hooks/events?view=azure-devops#git.push) event, configure the webhook to call the HTTP Trigger URL of the Logic App you previously copied

Now for every codepush event detected across any repository within that azure devops project the logic app will invoke the azure devops api to configure the multistage pipelines based on the pipeline definition yaml file located in the root of the repository

This way you have a repeatable deployment pattern across all your repsositories within the azure devops project

![Service Hooks](screenshots/servicehooks-azuredevops.png)

>Now your setup will correspond to the representation of the following architecture

![Architecture](screenshots/automating_multistage_azure_pipelines.png)
