---
title: Uppdatera funktionsappkod kontinuerligt med Azure DevOps
description: Lär dig hur du konfigurerar en Azure DevOps-pipeline som är inriktad på Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255760"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Kontinuerlig leverans med hjälp av Azure DevOps

Du kan automatiskt distribuera din funktion till en Azure Functions-app med hjälp av [Azure Pipelines](/azure/devops/pipelines/).

Du har två alternativ för att definiera pipelinen:

- **YAML-fil**: En YAML-fil beskriver pipelinen. Filen kan ha ett avsnittet byggsteg och ett utgivningsavsnitt. YAML-filen måste finnas i samma reppo som appen.
- **Mall**: Mallar är färdiga uppgifter som skapar eller distribuerar din app.

## <a name="yaml-based-pipeline"></a>YAML-baserad pipeline

Om du vill skapa en YAML-baserad pipeline skapar du först appen och distribuerar sedan appen.

### <a name="build-your-app"></a>Skapa appen

Hur du skapar din app i Azure Pipelines beror på appens programmeringsspråk. Varje språk har specifika byggsteg som skapar en distributionsartefakt. En distributionsartefakt används för att distribuera din funktionsapp i Azure.

# <a name="c"></a>[C\#](#tab/csharp)

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
    modifyOutputPath: false
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
    artifactName: 'drop'
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

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
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Du kan använda något av följande exempel för att skapa en YAML-fil för att skapa en app för en viss Python-version. Python stöds endast för funktionsappar som körs på Linux.

**Version 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Version 3.6**

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
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Du kan använda följande exempel för att skapa en YAML-fil för att paketera en PowerShell-app. PowerShell stöds endast för Windows Azure-funktioner.

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
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Distribuera din app

Du måste inkludera något av följande YAML-exempel i YAML-filen, beroende på värdoperativsystemet.

#### <a name="windows-function-app"></a>Windows-funktionsapp

Du kan använda följande kodavsnitt för att distribuera en Windows-funktionsapp:

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

Du kan använda följande kodavsnitt för att distribuera en Linux-funktionsapp:

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

## <a name="template-based-pipeline"></a>Mallbaserad pipeline

Mallar i Azure DevOps är fördefinierade grupper av uppgifter som skapar eller distribuerar en app.

### <a name="build-your-app"></a>Skapa appen

Hur du skapar din app i Azure Pipelines beror på appens programmeringsspråk. Varje språk har specifika byggsteg som skapar en distributionsartefakt. En distributionsartefakt används för att uppdatera din funktionsapp i Azure.

Om du vill använda inbyggda byggmallar väljer du **Använd den klassiska redigeraren** för att skapa en pipeline med hjälp av designermallar när du skapar en ny byggpipeline.

![Välj den klassiska redigeraren för Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

När du har konfigurerat kodens källa söker du efter Azure Functions build-mallar. Välj den mall som matchar appspråket.

![Välj en Azure Functions-byggmall](media/functions-how-to-azure-devops/build-templates.png)

I vissa fall har byggartefakter en specifik mappstruktur. Du kan behöva markera kryssrutan **Prepend-rotmappnamn för att arkivera sökvägar.**

![Alternativet att förbereda rotmappens namn](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-appar

Om JavaScript-appen är beroende av Windows-interna moduler måste du uppdatera agentpoolversionen till **Hosted VS2017**.

![Uppdatera agentpoolversionen](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuera din app

När du skapar en ny versionspipeline söker du efter azure functions-versionsmallen.

![Sök efter releasemallen för Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Distribution till en distributionsplats stöds inte i versionsmallen.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Skapa en build pipeline med hjälp av Azure CLI

Om du vill skapa en `az functionapp devops-pipeline create` byggpipeline i Azure använder du [kommandot](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Build-pipelinen skapas för att skapa och släppa alla kodändringar som görs i din repo. Kommandot genererar en ny YAML-fil som definierar versionspipelinen och sedan genomför den till din repo. Förutsättningarna för det här kommandot beror på var koden finns.

- Om koden finns i GitHub:

    - Du måste **write** ha skrivbehörighet för din prenumeration.

    - Du måste vara projektadministratör i Azure DevOps.

    - Du måste ha behörighet att skapa en GitHub-token för personlig åtkomst (PAT) som har tillräcklig behörighet. Mer information finns i [behörighetskraven för GitHub PAT.](https://aka.ms/azure-devops-source-repos)

    - Du måste ha behörighet att binda till huvudgrenen i GitHub-databasen så att du kan arkivera den automatiskt skapade YAML-filen.

- Om koden finns i Azure Repos:

    - Du måste **write** ha skrivbehörighet för din prenumeration.

    - Du måste vara projektadministratör i Azure DevOps.

## <a name="next-steps"></a>Nästa steg

- Granska [översikten över Azure-funktioner](functions-overview.md).
- Granska [översikten över Azure DevOps](/azure/devops/pipelines/).
