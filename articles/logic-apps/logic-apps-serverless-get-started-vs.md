---
title: Skapa appar utan server med hjälp av Azure Logic Apps och Azure Functions i Visual Studio
description: Skapa, distribuera och hantera din första app utan server med hjälp av Azure Logic Apps och Azure Functions i Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444876"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Skapa din första app utan server med Azure Logic Apps och Azure Functions i Visual Studio

Du kan snabbt utveckla och distribuera appar i molnet genom att använda de serverfria verktygen och funktionerna i Azure, som [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Azure Functions](../azure-functions/functions-overview.md). Den här artikeln visar hur du börja skapa en app som använder en logikapp som anropar en Azure-funktion i Visual Studio utan server. Läs mer om serverlösa lösningar i Azure i [Azure utan server med Functions och Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en app utan server i Visual Studio, behöver du:

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Följande verktyg. Om du inte redan har dem, hämtas och installeras.

  * [Visual Studio 2019 2017 eller 2015 (Community eller andra edition)](https://aka.ms/download-visual-studio). 
  I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017, kontrollera att du väljer den **Azure development** arbetsbelastning.

  * [Microsoft Azure SDK för .NET (version 2.9.1 eller senare)](https://azure.microsoft.com/downloads/). 
  Läs mer om [Azure SDK för .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps Tools för Visual Studio-version som du vill använda:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Se till att starta om Visual Studio när installationen är klar.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) för lokalt felsökning funktioner.

* Åtkomst till Internet när du använder den inbäddade Logic App Designer.

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp. 
  Om du t.ex. använder Dynamics CRM Online-anslutningen kontrollerar Designer om CRM-instansen har några standardegenskaper och anpassade egenskaper.

## <a name="create-a-resource-group-project"></a>Skapa en resursgrupp-projekt

Kom igång genom att skapa en [Azure-resursgruppsprojekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) för din app utan server. I Azure måste du skapa resurser i en *resursgrupp*, vilket är en logisk samling som du använder för att ordna, hantera och distribuera resurser för en hel app som ett enskilt objekt. För en app utan server i Azure innehåller resursgruppen resurser för både Azure Logic Apps och Azure Functions. Läs mer om [Azure-resursgrupper och resurser](../azure-resource-manager/resource-group-overview.md).

1. Starta Visual Studio och logga in med ditt Azure-konto.

1. Gå till **Arkiv**-menyn och välj **Nytt** > **Projekt**.

   ![Skapa nytt projekt i Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Under **Installerad** väljer du **Visual C#** eller **Visual Basic**. Välj **molnet** > **Azure-resursgrupp**.

   > [!NOTE]
   > Om den **molnet** kategori eller **Azure-resursgrupp** projektet inte finns, se till att du har installerat Azure SDK för Visual Studio.

   Följ dessa steg om du använder Visual Studio 2019:

   1. I den **skapa ett nytt projekt** väljer den **Azure-resursgrupp** projektmall för att antingen Visual C# eller Visual Basic och välj sedan **nästa**.

   1. Ange namnet och andra projektinformation som du vill använda för Azure-resursgruppen. När du är klar väljer du **Skapa**.

1. Ge projektet ett namn och en plats och välj sedan **OK**.

   Visual Studio uppmanas du att välja en mall från listan över mallar. 
   Det här exemplet används en Azure-snabbstartsmall så att du kan skapa en app som innehåller en logikapp och ett anrop till en Azure-funktion utan server.

   > [!TIP]
   > I scenarier där du inte vill fördistribuera din lösning till en Azure-resursgrupp, kan du använda du tom **Logikapp** mall som bara skapar en tom logikapp.

1. Från den **Visa mallar från den här platsen** väljer **Azure QuickStart (github.com/Azure/azure-quickstart-templates)** .

1. I sökrutan anger du ”logikapp” som filter. Från resultatet väljer den **101-logic-app-and-function-app** mall.

   ![Välj Azure-snabbstartsmall](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio skapar och öppnar en lösning för ditt resursgrupp-projekt. 
   Azure QuickStart-mallen som du har valt skapar en Distributionsmall med namnet azuredeploy.json i resursgruppsprojektet. Den här distributionsmallen innehåller definitionen för en enkel logikapp som utlöses av en HTTP-begäran, anropar en Azure-funktion och returnerar resultatet som ett HTTP-svar.

   ![Ny serverlös lösning](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Distribuera din lösning till Azure. Du måste göra detta innan du kan öppna distributionsmallen för och granska resurser för din app utan server.

## <a name="deploy-your-solution"></a>Distribuera din lösning

Innan du kan öppna logikappen i Logic App Designer i Visual Studio, måste du ha en Azure-resursgrupp som redan har distribuerats i Azure. Designern kan sedan skapa anslutningar till resurser och tjänster i din logikapp. Följ stegen nedan för att distribuera din lösning från Visual Studio till Azure-portalen för den här aktiviteten:

1. I Solution Explorer från snabbmenyn för projektet resursen och välj **distribuera** > **New**.

   ![Skapa ny distribution för resursgrupp](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Om de inte redan är markerat, Välj din Azure-prenumeration och resursgrupp som du vill distribuera. Välj **distribuera**.

   ![Distributionsinställningar](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Om den **redigera parametrar** visas rutan Ange resursnamn ska användas för din logikapp och Azure-funktionsapp vid distributionen och sparar inställningarna. Kontrollera att du använder ett globalt unikt namn för din funktionsapp.

   ![Ange namn för din logikapp och en funktionsapp](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   När Visual Studio startar distributionen till den angivna resursgruppen lösningens distributionens status visas i Visual Studio **utdata** fönster. 
   När distributionen är klar är din logikapp live i Azure-portalen.

## <a name="edit-your-logic-app-in-visual-studio"></a>Redigera din logikapp i Visual Studio

Öppna din logikapp med hjälp av Logic App Designer i Visual Studio om du vill redigera din logikapp efter distributionen.

1. Från snabbmenyn för filen azuredeploy.JSON i Solution Explorer väljer **öppna med Logic App Designer**.

   ![Öppna azuredeploy.json i Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kan du kontrollera att du har de senaste uppdateringarna för Visual Studio.

1. Efter den **egenskaper för Logikapp** visas under **prenumeration**, Välj din Azure-prenumeration om det inte redan är valt. Under **resursgrupp**, Välj den resursgrupp och plats där du har distribuerat din lösning och välj sedan **OK**.

   ![Egenskaper för logikapp](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   När Logic App Designer öppnas och kan du fortsätta att lägga till steg eller ändra arbetsflödet och spara dina uppdateringar.

   ![Öppna logikappen i Logic App Designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Skapa Azure Functions-projekt

Skapa din Functions-projekt och funktionen med hjälp av JavaScript, Python, F#, PowerShell, Batch eller Bash, följer du stegen i [arbeta med Azure Functions Core Tools](../azure-functions/functions-run-local.md). Att utveckla dina Azure-funktion med C# i din lösning, använda en C# klassbibliotek genom att följa stegen i [publicera en .NET-klassbiblioteket som Funktionsapp](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Distribuera funktioner från Visual Studio

Din Distributionsmall distribuerar alla Azure-funktioner som du har i din lösning från Git-lagringsplats som anges av variabler i filen azuredeploy.JSON. Om du skapar och redigerar din Functions-projekt i din lösning, du kan kontrollera projektet i Git-källkontrollen (t.ex, GitHub eller Azure DevOps) och sedan uppdatera den `repo` variabel så att mallen distribuerar din Azure-funktion.

## <a name="manage-logic-apps-and-view-run-history"></a>Hantera logic apps och visa körningshistorik

För logic apps som redan har distribuerats i Azure kan du fortfarande redigera, hantera, visa körningshistoriken för och inaktivera dessa appar från Visual Studio.

1. Från den **visa** menyn i Visual Studio, öppna **Cloud Explorer**.

1. Under **alla prenumerationer**, ange den prenumeration som är associerade med logikappar som du vill hantera och välj sedan **tillämpa**.

1. Under **Logikappar**, Välj din logikapp. Appens snabbmenyn och välj **öppna med Logic App Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kan du kontrollera att du har de senaste uppdateringarna för Visual Studio.

Du kan nu hämta redan publicerat logikappen till ditt resursgrupp-projekt. Så även om du kanske har startat en logikapp i Azure-portalen kan kan du fortfarande importera och hantera appen i Visual Studio. Mer information finns i [hantera logic apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera Logic Apps i Visual Studio](manage-logic-apps-with-visual-studio.md)
