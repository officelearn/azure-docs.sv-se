---
title: Kontinuerlig integrering med Azure Pipelines
description: Lär dig hur du kontinuerligt skapar, testar och distribuerar Azure Resource Manager-mallar.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b117861a528b6983876d28d5b343ea88c2bcadc0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260691"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Självstudiekurs: Kontinuerlig integrering av Azure Resource Manager-mallar med Azure Pipelines

Lär dig hur du använder Azure Pipelines för att kontinuerligt skapa och distribuera Azure Resource Manager-mallprojekt.

Azure DevOps tillhandahåller utvecklartjänster för att stödja team för att planera arbete, samarbeta om kodutveckling och skapa och distribuera program. Utvecklare kan arbeta i molnet med Azure DevOps Services. Azure DevOps innehåller en integrerad uppsättning funktioner som du kan komma åt via din webbläsare eller IDE-klient. Azure Pipeline är en av dessa funktioner. Azure Pipelines är en komplett kontinuerlig integration (CI) och cd-tjänst (continuous delivery). Det fungerar med din föredragna Git-leverantör och kan distribuera till de flesta större molntjänster. Sedan kan du automatisera version, testning och distribution av din kod till Microsoft Azure, Google Cloud Platform eller Amazon Web Services.

Den här självstudien är utformad för Azure Resource Manager-mallutvecklare som är nya Azure DevOps-tjänster och Azure Pipelines. Om du redan är bekant med GitHub och DevOps kan du hoppa till [Skapa en pipeline](#create-a-pipeline).

> [!NOTE]
> Välj ett projektnamn. När du går igenom självstudien ersätter du någon av **AzureRmPipeline** med ditt projektnamn.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en GitHub-lagringsplats
> * Skapa ett Azure DevOps-projekt
> * Skapa en Azure-pipeline
> * Verifiera pipeline-distributionen
> * Uppdatera mallen och distribuera om
> * Rensa resurser

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* **Ett GitHub-konto**, där du använder det för att skapa en databas för dina mallar. Om du inte har en, kan du [skapa en gratis](https://github.com). Mer information om hur du använder GitHub-databaser finns i [Skapa GitHub-databaser](/azure/devops/pipelines/repos/github).
* **Installera Git**. Den här självstudieinstruktionen använder *Git Bash* eller *Git Shell*. Instruktioner finns i [Installera Git]( https://www.atlassian.com/git/tutorials/install-git).
* **En Azure DevOps-organisation**. Om du inte har en, kan du skapa en gratis. Se [Skapa en organisation eller projektsamling]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio-kod med resurshanterarens verktygstillägg. Se [Använda Visual Studio-kod för att skapa Azure Resource Manager-mallar](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Förbereda en GitHub-lagringsplats

GitHub används för att lagra projektets källkod, inklusive Resource Manager-mallar. Andra databaser som stöds finns i [databaser som stöds av Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Skapa en GitHub-databas

Om du inte har ett GitHub-konto läser du [Förutsättningar](#prerequisites).

1. Logga in på [GitHub](https://github.com).
2. Välj kontobilden i det övre högra hörnet och välj sedan **Dina databaser**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines skapar GitHub-databas](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Välj **Ny**, en grön knapp.
1. Ange ett databasnamn i **databasnamn.**  **AzureRmPipeline-repo**. Kom ihåg att ersätta någon av **AzureRmPipeline** med ditt projektnamn. Du kan välja antingen **Offentlig** eller **privat** för att gå igenom den här självstudien. Och välj sedan **Skapa databas**.
1. Skriv ned webbadressen. Databasens URL är följande format:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Den här databasen kallas en *fjärrdatabas*. Var och en av utvecklarna i samma projekt kan klona sin egen *lokala databas*och sammanfoga ändringarna till fjärrdatabasen.

### <a name="clone-the-remote-repository"></a>Klona fjärrdatabasen

1. Öppna Git Shell eller Git Bash.  Se [Förutsättningar](#prerequisites).
1. Kontrollera att den aktuella mappen är **github**.
1. Kör följande kommando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Ersätt **[YourAccountName]** med ditt GitHub-kontonamn och ersätt **[YourGitHubRepositoryName]** med ditt databasnamn som du skapade i föregående procedur.

    Följande skärmbild visar ett exempel.

    ![Azure Resource Manager Azure DevOps Azure Pipelines skapar GitHub-bash](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

**Mappen CreateAzureStorage** är den mapp där mallen lagras. Kommandot **pwd** visar mappsökvägen. Sökvägen är där du sparar mallen till i följande procedur.

### <a name="download-a-quickstart-template"></a>Ladda ned en snabbstartsmall

I stället för att skapa en mall kan du hämta en [snabbstartsmall]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Den här mallen skapar ett Azure Storage-konto.

1. Öppna Visual Studio-kod. Se [Förutsättningar](#prerequisites).
2. Öppna mallen med följande URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Spara filen som **azuredeploy.json** i mappen **CreateAzureStorage.** Både mappnamnet och filnamnet används som de är på gång.  Om du ändrar dessa namn måste du uppdatera namnen som används i pipelinen.

### <a name="push-the-template-to-the-remote-repository"></a>Skicka mallen till fjärrdatabasen

Azuredeploy.json har lagts till i den lokala databasen. Därefter laddar du upp mallen till fjärrdatabasen.

1. Öppna *Git Shell* eller *Git Bash*, om det inte öppnas.
1. Ändra katalogen till mappen CreateAzureStorage i den lokala databasen.
1. Kontrollera att **filen azuredeploy.json** finns i mappen.
1. Kör följande kommando:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Du kan få en varning om LF. Du kan ignorera varningen. **master** är huvudgrenen.  Du skapar vanligtvis en gren för varje uppdatering. För att förenkla självstudien använder du huvudgrenen direkt.
1. Bläddra till din GitHub-databas från en webbläsare.  URL:en är ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. Du ska se mappen **CreateAzureStorage** och **Azuredeploy.json** i mappen.

Hittills har du skapat en GitHub-databas och laddat upp en mall till databasen.

## <a name="create-a-devops-project"></a>Skapa ett DevOps-projekt

En DevOps-organisation behövs innan du kan gå vidare till nästa procedur.  Om du inte har någon läser du [Förutsättningar](#prerequisites).

1. Logga in på [Azure DevOps](https://dev.azure.com).
1. Välj en DevOps-organisation från vänster.

    ![Azure Resource Manager Azure DevOps Azure Pipelines skapar Azure DevOps-projekt](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Välj **Skapa projekt**. Om du inte har några projekt öppnas sidan skapa projekt automatiskt.
1. Ange följande värden:

    * **Projektnamn**: ange ett projektnamn. Du kan använda projektnamnet som du valde i början av självstudien.
    * **Versionskontroll**: Välj **Git**. Du kan behöva expandera **Avancerat** för att se **versionskontroll**.

    Använd standardvärdet för de andra egenskaperna.
1. Välj **Skapa projekt**.

Skapa en tjänstanslutning som används för att distribuera projekt till Azure.

1. Välj **Projektinställningar** längst ned på den vänstra menyn.
1. Välj **Tjänstanslutningar** under **Pipelines**.
1. Välj **Ny tjänstanslutning**och välj sedan **AzureResourceManager**.
1. Ange följande värden:

    * **Anslutningsnamn**: ange ett anslutningsnamn. **AzureRmPipeline-conn**. Skriv ner det här namnet, du behöver namnet när du skapar din pipeline.
    * **Omfattningsnivå**: välj **Prenumeration**.
    * **Prenumeration**: välj din prenumeration.
    * **Resursgrupp**: Lämna den tom.
    * **Tillåt alla pipelines att använda den här anslutningen**. (vald)
1. Välj **OK**.

## <a name="create-a-pipeline"></a>Skapa en pipeline

Hittills har du slutfört följande uppgifter.  Om du hoppar över föregående avsnitt eftersom du är bekant med GitHub och DevOps måste du slutföra uppgifterna innan du fortsätter.

- Skapa en GitHub-databas och spara [den här mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) i mappen **CreateAzureStorage** i databasen.
- Skapa ett DevOps-projekt och skapa en Azure Resource Manager-tjänstanslutning.

Så här skapar du en pipeline med ett steg för att distribuera en mall:

1. Välj **Pipelines** på den vänstra menyn.
1. Välj **Ny pipeline**.
1. Välj **GitHub** på fliken **Connect** (Anslut). Om du tillfrågas anger du dina GitHub-autentiseringsuppgifter och följer sedan instruktionerna. Om följande skärm visas väljer du **Välj Endast databaser**och verifierar att databasen finns i listan innan du väljer Godkänn & **Installera**.

    ![Azure DevOps Azure Pipelines endast azure resource manager-databaser i Azure Resource Manager](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Välj databasen på fliken **Välj.**  Standardnamnet är **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Välj **Startpipeline på**fliken **Konfigurera** . Den visar **pipelines.yml-pipeline-filen** med två skriptsteg.
1. Ersätt **stegavsnittet** med följande YAML:

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Det skall se ut som:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Gör följande ändringar:

    * **deploymentScope**: Välj distributionens omfattning `Management Group` `Subscription` bland `Resource Group`alternativen: och . Använd **resursgrupp** i den här självstudien. Mer information om scope finns i [Distributionsomfattningar](deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: Ange det tjänstanslutningsnamn som du skapade tidigare.
    * **SubscriptionName**: Ange målprenumerations-ID.
    * **åtgärd:** Åtgärden **Skapa eller uppdatera resursgrupp** gör 2 åtgärder - 1. skapa en resursgrupp om ett nytt resursgruppsnamn anges. 2. distribuera den angivna mallen.
    * **resourceGroupName**: ange ett nytt resursgruppnamn. **AzureRmPipeline-rg**.
    * **plats:** ange plats för resursgruppen.
    * **templateLocation**: när **länkad artefakt** anges söker uppgiften efter mallfilen direkt från den anslutna databasen.
    * **csmFile** är sökvägen till mallfilen. Du behöver inte ange en mallparametrarfil eftersom alla parametrar som definieras i mallen har standardvärden.

    Mer information om aktiviteten finns i [Azure Resource Group Deployment task](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)och Azure Resource [Manager-malldistributionsuppgift](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Välj **Spara och kör**.
1. Välj **Spara och kör** igen. En kopia av YAML-filen sparas i den anslutna databasen. Du kan se YAML-filen genom att bläddra till databasen.
1. Kontrollera att pipelinen har körts.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna resursgruppen. Namnet är vad du angav i YAML-filen för pipeline.  Du ska se ett lagringskonto skapas.  Lagringskontonamnet börjar med **store**.
1. Välj det lagringskontonamn som ska öppnas.
1. Välj **egenskaper**. Observera **att replikering** är **lokalt redundant lagring (LRS)**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines-portalverifiering](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Uppdatera och distribuera om

När du uppdaterar mallen och skickar ändringarna till fjärrdatabasen uppdaterar pipelinen automatiskt resurserna, lagringskontot i det här fallet.

1. Öppna **azuredeploy.json** från din lokala databas i Visual Studio Code.
1. Uppdatera **standardvärde för** **storageAccountType** till **Standard_GRS**. Se följande skärmbild:

    ![Azure Resource Manager Azure DevOps Azure Pipelines uppdatera yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Spara ändringarna.
1. Skicka ändringarna till fjärrdatabasen genom att köra följande kommandon från Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Det första kommandot synkroniserar den lokala databasen med fjärrdatabasen. Kom ihåg att TV-filen för pipeline har lagts till i fjärrdatabasen.

    När huvudgrenen för fjärrdatabasen har uppdaterats utlöses pipelinen igen.

Om du vill verifiera ändringarna kan du kontrollera replikeringsegenskapen för lagringskontot.  Se [Verifiera distributionen](#verify-the-deployment).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

Du kanske också vill ta bort GitHub-databasen och Azure DevOps-projektet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du en Azure DevOps-pipeline för att distribuera en Azure Resource Manager-mall. Mer information om att distribuera Azure-resurser i flera regioner, och om att använda säker distributionspraxis, finns i

> [!div class="nextstepaction"]
> [Använda säkra distributionsmetoder](./deployment-manager-tutorial.md)
