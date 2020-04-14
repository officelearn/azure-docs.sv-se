---
title: Kontinuerlig integrering med Azure Pipelines
description: Lär dig hur du kontinuerligt skapar, testar och distribuerar Azure Resource Manager-mallar.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6ce6f176a52a742a3216a5b761b34254027a1c5b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255081"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Självstudiekurs: Kontinuerlig integrering av Azure Resource Manager-mallar med Azure Pipelines

I föregående [självstudiekurs](./deployment-tutorial-linked-template.md)distribuerar du en länkad mall.  I den här självstudien får du lära dig hur du använder Azure Pipelines för att kontinuerligt skapa och distribuera Azure Resource Manager-mallprojekt.

Azure DevOps tillhandahåller utvecklartjänster för att stödja team för att planera arbete, samarbeta om kodutveckling och skapa och distribuera program. Utvecklare kan arbeta i molnet med Azure DevOps Services. Azure DevOps innehåller en integrerad uppsättning funktioner som du kan komma åt via din webbläsare eller IDE-klient. Azure Pipeline är en av dessa funktioner. Azure Pipelines är en komplett kontinuerlig integration (CI) och cd-tjänst (continuous delivery). Det fungerar med din föredragna Git-leverantör och kan distribuera till de flesta större molntjänster. Sedan kan du automatisera version, testning och distribution av din kod till Microsoft Azure, Google Cloud Platform eller Amazon Web Services.

> [!NOTE]
> Välj ett projektnamn. När du går igenom självstudien ersätter du någon av **AzureRmPipeline** med ditt projektnamn.
> Det här projektnamnet används för att generera resursnamn.  En av resursen är ett lagringskonto. Namn på lagringskonto måste vara mellan 3 och 24 tecken långa och endast med siffror och gemener. Namnet måste vara unikt. I mallen är lagringskontonamnet projektnamnet med "store" bifogat och projektnamnet måste vara mellan 3 och 11 tecken. Så projektnamnet måste uppfylla kraven för lagringskontonamn och har färre än 11 tecken.

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
* (valfritt) **Visual Studio-kod med resurshanterarens verktygstillägg**. Se [Använda Visual Studio-kod för att skapa Azure Resource Manager-mallar](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Förbereda en GitHub-lagringsplats

GitHub används för att lagra projektets källkod, inklusive Resource Manager-mallar. Andra databaser som stöds finns i [databaser som stöds av Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Skapa en GitHub-databas

Om du inte har ett GitHub-konto läser du [Förutsättningar](#prerequisites).

1. Logga in på [GitHub](https://github.com).
1. Välj kontobilden i det övre högra hörnet och välj sedan **Dina databaser**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines skapar GitHub-databas](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Välj **Ny**, en grön knapp.
1. Ange ett databasnamn i **databasnamn.**  **AzureRmPipeline-repo**. Kom ihåg att ersätta någon av **AzureRmPipeline** med ditt projektnamn. Du kan välja antingen **Offentlig** eller **privat** för att gå igenom den här självstudien. Och välj sedan **Skapa databas**.
1. Skriv ned webbadressen. Databasens URL är följande format:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Den här databasen kallas en *fjärrdatabas*. Var och en av utvecklarna i samma projekt kan klona sin egen *lokala databas*och sammanfoga ändringarna till fjärrdatabasen.

### <a name="clone-the-remote-repository"></a>Klona fjärrdatabasen

1. Öppna Git Shell eller Git Bash.  Se [Förutsättningar](#prerequisites).
1. Kontrollera att den aktuella mappen är **GitHub**.
1. Kör följande kommando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Ersätt **[YourAccountName]** med ditt GitHub-kontonamn och ersätt **[YourGitHubRepositoryName]** med ditt databasnamn som du skapade i föregående procedur.

**Mappen CreateWebApp** är den mapp där mallen lagras. Kommandot **pwd** visar mappsökvägen. Sökvägen är där du sparar mallen till i följande procedur.

### <a name="download-a-quickstart-template"></a>Ladda ned en snabbstartsmall

I stället för att skapa mallarna kan du hämta mallarna och spara dem i **mappen CreateWebApp.**

* Huvudmallen:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* Den länkade mallen:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Både mappnamnet och filnamnen används som de är på gång.  Om du ändrar dessa namn måste du uppdatera namnen som används i pipelinen.

### <a name="push-the-template-to-the-remote-repository"></a>Skicka mallen till fjärrdatabasen

Azuredeploy.json har lagts till i den lokala databasen. Därefter laddar du upp mallen till fjärrdatabasen.

1. Öppna *Git Shell* eller *Git Bash*, om det inte öppnas.
1. Ändra katalogen till mappen CreateWebApp i den lokala databasen.
1. Kontrollera att **filen azuredeploy.json** finns i mappen.
1. Kör följande kommando:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Du kan få en varning om LF. Du kan ignorera varningen. **master** är huvudgrenen.  Du skapar vanligtvis en gren för varje uppdatering. För att förenkla självstudien använder du huvudgrenen direkt.
1. Bläddra till din GitHub-databas från en webbläsare.  URL:en är ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. Du ska se **mappen CreateWebApp** och de tre filerna i mappen.
1. Välj **linkedStorageAccount.json** om du vill öppna mallen.
1. Välj knappen **Rå.** WEBBADRESSEN startas med **raw.githubusercontent.com**.
1. Skapa en kopia av URL:en.  Du måste ange det här värdet när du konfigurerar pipelinen senare i självstudien.

Hittills har du skapat en GitHub-databas och laddat upp mallarna till databasen.

## <a name="create-a-devops-project"></a>Skapa ett DevOps-projekt

En DevOps-organisation behövs innan du kan gå vidare till nästa procedur.  Om du inte har någon läser du [Förutsättningar](#prerequisites).

1. Logga in på [Azure DevOps](https://dev.azure.com).
1. Välj en DevOps-organisation från vänster.

    ![Azure Resource Manager Azure DevOps Azure Pipelines skapar Azure DevOps-projekt](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Välj **Nytt projekt**. Om du inte har några projekt öppnas sidan skapa projekt automatiskt.
1. Ange följande värden:

    * **Projektnamn**: ange ett projektnamn. Du kan använda projektnamnet som du valde i början av självstudien.
    * **Versionskontroll**: Välj **Git**. Du kan behöva expandera **Avancerat** för att se **versionskontroll**.

    Använd standardvärdet för de andra egenskaperna.
1. Välj **Skapa**.

Skapa en tjänstanslutning som används för att distribuera projekt till Azure.

1. Välj **Projektinställningar** längst ned på den vänstra menyn.
1. Välj **Tjänstanslutningar** under **Pipelines**.
1. Välj **Ny tjänstanslutning,** välj **Azure Resource Manager**och välj sedan **Nästa**.
1. Välj **Tjänstens huvudnamn**och välj sedan **Nästa**.
1. Ange följande värden:

    * **Omfattningsnivå**: välj **Prenumeration**.
    * **Prenumeration**: välj din prenumeration.
    * **Resursgrupp**: Lämna den tom.
    * **Anslutningsnamn**: ange ett anslutningsnamn. **AzureRmPipeline-conn**. Skriv ner det här namnet, du behöver namnet när du skapar din pipeline.
    * **Bevilja åtkomstbehörighet till alla pipelines**. (vald)
1. Välj **Spara**.

## <a name="create-a-pipeline"></a>Skapa en pipeline

Hittills har du slutfört följande uppgifter.  Om du hoppar över föregående avsnitt eftersom du är bekant med GitHub och DevOps måste du slutföra uppgifterna innan du fortsätter.

* Skapa en GitHub-databas och spara mallarna i **mappen CreateWebApp** i databasen.
* Skapa ett DevOps-projekt och skapa en Azure Resource Manager-tjänstanslutning.

Så här skapar du en pipeline med ett steg för att distribuera en mall:

1. Välj **Pipelines** på den vänstra menyn.
1. Välj **Ny pipeline**.
1. Välj **GitHub** på fliken **Connect** (Anslut). Om du tillfrågas anger du dina GitHub-autentiseringsuppgifter och följer sedan instruktionerna. Om följande skärm visas väljer du **Välj Endast databaser**och verifierar att databasen finns i listan innan du väljer Godkänn & **Installera**.

    ![Azure DevOps Azure Pipelines endast azure resource manager-databaser i Azure Resource Manager](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Välj databasen på fliken **Välj.**  Standardnamnet är **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Välj **Startpipeline på**fliken **Konfigurera** . Den visar **pipelines.yml-pipeline-filen** med två skriptsteg.
1. Ta bort de två skriptstegen från yml-filen.
1. Flytta markören till linjen efter **steg:**.
1. Välj **Visa assistent** till höger på skärmen om du vill öppna fönstret **Uppgifter.**
1. Välj **ARM-malldistribution**.
1. Ange följande värden:

    * **deploymentScope**: Välj **Resursgrupp**.. Mer information om scope finns i [Distributionsomfattningar](deploy-rest.md#deployment-scope).
    * **Azure Resource Manager-anslutning:** Välj det tjänstanslutningsnamn som du skapade tidigare.
    * **Prenumeration**: Ange målprenumerations-ID.
    * **Åtgärd:** Välj åtgärden **Skapa eller uppdatera resursgrupp** gör 2 åtgärder - 1. skapa en resursgrupp om ett nytt resursgruppsnamn anges. 2. distribuera den angivna mallen.
    * **Resursgrupp**: Ange ett nytt resursgruppnamn. **AzureRmPipeline-rg**.
    * **Plats**: Välj en plats för resursgruppen, till exempel **Central US**.
    * **Mallplats**: Välj **Länkad artefakt**, vilket innebär att uppgiften söker efter mallfilen direkt från den anslutna databasen.
    * **Mall**: Ange **CreateWebApp/azuredeploy.json**. Om du har ändrat mappnamnet och filnamnet måste du ändra det här värdet.
    * **Mallparametrar**: Lämna det här fältet tomt. Du anger parametervärdena i mallparametrarna **Åsidosätt.
    * **overrideParameters**: Enter **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]**. Ersätt projektnamnet och url:en för den länkade mallen. Den länkade mall-URL:en är vad du skrev ned i slutet av [Skapa en GitHub-databas](#create-a-github-repository).
    * **Distributionsläge**: Välj **Inkrementell**.
    * **Distributionsnamn**: Ange **DeployPipelineTemplate**. Välj **Avancerat** innan du kan se **Distributionsnamn**.

    ![Azure DevOps Azure Pipelines-steg i Azure Resource Manager](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Välj **Lägg till**.

    Mer information om aktiviteten finns i [Azure Resource Group Deployment task](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)och Azure Resource [Manager-malldistributionsuppgift](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    Yml-filen skall likna följande:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Välj **Spara och kör**.
1. Välj **Spara och kör** igen i fönstret Spara **och kör.** En kopia av YAML-filen sparas i den anslutna databasen. Du kan se YAML-filen genom att bläddra till databasen.
1. Kontrollera att pipelinen har körts.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna resursgruppen. Namnet är vad du angav i YAML-filen för pipeline.  Du ska se ett lagringskonto skapas.  Lagringskontonamnet börjar med **store**.
1. Välj det lagringskontonamn som ska öppnas.
1. Välj **egenskaper**. Observera **att replikering** är **lokalt redundant lagring (LRS)**.

## <a name="update-and-redeploy"></a>Uppdatera och distribuera om

När du uppdaterar mallen och skickar ändringarna till fjärrdatabasen uppdaterar pipelinen automatiskt resurserna, lagringskontot i det här fallet.

1. Öppna **linkedStorageAccount.json** från din lokala databas i Visual Studio-kod eller någon textredigerare.
1. Uppdatera **standardvärde för** **storageAccountType** till **Standard_GRS**. Se följande skärmbild:

    ![Azure Resource Manager Azure DevOps Azure Pipelines uppdatera yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Spara ändringarna.
1. Skicka ändringarna till fjärrdatabasen genom att köra följande kommandon från Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    Det första kommandot (pull) synkroniserar den lokala databasen med fjärrdatabasen. DEN PIPELINE YAML filen lades bara till i fjärrdatabasen. Om du kör pull-kommandot hämtas en kopia av YAML-filen till den lokala grenen.

    Det fjärde kommandot (push) överför den reviderade linkedStorageAccount.json-filen till fjärrdatabasen. När huvudgrenen för fjärrdatabasen har uppdaterats utlöses pipelinen igen.

Om du vill verifiera ändringarna kan du kontrollera replikeringsegenskapen för lagringskontot.  Se [Verifiera distributionen](#verify-the-deployment).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

Du kanske också vill ta bort GitHub-databasen och Azure DevOps-projektet.

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här resource manager-malldistributionshandledningen. Låt oss veta om du har några kommentarer och förslag i feedbackavsnittet. Tack!
Du är redo att hoppa in i mer avancerade begrepp om mallar. Nästa självstudiekurs går in mer i detalj om hur du använder mallreferensdokumentation för att hjälpa till med att definiera resurser att distribuera.

> [!div class="nextstepaction"]
> [Använda mallreferens](./template-tutorial-use-template-reference.md)
