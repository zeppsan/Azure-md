# Logic app (Standard) local development & debugging 
This documents holds a short guide of how to develop and run Logic apps (Standard) locally using Visual Studio Code.

> **Important**: Depending on the versions of tools you have installed, you may encounter errors while following these steps. I've put a section in the end named **Errors section**, where possible solutions to various errors are located. 

## Tools
Tools that needs to be installed on your machine. 
- Azure-functions-core-tools@4 [Installation Guide](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=v4%2Cmacos%2Ccsharp%2Cportal%2Cbash)
- Visual Studio Code [Website](https://code.visualstudio.com/)
- .NET SDK ([Windows](https://dotnet.microsoft.com/en-us/download/visual-studio-sdks) | [MacOS](https://dotnet.microsoft.com/en-us/download))
  

## Visual Studio Code Extensions 
To ensure a seamless development experience when working with Logic Apps (Standard), it is necessary to install the following extensions in Visual Studio Code.
- [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)
- [Azure Logic Apps (Standard)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurelogicapps)
- [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)
- [Azurite](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)
- [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

## Setting up the Environment 
1. Ensure that the tools above are installed. 
2. Authenticating towards the subscription. Start by navigating to the Azure tab in the sidebar of vscode and find the Logic app (Standard) section. Press **Sign in to Azure...** A new page should appear in your browser. Sign in to the account that you want to authenticate to. 

![image](https://i.gyazo.com/cc1dfde58cc95f33827969e577035eb2.png)

---

## Create a new workflow 
1. Start by pressing the **create workflow** and follow the prompt. It will ask you to *Create a new project* or *Open existing project*. Once you've decided on that, fill in information such as *Stateless* or *Statefull* workflow etc.  
![image](https://i.gyazo.com/b346175af0cff160c236d6de52383252.png)

2. The new workflow should now be in your project / the new project that you created.

---

## Editing the workflow 
1. In the project that you now have in front of you, find a folder named after the workflow that you created. Open the folder, right-click on Workflow.json, and press Open Designer.

![image](https://i.gyazo.com/693e1564ac94505ae57064616c68ffa1.png)

> **Important**: When you have made changes to the workflow, make sure to save your work before switching tab or closing VS Code. Otherwise, all your work will be wiped.


*Known errors are listed in errors section*

---

## Deploying to Azure 
1. To deploy your workflow to Azure, press `alt+shift+p` to open the vscode promt. Search for "Logic app deploy" and you should find a result "Azure Logic Apps: Deploy yo Logic App...". Choose that one and just follow the prompt.

![image](https://i.gyazo.com/dd305d6d5e0b338f7ee088dc7d1ee34c.png)

Although this is not the recommended way to upload workflows (pipelines are ideal), it's good for just playing around and making quick changes. 

--- 

## Run workflow locally
1. Let's start with some neccesary configuration. Open the `local.settings.json` in the root of your project and make sure that the following config is present. The dummy values should be replaced with the values that are currently in your config file. 
```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "WORKFLOWS_TENANT_ID": "dummy",
    "WORKFLOWS_SUBSCRIPTION_ID": "dummy",
    "WORKFLOWS_RESOURCE_GROUP_NAME": "dummy",
    "WORKFLOWS_LOCATION_NAME": "dummy",
    "WORKFLOWS_MANAGEMENT_BASE_URI": "https://management.azure.com/",
    "AzureWebJobsSecretStorageType": "Files",
    "Workflows.helloWorld.OperationOptions": "WithStatelessRunHistory"
  }
}
```
2. To run your workflow locally, first start up Azurite by using the `Azurite: Start` in the vscode promt (Or start it in any other way. Just make sure its running).  
3. Then go to the `RUN AND DEBUG` section of vscode and press "start debugging"(F5). Hopefully your logic app will now start running without problems. 
4. Now navigate back to your project folder and right-click on the workflow.json file that you would like to inspect and press **Overview**, this will open up the local run-history. It's then possible to inspect the runs. 

![image](https://i.gyazo.com/b9d026c735c835343ed935c1339cab11.png)

> If you created a workflow with HTTP trigger, the **url** will be displayed in the terminal of vscode. 

![image](https://i.gyazo.com/c02a8ea20fb01106e5d26bda6817459d.png)


# Errors section 
In this section, possible errors and solutions are listed. 

## Editing the workflow 
**Workflow design time could not be started**  
Open the folder named **workflow-designtime** and edit the **hosts.json** file. In the file, we need to replace the extensionsBundle as follows: 

Previous:
```
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle.Workflows",
    "version": "[1.*, 2.0.0)"
  },
```  
Changed version: 
```
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle.Workflows",
    "version": "[1.*, 1.2.18)"
  },
```

This bug is beeing taken care of as we speak, but for now this is the solution. 

--- 


## Run workflow locally
**Failed to execute 'open' on 'XMLHttpRequest': Invalid URL**  
This error is caused by vscode not beeing able to fetch the run history. Make sure that the `local.settings.json` file in the root folder is pointing it's AzureWebJobsStorage to Azurite: 
```
"AzureWebJobsStorage": "UseDevelopmentStorage=true",
```
