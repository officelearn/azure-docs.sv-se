---
title: Kontinuerlig integrering med Azure Pipelines
description: Lär dig hur du kontinuerligt skapar, testar och distribuerar Azure Resource Manager mallar.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2dfe17b99c1a9b1130695c8e5cd9c65ca7681d35
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472452"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Självstudie: kontinuerlig integrering av Azure Resource Manager mallar med Azure-pipelines

Lär dig hur du använder Azure pipelines för att kontinuerligt bygga och Distribuera Azure Resource Manager mal Lav projekt.

Azure DevOps tillhandahåller utvecklartjänster som stöd för team för att planera arbete, samar beta med kod utveckling och bygga och distribuera program. Utvecklare kan arbeta i molnet med Azure DevOps Services. Azure DevOps tillhandahåller en integrerad uppsättning funktioner som du kan komma åt via webbläsaren eller IDE-klienten. Azure pipeline är en av dessa funktioner. Azure-pipelines är en helt aktuell tjänst för kontinuerlig integrering (CI) och kontinuerlig leverans (CD). Det fungerar med din prioriterade git-Provider och kan distribueras till de flesta större moln tjänster. Sedan kan du automatisera bygge, testning och distribution av koden till Microsoft Azure, Google Cloud Platform eller Amazon Web Services.

Den här självstudien är avsedd för Azure Resource Manager mallar för utvecklare som är nya Azure DevOps-tjänster och Azure-pipeliner. Om du redan är bekant med GitHub och DevOps kan du gå vidare till [skapa en pipeline](#create-a-pipeline).

> [!NOTE]
> Välj ett projekt namn. När du går igenom självstudien ersätter du någon av **AzureRmPipeline** med ditt projekt namn.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en GitHub-lagringsplats
> * Skapa ett Azure DevOps-projekt
> * Skapa en Azure-pipeline
> * Verifiera pipeline-distributionen
> * Uppdatera mallen och distribuera om
> * Rensa resurser

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* **Ett GitHub-konto**där du använder det för att skapa en lagrings plats för mallarna. Om du inte har något konto kan du [skapa ett utan kostnad](https://github.com). Mer information om hur du använder GitHub-databaser finns i [bygga GitHub-databaser](/azure/devops/pipelines/repos/github).
* **Installera Git**. I den här självstudien används *git bash* eller *git-gränssnittet*. Anvisningar finns i [Installera git]( https://www.atlassian.com/git/tutorials/install-git).
* **En Azure DevOps-organisation**. Om du inte har ett kan du skapa ett kostnads fritt. Se [skapa en organisation eller en projekt samling]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio Code med Resource Manager Tools-tillägg. [Skapa Azure Resource Manager mallar i använda Visual Studio Code](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Förbereda en GitHub-lagringsplats

GitHub används för att lagra projekt käll koden, inklusive Resource Manager-mallar. För andra databaser som stöds, se [databaser som stöds av Azure-DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Skapa en GitHub-lagringsplats

Om du inte har ett GitHub-konto, se [krav](#prerequisites).

1. Logga in på [GitHub](https://github.com).
2. Välj din konto avbildning i det övre högra hörnet och välj sedan **dina databaser**.

    ![Azure Resource Manager Azure-pipeline för Azure DevOps skapa GitHub-lagringsplats](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Välj **nytt**, en grön knapp.
1. I **databas namn**anger du ett namn på databasen.  Till exempel **AzureRmPipeline-lagrings platsen**. Kom ihåg att ersätta **AzureRmPipeline** med ditt projekt namn. Du kan välja antingen **offentlig** eller **privat** för att gå igenom den här självstudien. Och välj sedan **skapa lagrings plats**.
1. Skriv ner URL: en. URL: en för databasen är i följande format:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Den här lagrings platsen kallas för en *fjärrlagringsplats*. Varje utvecklare av samma projekt kan klona sin egen *lokala lagrings plats*och sammanfoga ändringarna till fjärrlagringsplatsen.

### <a name="clone-the-remote-repository"></a>Klona fjärrlagringsplatsen

1. Öppna git-Shell eller git-bash.  Se [Förutsättningar](#prerequisites).
1. Kontrol lera att din aktuella mapp är **GitHub**.
1. Kör följande kommando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Ersätt **[YourAccountName]** med namnet på ditt GitHub-konto och Ersätt **[YourGitHubRepositoryName]** med ditt databas namn som du skapade i föregående procedur.

    Följande skärm bild visar ett exempel.

    ![Azure Resource Manager Azure-pipeline för Azure DevOps skapa GitHub bash](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

Mappen **CreateAzureStorage** är den mapp där mallen lagras. Kommandot **PWD** visar mappsökvägen. Sökvägen är den plats där du sparar mallen i följande procedur.

### <a name="download-a-quickstart-template"></a>Hämta en snabb starts mall

I stället för att skapa en mall kan du hämta en [snabb starts mall]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Den här mallen skapar ett Azure Storage-konto.

1. Öppna Visual Studio Code. Se [Förutsättningar](#prerequisites).
2. Öppna mallen med följande URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Spara filen som **azuredeploy. JSON** i mappen **CreateAzureStorage** . Både mappnamnet och fil namnet används som de är i pipelinen.  Om du ändrar namnen måste du uppdatera namnen som används i pipelinen.

### <a name="push-the-template-to-the-remote-repository"></a>Skicka mallen till fjärrlagringsplatsen

Azuredeploy. JSON har lagts till i den lokala lagrings platsen. Sedan laddar du upp mallen till fjärrlagringsplatsen.

1. Öppna *git-Shell* eller *git-bash*, om den inte är öppen.
1. Ändra katalogen till mappen CreateAzureStorage på din lokala lagrings plats.
1. Kontrol lera att filen **azuredeploy. JSON** finns i mappen.
1. Kör följande kommando:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Du kan få en varning om LF. Du kan ignorera varningen. **Master** är huvud grenen.  Du skapar vanligt vis en gren för varje uppdatering. För att förenkla självstudien använder du huvud grenen direkt.
1. Bläddra till GitHub-lagringsplatsen från en webbläsare.  URL: en är **https://github.com/ [YourAccountName]/[YourGitHubRepository]** . Du ska se mappen **CreateAzureStorage** och **Azuredeploy. JSON** i mappen.

Hittills har du skapat en GitHub-lagringsplats och laddat upp en mall till lagrings platsen.

## <a name="create-a-devops-project"></a>Skapa ett DevOps-projekt

En DevOps-organisation krävs innan du kan fortsätta till nästa procedur.  Om du inte har en sådan, se [krav](#prerequisites).

1. Logga in på [Azure-DevOps](https://dev.azure.com).
1. Välj en DevOps organisation från vänster.

    ![Azure Resource Manager Azure-pipeline för Azure DevOps skapa Azure DevOps-projekt](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Välj **Skapa projekt**. Om du inte har några projekt öppnas sidan Skapa projekt automatiskt.
1. Ange följande värden:

    * **Projekt namn**: Ange ett projekt namn. Du kan använda det projekt namn som du valde i början av självstudien.
    * **Versions kontroll**: Välj **git**. Du kan behöva expandera **Avancerad** för att se **versions kontrollen**.

    Använd standardvärdet för de andra egenskaperna.
1. Välj **Skapa projekt**.

Skapa en tjänst anslutning som används för att distribuera projekt till Azure.

1. Välj **projekt inställningar** längst ned på den vänstra menyn.
1. Välj **tjänst anslutningar** under **pipeliner**.
1. Välj **ny tjänst anslutning**och välj sedan **AzureResourceManager**.
1. Ange följande värden:

    * **Anslutnings namn**: Ange ett anslutnings namn. Till exempel **AzureRmPipeline-ansluten**. Skriv ned det här namnet. du behöver namnet när du skapar din pipeline.
    * **Omfattnings nivå**: Välj **prenumeration**.
    * **Prenumeration**: Välj din prenumeration.
    * **Resurs grupp**: lämna det tomt.
    * **Tillåt alla pipeliner att använda den här anslutningen**. välja
1. Välj **OK**.

## <a name="create-a-pipeline"></a>Skapa en pipeline

Tills nu har du slutfört följande uppgifter.  Om du hoppar över föregående avsnitt eftersom du är van vid GitHub och DevOps måste du slutföra uppgifterna innan du fortsätter.

- Skapa en GitHub-lagringsplats och spara [mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) i mappen **CreateAzureStorage** i lagrings platsen.
- Skapa ett DevOps-projekt och skapa en Azure Resource Manager tjänst anslutning.

Så här skapar du en pipeline med ett steg för att distribuera en mall:

1. Välj **pipeliner** på den vänstra menyn.
1. Välj **Ny pipeline**.
1. Välj **GitHub** på fliken **Connect** (Anslut). Om du uppmanas anger du dina GitHub-autentiseringsuppgifter och följer sedan anvisningarna. Om du ser följande skärm väljer du **endast Välj databaser**och kontrollerar att lagrings platsen finns i listan innan du väljer **Godkänn & installera**.

    ![Azure Resource Manager Azure DevOps Azure-pipeliner Välj bara databaser](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Välj din lagrings plats på fliken **Välj** .  Standard namnet är **[YourAccountName]/[YourGitHubRepositoryName]** .
1. På fliken **Konfigurera** väljer du **starter pipeline**. Den visar pipeline-filen **Azure-pipelines. yml** med två skript steg.
1. Ersätt avsnittet **steg** med följande yaml:

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

    Det ser ut så här:

    ![Azure Resource Manager Azure DevOps Azure pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Gör följande ändringar:

    * **deloymentScope**: Välj distributions område från alternativen: `Management Group``Subscription` och `Resource Group`. Använd **resurs grupp** i den här självstudien. Mer information om omfattningarna finns i [distributions omfång](deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: Ange namnet på tjänst anslutningen som du skapade tidigare.
    * **SubscriptionName**: Ange PRENUMERATIONS-ID för mål.
    * **åtgärd**: åtgärden **skapa eller uppdatera resurs grupp** utför 2 åtgärder-1. skapa en resurs grupp om ett nytt resurs grupps namn har angetts. 11.2. distribuera den angivna mallen.
    * **resourceGroupName**: Ange ett nytt resurs grupps namn. Till exempel **AzureRmPipeline-RG**.
    * **plats**: Ange platsen för resurs gruppen.
    * **templateLocation**: när den **länkade artefakten** anges söker aktiviteten efter mallfilen direkt från den anslutna lagrings platsen.
    * **csmFile** är sökvägen till mallfilen. Du behöver inte ange en mallparametrar-fil eftersom alla parametrar som definierats i mallen har standardvärden.

    Mer information om uppgiften finns i [distributions uppgiften för Azure Resource Group](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)och [Azure Resource Manager för mall distribution](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Välj **Spara och kör**.
1. Välj **Spara och kör** igen. En kopia av YAML-filen sparas i den anslutna lagrings platsen. Du kan se YAML-filen genom att bläddra till din lagrings plats.
1. Kontrol lera att pipelinen har körts.

    ![Azure Resource Manager Azure DevOps Azure pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna resurs gruppen. Namnet är det du angav i YAML-filen för pipelinen.  Du ska se ett lagrings konto som skapats.  Lagrings kontots namn börjar med **Store**.
1. Välj lagrings kontots namn för att öppna det.
1. Välj **Egenskaper**. Observera att **replikeringen** är **lokalt REDUNDANT lagring (LRS)** .

    ![Azure Resource Manager Azure-pipeline för Azure-DevOps Azure](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Uppdatera och distribuera om

När du uppdaterar mallen och push-överför ändringarna till fjärrlagringsplatsen uppdaterar pipelinen automatiskt resurserna, lagrings kontot i det här fallet.

1. Öppna **azuredeploy. JSON** från din lokala lagrings plats i Visual Studio Code.
1. Uppdatera **DefaultValue** för **storageAccountType** till **Standard_GRS**. Se följande skärmbild:

    ![Azure Resource Manager Azure DevOps Azure-pipeline uppdatera yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Spara ändringarna.
1. Skicka ändringarna till fjärrlagringsplatsen genom att köra följande kommandon från git bash/shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Det första kommandot synkroniserar den lokala lagrings platsen med fjärrlagringsplatsen. Kom ihåg att YAML-filen för pipelinen har lagts till i fjärrlagringsplatsen.

    När huvud delen av fjärrlagringsplatsen har uppdaterats, utlöses pipelinen igen.

Du kan kontrol lera ändringarna genom att kontrol lera egenskapen replikering för lagrings kontot.  Se [Verifiera distributionen](#verify-the-deployment).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

Du kanske också vill ta bort GitHub-lagringsplatsen och Azure DevOps-projektet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du en Azure DevOps-pipeline för att distribuera en Azure Resource Manager-mall. Mer information om att distribuera Azure-resurser i flera regioner, och om att använda säker distributionspraxis, finns i

> [!div class="nextstepaction"]
> [Använda säkra distributionsmetoder](./deployment-manager-tutorial.md)
