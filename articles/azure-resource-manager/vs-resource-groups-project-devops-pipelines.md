---
title: CI/CD med Azure Pipelines och Resource Manager-mallar
description: Beskriver hur du ställer in kontinuerlig integration i Azure-Pipelines med projekt för distribution av Azure-resursgrupp i Visual Studio för att distribuera Resource Manager-mallar.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191465"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrera Resource Manager-mallar med Azure-Pipelines

Visual Studio har Azure-resursgrupp-projekt för att skapa mallar och distribuera dem till din Azure-prenumeration. Du kan integrera det här projektet med Azure Pipelines för kontinuerlig integrering och kontinuerlig distribution (CI/CD).

Det finns två sätt att distribuera mallar med Azure Pipelines:

* **Lägg till aktivitet som kör ett Azure PowerShell-skript**. Det här alternativet har fördelen med att tillhandahålla konsekvens i hela livscykeln för utveckling, eftersom du använder samma skript som ingår i Visual Studio-projekt (distribuera AzureResourceGroup.ps1). Skriptet faser artefakter från projektet till ett lagringskonto som har åtkomst till Resource Manager. Artefakter finns objekt i projektet som länkade mallar, skript och binärfilerna för programmet. Skriptet distribuerar sedan mallen.

* **Lägga till aktiviteter för att kopiera och distribuera uppgifter**. Det här alternativet är ett praktiskt alternativ till projekt-skriptet. Du kan konfigurera två aktiviteter i pipelinen. En aktivitet skapar etapper artefakter och den andra aktiviteten distribuerar mallen.

Den här artikeln visar båda metoderna.

## <a name="prepare-your-project"></a>Förbered ditt projekt

Den här artikeln förutsätter Visual Studio-projekt och Azure DevOps-organisation är redo för att skapa pipelinen. Följande steg visar hur du kontrollerar att du är klar:

* Du har en Azure DevOps-organisation. Om du inte har någon, [skapa ett gratis](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Om det finns redan en Azure DevOps-organisation, kontrollera att du är administratör för Azure DevOps-projektet som du vill använda för ditt team.

* Du har konfigurerat en [anslutning tjänst](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) på Azure-prenumerationen. Aktiviteterna i pipelinen kör under identiteten för tjänstens huvudnamn. Anvisningar om att skapa anslutningen finns i [skapar du ett DevOps-projekt](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Du har ett Visual Studio-projekt som har skapats med den **Azure-resursgrupp** Startmall. Information om hur du skapar den typ av projekt som finns i [skapa och distribuera Azure-resursgrupper via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Visual Studio-projektet är [är ansluten till ett Azure DevOps-projekt](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Skapa pipeline

1. Om du inte har lagt till en pipeline tidigare, måste du skapa en ny pipeline. Välj din Azure DevOps-organisation **Pipelines** och **ny pipeline**.

   ![Lägg till ny pipeline](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Ange var din kod ska lagras. Följande bild visar att välja **Azure lagringsplatser Git**.

   ![Välj Kodkälla för](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Välj den databas som har kod för ditt projekt från denna källa.

   ![Välj databas](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Välj typ av pipeline för att skapa. Du kan välja **Starter pipeline**.

   ![Välj pipeline](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Nu kan du antingen lägga till en aktivitet för Azure PowerShell eller kopieringsfil och distribuera uppgifter.

## <a name="azure-powershell-task"></a>Azure PowerShell-aktivitet

Det här avsnittet visar hur du konfigurerar kontinuerlig distribution med hjälp av en aktivitet som kör PowerShell-skript i projektet. Följande YAML-fil skapar en [Azure PowerShell-uppgift](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

När du anger aktiviteten till `AzurePowerShell@3`, pipelinen använder kommandon från AzureRM-modulen för att autentisera anslutningen. Som standard använder AzureRM-modulen i PowerShell-skriptet i Visual Studio-projektet. Om du har uppdaterat dina skript om du vill använda den [Az modulen](/powershell/azure/new-azureps-module-az), ge aktiviteten `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

För `azureSubscription`, ange namnet på den tjänstanslutning som du skapade.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

För `scriptPath`, den relativa sökvägen från filen pipeline i skriptet. Du kan leta i centrallagret till finns i sökvägen.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Om du inte behöver scenen artefakter kan bara skicka namn och platsen för en resursgrupp som ska användas för distribuering. Visual Studio-skript skapar resursgruppen om den inte redan finns.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Om du behöver scenen artefakter till ett befintligt lagringskonto kan använda:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Nu, att du förstår hur du skapar uppgiften, ska vi gå igenom stegen för att redigera pipelinen.

1. Öppna din pipeline och Ersätt innehållet med dina YAML:

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

1. Ange ett meddelande för genomförandet och spara direkt till **master**.

1. När du väljer **spara**, build-pipelinen körs automatiskt. Gå tillbaka till sammanfattning för build-pipeline och titta på statusen.

   ![Visa resultat](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Du kan välja att visa information om uppgifterna som körs för tillfället pipelinen. När den är klar visas resultaten för varje steg.

## <a name="copy-and-deploy-tasks"></a>Kopiera och distribuera uppgifter

Det här avsnittet visar hur du konfigurerar kontinuerlig distribution med hjälp av två saker att mellanlagra artefakter och distribuera mallen. 

I följande YAML-visas den [aktiviteter i Azure file copy](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

Det finns flera olika delar av den här aktiviteten för att ändra för din miljö. Den `SourcePath` platsen för artefakter i förhållande till pipeline-filen. I det här exemplet filerna finns i en mapp med namnet `AzureResourceGroup1` som var namnet på projektet.

```yaml
SourcePath: '<path-to-artifacts>'
```

För `azureSubscription`, ange namnet på den tjänstanslutning som du skapade.

```yaml
azureSubscription: '<your-connection-name>'
```

Ange namnen på lagringskontot och behållaren som du vill använda för att lagra artefakter för lagring och behållare. Lagringskontot måste finnas.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

I följande YAML-visas den [distributionsuppgift för Azure-resurs grupp](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Det finns flera olika delar av den här aktiviteten för att ändra för din miljö. För `azureSubscription`, ange namnet på den tjänstanslutning som du skapade.

```yaml
azureSubscription: '<your-connection-name>'
```

För `resourceGroupName` och `location`, ange namn och plats för resursgruppen som du vill distribuera till. Uppgiften skapas en resursgrupp om det inte finns.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

Aktivitetslänkar för distribution till en mall med namnet `WebSite.json` och en parameterfil med namnet WebSite.parameters.json. Använda namnen på din mall- och parameterfilerna filer.

Nu, att du förstår hur du skapar aktiviteterna, vi ska gå igenom stegen för att redigera pipelinen.

1. Öppna din pipeline och Ersätt innehållet med dina YAML:

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
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Välj **Spara**.

1. Ange ett meddelande för genomförandet och spara direkt till **master**.

1. När du väljer **spara**, build-pipelinen körs automatiskt. Gå tillbaka till sammanfattning för build-pipeline och titta på statusen.

   ![Visa resultat](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Du kan välja att visa information om uppgifterna som körs för tillfället pipelinen. När den är klar visas resultaten för varje steg.

## <a name="next-steps"></a>Nästa steg

För steg hur du använder Azure-Pipelines med Resource Manager-mallar, se [självstudien: Kontinuerlig integration av Azure Resource Manager-mallar med Azure Pipelines](resource-manager-tutorial-use-azure-pipelines.md).
