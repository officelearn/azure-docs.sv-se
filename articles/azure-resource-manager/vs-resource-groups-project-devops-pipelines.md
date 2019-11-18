---
title: CI/CD med Azure-pipeline och-mallar
description: Beskriver hur du konfigurerar en kontinuerlig integrering i Azure-pipeline med hjälp av distributions projekt i Azure Resource Group i Visual Studio för att distribuera Resource Manager-mallar.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 51122e314ebd0a97647fc4026b1f49619950c351
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143766"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrera Resource Manager-mallar med Azure-pipeline

Visual Studio tillhandahåller Azure Resource Group-projektet för att skapa mallar och distribuera dem till din Azure-prenumeration. Du kan integrera det här projektet med Azure-pipelines för kontinuerlig integrering och kontinuerlig distribution (CI/CD).

Det finns två sätt att distribuera mallar med Azure-pipeliner:

* **Lägg till aktivitet som kör ett Azure PowerShell-skript**. Det här alternativet har fördelen att tillhandahålla konsekvens under utvecklings livs cykeln eftersom du använder samma skript som ingår i Visual Studio-projektet (Deploy-AzureResourceGroup. ps1). Skript stegen artefakter från ditt projekt till ett lagrings konto som Resource Manager har åtkomst till. Artefakter är objekt i projektet, till exempel länkade mallar, skript och binärfiler för program. Sedan distribuerar skriptet mallen.

* **Lägg till aktiviteter för att kopiera och distribuera uppgifter**. Det här alternativet erbjuder ett användbart alternativ till projekt skriptet. Du konfigurerar två aktiviteter i pipelinen. En uppgift steg för artefakterna och den andra aktiviteten distribuerar mallen.

Den här artikeln visar båda metoderna.

## <a name="prepare-your-project"></a>Förbered ditt projekt

Den här artikeln förutsätter att Visual Studio-projektet och Azure DevOps-organisationen är redo att skapa pipelinen. Följande steg visar hur du kan se till att du är redo:

* Du har en Azure DevOps-organisation. Om du inte har ett kan du [skapa ett kostnads fritt](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Om ditt team redan har en Azure DevOps-organisation, se till att du är administratör för det Azure DevOps-projekt som du vill använda.

* Du har konfigurerat en [tjänst anslutning](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) till din Azure-prenumeration. Aktiviteterna i pipelinen körs under identiteten för tjänstens huvud namn. Anvisningar för hur du skapar anslutningen finns i [skapa ett DevOps-projekt](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Du har ett Visual Studio-projekt som har skapats från start mal len för **Azure Resource Group** . Information om hur du skapar den typen av projekt finns i [skapa och Distribuera Azure-resurs grupper via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Ditt Visual Studio-projekt är [anslutet till ett Azure DevOps-projekt](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Skapa pipeline

1. Om du inte har lagt till en pipeline tidigare måste du skapa en ny pipeline. Välj **pipeliner** och **ny pipeline**från din Azure DevOps-organisation.

   ![Lägg till ny pipeline](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Ange var koden lagras. Följande bild visar hur du väljer **Azure databaser git**.

   ![Välj kod källa](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Från den källan väljer du den lagrings plats som innehåller koden för projektet.

   ![Välj databas](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Välj den typ av pipeline som ska skapas. Du kan välja **starter pipeline**.

   ![Välj pipeline](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Du är redo att antingen lägga till en Azure PowerShell uppgift eller kopiera filen och distribuera uppgifter.

## <a name="azure-powershell-task"></a>Azure PowerShell aktivitet

I det här avsnittet visas hur du konfigurerar kontinuerlig distribution med hjälp av en enda aktivitet som kör PowerShell-skriptet i projektet. Följande YAML-fil skapar en [Azure PowerShell-aktivitet](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

När du ställer in uppgiften till `AzurePowerShell@3`, använder pipelinen kommandon från modulen AzureRM för att autentisera anslutningen. PowerShell-skriptet i Visual Studio-projektet använder som standard AzureRM-modulen. Om du har uppdaterat skriptet för att använda [AZ-modulen](/powershell/azure/new-azureps-module-az)anger du uppgiften till `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Ange namnet på den tjänst anslutning som du har skapat för `azureSubscription`.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

För `scriptPath`anger du den relativa sökvägen från pipeline-filen till skriptet. Du kan titta på lagrings platsen för att se sökvägen.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Om du inte behöver mellanlagra artefakter kan du bara skicka namn och plats för en resurs grupp som ska användas för distribution. Visual Studio-skriptet skapar resurs gruppen om den inte redan finns.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Om du behöver mellanlagra artefakter till ett befintligt lagrings konto använder du:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Nu när du förstår hur du skapar uppgiften ska vi gå igenom stegen för att redigera pipelinen.

1. Öppna din pipeline och ersätt innehållet med din YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Välj **Spara**.

   ![Spara pipeline](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Ange ett meddelande för incheckning och spara direkt till **Master**.

1. När du väljer **Spara**körs Bygg pipelinen automatiskt. Gå tillbaka till sammanfattningen för din build-pipeline och se status.

   ![Visa resultat](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Du kan välja den pipeline som körs för tillfället för att se information om aktiviteterna. När den är klar visas resultaten för varje steg.

## <a name="copy-and-deploy-tasks"></a>Kopiera och distribuera uppgifter

I det här avsnittet visas hur du konfigurerar kontinuerlig distribution genom att använda två aktiviteter för att mellanlagra artefakterna och distribuera mallen.

Följande YAML visar [Azure File Copy-aktiviteten](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Det finns flera delar av den här uppgiften att ändra för din miljö. `SourcePath` anger platsen för artefakterna i förhållande till pipelin filen. I det här exemplet finns filerna i en mapp med namnet `AzureResourceGroup1` som var namnet på projektet.

```yaml
SourcePath: '<path-to-artifacts>'
```

Ange namnet på den tjänst anslutning som du har skapat för `azureSubscription`.

```yaml
azureSubscription: '<your-connection-name>'
```

För lagrings-och behållar namn anger du namnen på det lagrings konto och den behållare som du vill använda för att lagra artefakterna. Lagrings kontot måste finnas.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Följande YAML visar [distributions uppgiften för Azure Resource Manager mallar](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Det finns flera delar av den här uppgiften att ändra för din miljö.

- `deploymentScope`: Välj distributions område från alternativen: `Management Group`, `Subscription` och `Resource Group`. Använd **resurs grupp** i den här genom gången. Mer information om omfattningarna finns i [distributions omfång](./resource-group-template-deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Ange namnet på den tjänst anslutning som du har skapat.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: ange prenumerations-ID för mål. Den här egenskapen gäller endast för distributions omfånget för resurs gruppen och bilden för prenumerations distribution.

- `resourceGroupName` och `location`: Ange namn och plats för den resurs grupp som du vill distribuera till. Aktiviteten skapar resurs gruppen om den inte finns.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

Distributions aktiviteten länkar till en mall med namnet `WebSite.json` och en parameter fil med namnet webbplats. Parameters. JSON. Använd namnen på din mall och parameter-filer.

Nu när du förstår hur du skapar aktiviteterna ska vi gå igenom stegen för att redigera pipelinen.

1. Öppna din pipeline och ersätt innehållet med din YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Välj **Spara**.

1. Ange ett meddelande för incheckning och spara direkt till **Master**.

1. När du väljer **Spara**körs Bygg pipelinen automatiskt. Gå tillbaka till sammanfattningen för din build-pipeline och se status.

   ![Visa resultat](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Du kan välja den pipeline som körs för tillfället för att se information om aktiviteterna. När den är klar visas resultaten för varje steg.

## <a name="next-steps"></a>Nästa steg

Stegvisa anvisningar för hur du använder Azure-pipeliner med Resource Manager-mallar finns i [självstudie: kontinuerlig integrering av Azure Resource Manager mallar med Azure-pipeliner](resource-manager-tutorial-use-azure-pipelines.md).
