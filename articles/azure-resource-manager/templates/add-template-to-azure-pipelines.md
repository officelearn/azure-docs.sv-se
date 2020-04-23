---
title: CI/CD med Azure Pipelines och mallar
description: Beskriver hur du konfigurerar kontinuerlig integrering i Azure Pipelines med hjälp av Azure Resource Group-distributionsprojekt i Visual Studio för att distribuera Resource Manager-mallar.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084659"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrera ARM-mallar med Azure Pipelines

Visual Studio tillhandahåller Azure Resource Group-projektet för att skapa ARM-mallar (Azure Resource Manager) och distribuera dem till din Azure-prenumeration. Du kan integrera det här projektet med Azure Pipelines för kontinuerlig integrering och kontinuerlig distribution (CI/CD).

Det finns två sätt att distribuera mallar med Azure Pipelines:

* **Lägg till uppgift som kör ett Azure PowerShell-skript**. Det här alternativet har fördelen att tillhandahålla konsekvens under hela utvecklingslivscykeln eftersom du använder samma skript som ingår i Visual Studio-projektet (Deploy-AzureResourceGroup.ps1). Skriptet arrangerar artefakter från projektet till ett lagringskonto som Resource Manager kan komma åt. Artefakter är objekt i projektet, till exempel länkade mallar, skript och programbinärer. Skriptet distribuerar sedan mallen.

* **Lägg till uppgifter för att kopiera och distribuera uppgifter**. Det här alternativet erbjuder ett bekvämt alternativ till projektskriptet. Du kan konfigurera två aktiviteter i pipelinen. En aktivitet arrangerar artefakterna och den andra uppgiften distribuerar mallen.

Den här artikeln visar båda metoderna.

## <a name="prepare-your-project"></a>Förbered projektet

Den här artikeln förutsätter att visual studio-projektet och Azure DevOps-organisationen är redo för att skapa pipelinen. Följande steg visar hur du ser till att du är redo:

* Du har en Azure DevOps-organisation. Om du inte har en, [skapa en gratis](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Om ditt team redan har en Azure DevOps-organisation kontrollerar du att du är administratör för Azure DevOps-projektet som du vill använda.

* Du har konfigurerat en [tjänstanslutning](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) till din Azure-prenumeration. Aktiviteterna i pipelinen körs under identiteten för tjänstens huvudnamn. Steg för att skapa anslutningen finns i [Skapa ett DevOps-projekt](deployment-tutorial-pipeline.md#create-a-devops-project).

* Du har ett Visual Studio-projekt som har skapats från startmallen **för Azure Resource Group.** Information om hur du skapar den typen av projekt finns i [Skapa och distribuera Azure-resursgrupper via Visual Studio](create-visual-studio-deployment-project.md).

* Ditt Visual Studio-projekt är [anslutet till ett Azure DevOps-projekt](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Skapa pipeline

1. Om du inte har lagt till en pipeline tidigare måste du skapa en ny pipeline. Välj **Pipelines** och **Ny pipeline**från din Azure DevOps-organisation .

   ![Lägg till ny pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Ange var koden ska lagras. Följande bild visar valet av **Azure Repos Git**.

   ![Välj kodkälla](./media/add-template-to-azure-pipelines/select-source.png)

1. Från den källan väljer du den databas som har koden för projektet.

   ![Välj databas](./media/add-template-to-azure-pipelines/select-repo.png)

1. Välj vilken typ av pipeline som ska skapas. Du kan välja **Startpipeline**.

   ![Välj pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

Du är redo att antingen lägga till en Azure PowerShell-uppgift eller kopieringsfilen och distribuera uppgifter.

## <a name="azure-powershell-task"></a>Azure PowerShell-uppgift

I det här avsnittet visas hur du konfigurerar kontinuerlig distribution med hjälp av en enskild aktivitet som kör PowerShell-skriptet i projektet. Följande YAML-fil skapar en [Azure PowerShell-uppgift:](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)

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

När du ställer `AzurePowerShell@3`in uppgiften till använder pipelinen kommandon från AzureRM-modulen för att autentisera anslutningen. Som standard använder PowerShell-skriptet i Visual Studio-projektet AzureRM-modulen. Om du har uppdaterat skriptet för att använda `AzurePowerShell@4` [Az-modulen](/powershell/azure/new-azureps-module-az)ställer du in uppgiften på .

```yaml
steps:
- task: AzurePowerShell@4
```

För `azureSubscription`anger du namnet på den tjänstanslutning som du skapade.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

För `scriptPath`anger du den relativa sökvägen från pipeline-filen till skriptet. Du kan titta i databasen för att se sökvägen.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Om du inte behöver arrangera artefakter skickar du bara namnet och platsen för en resursgrupp som ska användas för distribution. Visual Studio-skriptet skapar resursgruppen om den inte redan finns.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Om du behöver arrangera artefakter till ett befintligt lagringskonto använder du:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Nu, när du förstår hur du skapar uppgiften, låt oss gå igenom stegen för att redigera pipelinen.

1. Öppna pipelinen och ersätt innehållet med YAML:

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

   ![Spara pipeline](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Ange ett meddelande för commit, och åta sig direkt att **behärska**.

1. När du väljer **Spara**körs byggpipelinen automatiskt. Gå tillbaka till sammanfattningen för din byggpipeline och titta på statusen.

   ![Visa resultat](./media/add-template-to-azure-pipelines/view-results.png)

Du kan välja den pipeline som körs för att se information om aktiviteterna. När den är klar ser du resultaten för varje steg.

## <a name="copy-and-deploy-tasks"></a>Kopiera och distribuera uppgifter

Det här avsnittet visar hur du konfigurerar kontinuerlig distribution med hjälp av två uppgifter för att iscensätta artefakterna och distribuera mallen.

Följande YAML visar [azure-filkopieringsuppgiften:](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)

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

Det finns flera delar av den här uppgiften som du vill ändra för din miljö. Anger `SourcePath` platsen för artefakterna i förhållande till pipeline-filen. I det här exemplet finns filerna `AzureResourceGroup1` i en mapp med namnet som var namnet på projektet.

```yaml
SourcePath: '<path-to-artifacts>'
```

För `azureSubscription`anger du namnet på den tjänstanslutning som du skapade.

```yaml
azureSubscription: '<your-connection-name>'
```

För lagrings- och behållarnamn anger du namnen på det lagringskonto och behållare som du vill använda för att lagra artefakterna. Lagringskontot måste finnas.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Följande YAML visar [distributionsaktiviteten för Azure Resource Manager-mallen:](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

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

Det finns flera delar av den här uppgiften som du vill ändra för din miljö.

- `deploymentScope`: Välj distributionens omfattning `Management Group`bland `Subscription` `Resource Group`alternativen: och . Använd **Resursgrupp** i den här genomståmen. Mer information om scope finns i [Distributionsomfattningar](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Ange namnet på den tjänstanslutning som du skapade.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Ange målprenumerations-ID. Den här egenskapen gäller endast distributionsomfånget för resursgruppen och prenumerationsdistributionsomfattningen.

- `resourceGroupName`och `location`: ange namn och plats för den resursgrupp som du vill distribuera till. Aktiviteten skapar resursgruppen om den inte finns.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

Distributionsuppgiften länkar till `WebSite.json` en namngiven mall och en parameterfil med namnet WebSite.parameters.json. Använd namnen på mall- och parameterfilerna.

Nu, när du förstår hur du skapar aktiviteterna, låt oss gå igenom stegen för att redigera pipelinen.

1. Öppna pipelinen och ersätt innehållet med YAML:

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

1. Ange ett meddelande för commit, och åta sig direkt att **behärska**.

1. När du väljer **Spara**körs byggpipelinen automatiskt. Gå tillbaka till sammanfattningen för din byggpipeline och titta på statusen.

   ![Visa resultat](./media/add-template-to-azure-pipelines/view-results.png)

Du kan välja den pipeline som körs för att se information om aktiviteterna. När den är klar ser du resultaten för varje steg.

## <a name="next-steps"></a>Nästa steg

Stegvis process för att använda Azure Pipelines med ARM-mallar finns i [Självstudiekurs: Kontinuerlig integrering av Azure Resource Manager-mallar med Azure Pipelines](deployment-tutorial-pipeline.md).
