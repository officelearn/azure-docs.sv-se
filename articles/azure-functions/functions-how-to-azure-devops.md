---
title: Kontinuerligt leverera funktionen kod med hjälp av Azure DevOps - Azure Functions
description: Lär dig hur du ställer in en Azure DevOps-pipeline som riktar in sig Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594466"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Kontinuerlig leverans med hjälp av Azure DevOps

Du kan distribuera funktionen till en Azure Functions-app automatiskt med hjälp av [Azure Pipelines](/azure/devops/pipelines/).

Har du två alternativ för att definiera din pipeline:

- **YAML-fil**: En YAML-filen beskriver pipelinen. Filen kan ha en build steg och en version-avsnitt. YAML-filen måste vara i samma lagringsplats som appen.
- **Mallen**: Mallar är färdiga åtgärder som att skapa eller distribuera din app.

## <a name="yaml-based-pipeline"></a>YAML-baserade pipeline

Om du vill skapa en YAML-baserade pipeline, först skapa din app och distribuera sedan appen.

### <a name="build-your-app"></a>Bygg din app

Hur du skapar din app i Azure Pipelines beror på vilket språk du din app. Varje språk har specifika steg som kan skapa en distribution av artefakt. En distribution av artefakt används för att distribuera funktionsappen i Azure.

#### <a name="net"></a>.NET

Du kan använda följande exempel för att skapa en YAML-fil för att skapa en .NET-app:

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

Du kan använda följande exempel för att skapa en YAML-fil för att skapa en JavaScript-app:

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

Du kan använda följande exempel för att skapa en YAML-fil för att skapa en Python-app. Python stöds endast för Linux Azure Functions.

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

Du kan använda följande exempel för att skapa en YAML-fil om du vill paketera en PowerShell-app. PowerShell stöds endast för Windows Azure Functions.

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

Du måste inkludera en av följande YAML-exempel i din YAML-fil, beroende på Operativsystemet som värd.

#### <a name="windows-function-app"></a>Windows-funktionsapp

Du kan använda följande fragment för att distribuera en funktionsapp för Windows:

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

#### <a name="linux-function-app"></a>Linux-funktionsapp

Du kan använda följande fragment för att distribuera en funktionsapp i Linux:

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

Mallar i Azure DevOps är fördefinierade grupper av uppgifter som att skapa eller distribuera en app.

### <a name="build-your-app"></a>Bygg din app

Hur du skapar din app i Azure Pipelines beror på vilket språk du din app. Varje språk har specifika steg som kan skapa en distribution av artefakt. En distribution av artefakt används för att uppdatera din funktionsapp i Azure.

Om du vill använda inbyggda build-mallar när du skapar en ny build-pipeline, Välj **använda redigeraren för klassiska** att skapa en pipeline med hjälp av designern mallar.

![Välj Azure Pipelines klassisk Redigare](media/functions-how-to-azure-devops/classic-editor.png)

När du har konfigurerat källan för din kod, Sök efter Azure Functions skapa mallar. Välj den mall som matchar ditt språk.

![Välj en mall för Azure Functions-version](media/functions-how-to-azure-devops/build-templates.png)

I vissa fall kan ha byggartefakter en viss mapp-struktur. Du kan behöva välja den **Prepend namn på rapportrotmappen att arkivera sökvägar** markerar du kryssrutan.

![Alternativet till åtkomstgruppen för rot-mappnamn](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-appar

Om JavaScript-app har ett beroende på Windows inbyggda moduler, måste du uppdatera pool agentversionen till **finns VS2017**.

![Uppdatera versionen av agenten-pool](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuera din app

När du skapar en ny version pipeline kan du söka efter versionsmall Azure Functions.

![Sök efter Azure Functions-viktig mall](media/functions-how-to-azure-devops/release-template.png)

Distribuera till ett distributionsfack stöds inte i mallen versionen.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Skapa en build-pipeline med hjälp av Azure CLI

Du kan skapa en build-pipeline i Azure med den `az functionapp devops-pipeline create` [kommandot](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Skapa pipelinen skapas för att skapa och släpp kodändringar som görs i ditt lager. Kommandot skapar en ny YAML-fil som definierar skapa och släpp-pipeline och sparar den till din lagringsplats. Krav för det här kommandot är beroende av platsen för din kod.

- Om din kod i GitHub:

    - Du måste ha **skriva** behörigheter för din prenumeration.

    - Du måste vara projektadministratör i Azure DevOps.

    - Du måste ha behörighet att skapa en GitHub personlig åtkomsttoken (PAT) som har tillräcklig behörighet. Mer information finns i [behörighetskrav för känna dig NÖJD med GitHub.](https://aka.ms/azure-devops-source-repos)

    - Du måste ha behörighet att byta till huvudgrenen i GitHub-databasen så att du kan genomföra den automatiskt genererade YAML-filen.

- Om din kod i Azure-databaser:

    - Du måste ha **skriva** behörigheter för din prenumeration.

    - Du måste vara projektadministratör i Azure DevOps.

## <a name="next-steps"></a>Nästa steg

- Granska den [översikt över Azure Functions](functions-overview.md).
- Granska den [översikt över Azure DevOps](/azure/devops/pipelines/).
