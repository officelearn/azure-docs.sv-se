---
title: Skapa appar utan server med Azure Logic Apps och Azure Functions i Visual Studio
description: Skapa, distribuera och hantera ditt första serverlösa program med Azure Logic Apps och Azure Functions i Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 04/25/2019
ms.openlocfilehash: 265ed220b023a9d81fc5af48920152d95d9cac08
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65136695"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Skapa ditt första serverlösa program med Azure Logic Apps och Azure Functions – Visual Studio

Du kan snabbt utveckla och distribuera appar i molnet genom att använda de serverfria verktygen och funktionerna i Azure som [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Azure Functions](../azure-functions/functions-overview.md). Den här artikeln visar hur du börja skapa en app som använder en logikapp som anropar en Azure-funktion i Visual Studio utan server. Läs mer om serverlösa lösningar i Azure i [Azure utan server med Functions och Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill skapa en app utan server i Visual Studio, behöver du följande objekt:

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio 2019, 2017 eller 2015 – Community edition eller större](https://aka.ms/download-visual-studio). 
  I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017, kontrollera att du väljer den **Azure development** arbetsbelastning.

  * [Microsoft Azure SDK för .NET (2.9.1 eller senare)](https://azure.microsoft.com/downloads/). 
  Läs mer om [Azure SDK för .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps Tools för Visual Studio-version som du vill använda:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Se till att starta om Visual Studio när installationen är klar.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) för felsökning Functions lokalt

* Tillgång till Internet när du använder den inbäddade Logic App Designer

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp. 
  Om du t.ex. använder Dynamics CRM Online-anslutningen kontrollerar Designer om CRM-instansen har några standardegenskaper och anpassade egenskaper.

## <a name="create-resource-group-project"></a>Skapa resursgrupp-projekt

Kom igång genom att skapa en [Azure-resursgruppsprojekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) för din app utan server. I Azure måste skapa du resurser i en resursgrupp är en logisk samling som du använder för Ordna, hantera och distribuera resurser för en hel app som ett enskilt objekt. För en app utan server i Azure innehåller resursgruppen resurser för både Azure Logic Apps och Azure Functions. Läs mer om [Azure-resursgrupper och resurser](../azure-resource-manager/resource-group-overview.md).

1. Starta Visual Studio och logga in med ditt Azure-konto.

1. Gå till **Arkiv**-menyn och välj **Nytt** > **Projekt**.

   ![Skapa nytt projekt i Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Under **Installerad** väljer du **Visual C#** eller **Visual Basic**. Välj **Moln** > **Azure-resursgrupp**.

   > [!NOTE]
   > Om den **molnet** kategori eller **Azure-resursgrupp** projektet inte finns, kontrollera att du har installerat Azure SDK för Visual Studio.

   Följ dessa steg om du använder Visual Studio 2019:

   1. I den **skapa ett nytt projekt** väljer den **Azure-resursgrupp** projektmall för att antingen Visual C# eller Visual Basic, och välj **nästa**.

   1. Ange namn för Azure-resursgrupp som du vill använda och annan projektinformation. När du är klar väljer du **Skapa**.

1. Ge projektet ett namn och en plats och välj sedan **OK**.

   Visual Studio uppmanas du att välja en mall från listan över mallar. 
   Det här exemplet används en Azure-snabbstartsmall så att du kan skapa en app som innehåller en logikapp och ett anrop till en Azure-funktion utan server.

   > [!TIP]
   > I scenarier där du inte vill fördistribuera din lösning till en Azure-resursgrupp, kan du använda du tom **Logikapp** mall som bara skapar en tom logikapp.

1. Från den **Visa mallar från den här platsen** väljer **Azure Quickstart (github.com/Azure/azure-quickstart-templates)**.

1. I sökrutan anger du ”logikapp” som filter. Välj den här mallen från resultatet: **101-logic-app-and-function-app**

   ![Välj Azure-snabbstartsmall](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio skapar och öppnar en lösning för ditt resursgrupp-projekt. 
   Azure Quickstart-mallen som du har valt skapar en Distributionsmall med namnet `azuredeploy.json` i resursgruppsprojektet. Den här distributionsmallen innehåller definitionen för en enkel logikapp som utlöser på en HTTP-begäran, anropar en Azure-funktion och returnerar resultatet som ett HTTP-svar.

   ![Ny serverlös lösning](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Därefter måste du distribuera din lösning till Azure innan du kan öppna distributionsmallen för och granska resurser för din app utan server.

## <a name="deploy-your-solution"></a>Distribuera din lösning

Innan du kan öppna din logikapp med Logic App Designer i Visual Studio, måste du ha en Azure-resursgrupp som redan har distribuerats i Azure. Designern kan sedan skapa anslutningar till resurser och tjänster i din logikapp. Distribuera din lösning från Visual Studio till Azure-portalen för den här uppgiften.

1. I Solution Explorer från snabbmenyn för projektet resursen och välj **distribuera** > **New**.

   ![Skapa ny distribution för resursgrupp](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Om du inte redan är valt väljer du din Azure-prenumeration och resursgrupp där du vill distribuera. Välj **distribuera**.

   ![Distributionsinställningar](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Om den **redigera parametrar** visas anger du resursnamnet för logikappen och Azure-funktionsapp vid distributionen och sparar inställningarna. Kontrollera att du använder ett globalt unikt namn för din funktionsapp.

   ![Ange namn för din logikapp och en funktionsapp](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   När Visual Studio startar distributionen till den angivna resursgruppen lösningens distributionens status visas i Visual Studio **utdata** fönster. 
   När distributionen är klar är din logikapp live i Azure-portalen.

## <a name="edit-logic-app-in-visual-studio"></a>Redigera logic app i Visual Studio

Nu när lösningen har distribuerats i resursgruppen, måste du öppna din logikapp med Logic App Designer så att du kan redigera och ändra din logikapp.

1. I Solution Explorer från den `azuredeploy.json` filens snabbmenyn och välj **öppna med Logic App Designer**.

   ![Öppna ”azuredeploy.json” i Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. Efter den **egenskaper för Logikapp** visas rutan och om det inte redan är valt under **prenumeration**, Välj din Azure-prenumeration. Under **resursgrupp**, Välj den resursgrupp och plats där du har distribuerat din lösning och välj sedan **OK**.

   ![Egenskaper för logikapp](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   När Logic App Designer öppnas och kan du fortsätta att lägga till steg eller ändra arbetsflödet och spara dina uppdateringar.

   ![Öppna logikappen i Logic App Designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Skapa Azure Functions-projekt

Att skapa din Functions-projekt och funktionen med JavaScript, Python, F#, PowerShell, Batch eller Bash, följer du stegen i artikeln [arbeta med Azure Functions Core Tools](../azure-functions/functions-run-local.md). För att utveckla dina Azure-funktion med C# i lösningen kan du använda en C#-klassbibliotek genom att följa stegen i artikeln [publicera en .NET-klassbiblioteket som Funktionsapp](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Distribuera funktioner från Visual Studio

Din Distributionsmall distribuerar alla Azure-funktioner som du har i din lösning från Git-lagringsplats som anges av variabler i den `azuredeploy.json` filen. Om du skapar och redigerar din Functions-projekt i din lösning du kan kontrollera projektet till Git-källkontroll, till exempel GitHub eller Azure DevOps och sedan uppdatera den `repo` variabel så att mallen distribuerar din Azure-funktion.

## <a name="manage-logic-apps-and-view-run-history"></a>Hantera logic apps och visa körningshistorik

För logic apps som redan har distribuerats i Azure kan du fortfarande redigera, hantera, visa körningshistorik och inaktivera dessa appar från Visual Studio.

1. Från den **visa** menyn i Visual Studio, öppna **Cloud Explorer**.

1. Under **alla prenumerationer**, ange den prenumeration som är associerade med logikappar som du vill hantera och välj **tillämpa**.

1. Under **Logikappar**, Välj din logikapp. Appens snabbmenyn och välj **öppna med Logic App Editor**.

Du kan nu hämta redan publicerat logikappen till ditt resursgrupp-projekt. Så även om du kanske har startat en logikapp i Azure-portalen kan kan du fortfarande importera och hantera appen i Visual Studio. Mer information finns i [hantera logic apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera Logic Apps i Visual Studio](manage-logic-apps-with-visual-studio.md)
