---
title: Skapa kontinuerligt funktionen Koduppdateringar med hjälp av Azure DevOps
description: Lär dig hur du ställer in en Azure DevOps-pipeline som riktar in sig på Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 9806a982982971b1b3ac9c28454e17813b2ad2a5
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479874"
---
# <a name="continuous-delivery-using-azure-devops"></a>Kontinuerlig leverans med Azure DevOps

Du kan distribuera din funktion automatiskt till en Azure Function-app med [Azure Pipelines](/azure/devops/pipelines/).
Du kan använda för att definiera din pipeline:

- YAML-fil: Den här filen beskriver pipelinen, så kan det ha en build steg, och en version avsnitt. YAML-filen ska vara i samma lagringsplats som appen.

- Mallar: Mallar är det dags gjort uppgifter som att skapa eller distribuera din app.

## <a name="yaml-based-pipeline"></a>YAML-baserade pipeline

### <a name="build-your-app"></a>Bygg din app

Skapa en app i Azure Pipelines beror på vilket språk du din app.
Varje språk har specifika steg för att skapa en distribution av artefakt som kan användas för att distribuera funktionsappen i Azure.

#### <a name="net"></a>.NET

Du kan använda följande exempel för att skapa en YAML-fil för att bygga din .NET-app.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Du kan använda följande exempel för att skapa en YAML-fil för att skapa din JavaScript-app:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

Du kan använda följande exempel för att skapa en YAML-fil för att skapa din app med Python, Python stöds endast för Linux Azure Functions:

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

Du kan använda följande exempel för att skapa en YAML-fil om du vill paketera din PowerShell-app, PowerShell stöds endast för Windows Azure Functions:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Distribuera din app

Beroende på Operativsystemet som värd måste följande YAML-exempel för YAML-fil.

#### <a name="windows-function-app"></a>Windows-funktionen App

Följande kodavsnitt kan användas för att distribuera till en Windows-funktionsapp

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux-funktionen App

Följande kodavsnitt kan användas för att distribuera till en funktionsapp i Linux

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Mallbaserade pipeline

Mallar i Azure DevOps, är fördefinierad grupp med aktiviteter som att skapa eller distribuera en app.

### <a name="build-your-app"></a>Bygg din app

Skapa en app i Azure Pipelines beror på vilket språk du din app. Varje språk har specifika steg för att skapa en distribution av artefakt som kan användas för att uppdatera din funktionsapp i Azure.
Om du vill använda inbyggda build-mallar när du skapar en ny build-pipeline, Välj **använda redigeraren för klassiska** att skapa en pipeline med hjälp av designern mallar

![Azure Pipelines klassisk Redigare](media/functions-how-to-azure-devops/classic-editor.png)

Sök efter Azure Functions build-mallar när du har konfigurerat källan för din kod, och välj den mall som matchar ditt språk.

![Skapa mallar för Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

I vissa fall kan byggartefakterna har en specifik mappstruktur och du kan behöva kontrollera den **Prepend namn på rapportrotmappen att arkivera sökvägar** alternativet.

![Lägg till åtkomstgruppen rotmappen](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-appar

Om JavaScript-app är beroende av Windows inbyggda moduler, kommer du behöva uppdatera:

- Versionen för Agentpoolen att **finns VS2017**

  ![Ändra Skapandeagent OS](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuera din app

När du skapar en ny version pipeline kan du söka efter versionsmall för Azure Functions.

![](media/functions-how-to-azure-devops/release-template.png)

Distribuera till ett distributionsfack stöds inte i mallen versionen.

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Skapa en Azure-Pipeline med hjälp av Azure CLI

Med hjälp av den `az functionapp devops-pipeline create` [kommandot](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), skapas en Azure-pipeline för att skapa och släpp kodändringar i ditt lager. Kommandot ska generera en ny YAML-fil som definierar skapa och släpp-pipeline och överför den till din lagringsplats. Distribuera till ett distributionsfack stöds inte av Azure CLI-kommando.
Förutsättningar för det här kommandot är beroende av platsen för din kod:

- Om din kod i GitHub:

    - Du måste ha **skriva** behörighet till din prenumeration.

    - Du är projektadministratör i Azure DevOps.

    - Du har behörighet att skapa en GitHub personlig åtkomst-Token med tillräcklig behörighet. [GitHub PAT behörighet som krävs.](https://aka.ms/azure-devops-source-repos)

    - Du har behörighet att byta till huvudgrenen i ditt GitHub-lagringsplats för att genomföra den automatiskt genererade YAML-filen.

- Om din kod i Azure-databaser:

    - Du måste ha **skriva** behörighet till din prenumeration.

    - Du är projektadministratör i Azure DevOps.

## <a name="next-steps"></a>Nästa steg

+ [Översikt över Azure Functions](functions-overview.md)
+ [Översikt över Azure DevOps](/azure/devops/pipelines/)
