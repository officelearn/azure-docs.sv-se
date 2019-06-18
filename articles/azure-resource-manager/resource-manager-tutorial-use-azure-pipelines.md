---
title: Kontinuerlig integrering med Azure Pipelines | Microsoft Docs
description: Lär dig att kontinuerligt skapa, testa och distribuera Azure Resource Manager-mallar.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 85dc0476da12bea64610b6910b0682fef00f4b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057794"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Självstudier: Kontinuerlig integration av Azure Resource Manager-mallar med Azure-Pipelines

Lär dig hur du använder Azure Pipelines för att kontinuerligt skapa och distribuera Azure Resource Manager mallprojekt.

Azure DevOps tillhandahåller tjänster för utvecklare för att stödja team att planera arbete, samarbeta med kodutveckling, och skapa och distribuera program. Utvecklare kan arbeta i molnet med Azure DevOps-tjänsterna. Azure DevOps ger en integrerad uppsättning funktioner som du kommer åt via webbläsaren eller IDE-klienten. Azure Pipeline är en av dessa funktioner. Azure Pipelines är en komplett funktionalitet kontinuerlig integrering (CI) och kontinuerlig leverans (CD). Den fungerar med din önskade Git-provider och kan distribuera till de flesta större molntjänster. Sedan kan du automatisera build, testa och distribuera din kod till Microsoft Azure, Google Cloud Platform och Amazon Web Services.

Den här kursen är avsedd för Azure Resource Manager mall utvecklare som är nya Azure DevOps-tjänsterna och Azure-Pipelines. Om du redan är bekant med GitHub och DevOps, du kan gå vidare till [skapa en pipeline](#create-a-pipeline).

> [!NOTE]
> Välj ett projektnamn. När du går igenom självstudien ersätter du någon av de **AzureRmPipeline** med ditt projektnamn.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en GitHub-lagringsplats
> * Skapa ett Azure DevOps-projekt
> * Skapa en Azure-pipeline
> * Verifiera pipeline-distributionen
> * Uppdatera mallen och distribuera om
> * Rensa resurser

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* **Ett GitHub-konto**, där du använda den för att skapa en databas för dina mallar. Om du inte har något, kan du [skapa ett gratis](https://github.com). Läs mer om hur du använder GitHub-lagringsplatser, [skapa GitHub-lagringsplatser](/azure/devops/pipelines/repos/github).
* **Installera Git**. Den här självstudiekursen instruktionen använder *Git Bash* eller *Git Shell*. Anvisningar finns i [installera Git]( https://www.atlassian.com/git/tutorials/install-git).
* **En organisation med Azure DevOps**. Om du inte har någon kan skapa du en kostnadsfritt. Se [skapa en organisation eller samling]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[Visual Studio Code](https://code.visualstudio.com/) med Resource Manager Tools-tillägget**. Se [Installera tillägget](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>Förbereda en GitHub-lagringsplats

GitHub används för att lagra din projekt-källkod inklusive Resource Manager-mallar. Andra lagringsplatser som stöds finns i [lagringsplatser som stöds av Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Skapa en GitHub-lagringsplats

Om du inte har ett GitHub-konto kan du läsa [krav](#prerequisites).

1. Logga in på [GitHub](https://github.com).
2. Välj din bild av tjänstkonto i det övre högra hörnet och välj sedan **lagringsplatserna**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines skapa GitHub-lagringsplats](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Välj **New**, en grön knapp.
1. I **databasnamn**, ange namnet på lagringsplatsen.  Till exempel **AzureRmPipeline repo**. Kom ihåg att ersätta någon av **AzureRmPipeline** med ditt projektnamn. Du kan välja antingen **offentliga** eller **privata** för att gå igenom den här kursen. Välj sedan **skapa lagringsplatsen**.
1. Anteckna URL: en. URL för databasen är följande format:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Den här lagringsplatsen kallas en *fjärrdatabasen*. Var och en av utvecklare av samma projekt kan klona hans/hennes egna *lagringsplats*, och slå samman ändringar till fjärrdatabasen.

### <a name="clone-the-remote-repository"></a>Klona fjärrdatabasen

1. Öppna Git Shell eller Git Bash.  Se [Förutsättningar](#prerequisites).
1. Kontrollera den aktuella mappen exempelvis är **github**.
1. Kör följande kommando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Ersätt **[YourAccountName]** med dina GitHub-kontonamn och Ersätt **[YourGitHubRepositoryName]** med namnet på din lagringsplats som du skapade i föregående procedur.

    Följande skärmbilder visar ett exempel.

    ![Azure Resource Manager Azure DevOps Azure Pipelines skapa GitHub bash](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

Den **CreateAzureStorage** mappen är den mapp där mallen lagras. Den **pwd** -kommando visar sökvägen till mappen. Sökvägen är där du spara mallen i i följande procedur.

### <a name="download-a-quickstart-template"></a>Ladda ned en snabbstartsmall

Istället för att skapa en mall, kan du ladda ned en [snabbstartsmall]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Den här mallen skapar ett Azure Storage-konto.

1. Öppna Visual Studio code. Se [Förutsättningar](#prerequisites).
2. Öppna mallen med följande URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Spara filen som **azuredeploy.json** till den **CreateAzureStorage** mapp. Både namnet på mappen och filnamnet används som i pipelinen.  Om du ändrar dessa namn, måste du uppdatera de namn som används i pipelinen.

### <a name="push-the-template-to-the-remote-repository"></a>Push-mallen till fjärrdatabasen

Azuredeploy.json har lagts till den lokala lagringsplatsen. Därefter måste överföra du den till fjärrdatabasen.

1. Öppna *Git Shell* eller *Git Bash*, om den inte är öppen.
1. Ändra katalogen till mappen CreateAzureStorage i din lokala lagringsplats.
1. Kontrollera den **azuredeploy.json** filen är i mappen.
1. Kör följande kommando:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Du kan få en varning om LF. Du kan ignorera varningen. **Master** är huvudgrenen.  Vanligtvis skapar du en gren för varje uppdatering. För att förenkla självstudien kan använda du huvudgrenen direkt.
1. Bläddra till din GitHub-lagringsplats från en webbläsare.  URL: en är  **https://github.com/ [YourAccountName] / [YourGitHubRepository]** . Du bör se den **CreateAzureStorage** mapp och **Azuredeploy.json** i mappen.

Du har hittills skapat en GitHub-lagringsplats och laddat upp en mall till databasen.

## <a name="create-a-devops-project"></a>Skapa ett DevOps-projekt

En DevOps-organisation krävs innan du kan fortsätta till nästa procedur.  Om du inte har något [krav](#prerequisites).

1. Logga in på [Azure DevOps](https://dev.azure.com).
1. Välj en DevOps-organisation till vänster.

    ![Azure Resource Manager Azure DevOps Azure Pipelines skapa Azure DevOps-projekt](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Välj **Skapa projekt**. Om du inte har några projekt, öppnas sidan Skapa projektet automatiskt.
1. Ange följande värden:

    * **Projektnamnet**: Ange ett projektnamn. Du kan använda på projektets namn som du valde i början av självstudien.
    * **Versionskontroll**: Välj **Git**. Du kan behöva expandera **Avancerat** att se **versionskontroll**.

    Använd standardvärdet för de andra egenskaperna.
1. Välj **Skapa projekt**.

Skapa en anslutning för tjänsten som används för att distribuera projekt till Azure.

1. Välj **Projektinställningar** längst ned i den vänstra menyn.
1. Välj **anslutningar** under **Pipelines**.
1. Välj **ny tjänstanslutning**, och välj sedan **AzureResourceManager**.
1. Ange följande värden:

    * **Anslutningsnamn**: Ange ett namn på anslutningen. Till exempel **AzureRmPipeline an**. Anteckna det här namnet måste namnet när du skapar din pipeline.
    * **Begränsa nivå**: Välj **prenumeration**.
    * **Prenumeration**: Välj din prenumeration.
    * **Resursgrupp**: Lämna tomt.
    * **Tillåt alla pipelines så att de använder den här anslutningen**. (valt)
1. Välj **OK**.

## <a name="create-a-pipeline"></a>Skapa en pipeline

Fram till nu har du slutfört följande uppgifter.  Om du hoppar över i föregående avsnitt, eftersom du är bekant med GitHub och DevOps, måste du utföra åtgärder innan du fortsätter.

- Skapa en GitHub-lagringsplats och spara [den här mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) till den **CreateAzureStorage** mappen i databasen.
- Skapa en DevOps-projekt och skapa en tjänstanslutning för Azure Resource Manager.

Skapa en pipeline med ett steg för att distribuera en mall:

1. Välj **Pipelines** menyn till vänster.
1. Välj **ny pipeline**.
1. Från den **Connect** fliken **GitHub**. Om du blir tillfrågad, ange dina autentiseringsuppgifter för GitHub och följ sedan instruktionerna. Om du ser följande skärm väljer **bara välja databaser**, och kontrollera att databasen är i listan innan du väljer **godkänna och installera**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines bara välja databaser](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Från den **Välj** väljer du din lagringsplats.  Standardnamnet är **[YourAccountName] / [YourGitHubRepositoryName]** .
1. Från den **konfigurera** fliken **Starter pipeline**. Den visar den **azure pipelines.yml** pipeline-fil med två skriptstegen.
1. Ersätt den **steg** avsnitt med följande YAML:

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Det bör se ut som:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Gör följande ändringar:

    * **azureSubscription**: uppdatera värdet med tjänstanslutning som skapats i föregående procedur.
    * **åtgärden**: den **skapa eller uppdatera resursgrupp** åtgärd utför åtgärder med 2 - 1. Skapa en resursgrupp om ett nytt resursgruppnamn anges. 2. distribuera mallen anges.
    * **resourceGroupName**: Ange ett nytt resursgruppnamn. Till exempel **AzureRmPipeline-rg**.
    * **plats**: Ange platsen för resursgruppen.
    * **templateLocation**: när **länkade artefakt** anges, aktiviteten söker efter mallfilen direkt från den anslutna databasen.
    * **csmFile** är sökvägen till mallfilen. Du behöver inte ange en fil med mallparametrar eftersom alla parametrar som definierats i mallen har standardvärden.

    Läs mer om uppgiften [Azure Resursgruppsdistribution uppgift](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
1. Välj **Spara och kör**.
1. Välj **spara och kör** igen. En kopia av YAML-fil sparas i den anslutna databasen. Du kan se YAML-fil genom att bläddra till din databas.
1. Kontrollera att pipelinen har körts.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Öppna resursgruppen. Namnet är vad du angav i pipeline YAML-fil.  Du bör se ett lagringskonto som har skapats.  Lagringskontonamnet som börjar med **lagra**.
1. Välj namnet på lagringskontot att öppna den.
1. Välj **egenskaper**. Observera den **SKU** är **Standard_LRS**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines portal verifiering](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Uppdatera och distribuera om

När du uppdatera mallen och push-överföra ändringarna till fjärrdatabasen uppdateras pipelinen automatiskt resurser, storage-konto i det här fallet.

1. Öppna **azuredeploy.json** från din lokala lagringsplats i Visual Studio Code.
1. Uppdatera den **defaultValue** av **storageAccountType** till **Standard_GRS**. Se följande skärmbild:

    ![Azure Resource Manager Azure DevOps Azure Pipelines uppdatera yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Spara ändringarna.
1. Genomför ändringarna i fjärrdatabasen genom att köra följande kommandon från Git Bash /-gränssnittet.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Det första kommandot synkroniserar den lokala lagringsplatsen med fjärrdatabasen. Kom ihåg att pipelinen YAML-fil har lagts till fjärrdatabasen.

    Med huvuddelen av fjärrdatabasen uppdateras, utlöses pipelinen igen.

Bekräfta ändringarna, kan du kontrollera SKU för lagringskontot.  Se [verifiera distributionen](#verify-the-deployment).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

Du kanske också vill ta bort GitHub-lagringsplatsen och Azure DevOps-projekt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du en Azure DevOps-pipeline för att distribuera en Azure Resource Manager-mall. Mer information om att distribuera Azure-resurser i flera regioner, och om att använda säker distributionspraxis, finns i

> [!div class="nextstepaction"]
> [Använda Distributionshanteraren i Azure](./resource-manager-tutorial-deploy-vm-extensions.md)
