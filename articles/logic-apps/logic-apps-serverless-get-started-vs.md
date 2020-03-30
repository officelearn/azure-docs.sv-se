---
title: Skapa den första serverlösa appen i Visual Studio
description: Skapa, distribuera och hantera en serverlös app med hjälp av Azure Logic Apps och Azure Functions i Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981152"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Skapa din första serverlösa app med hjälp av Azure Logic Apps och Azure Functions i Visual Studio

Du kan snabbt utveckla och distribuera molnappar med hjälp av serverlösa verktyg och funktioner i Azure, till exempel [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Azure [Functions](../azure-functions/functions-overview.md). Den här artikeln visar hur du börjar skapa en serverlös app, som använder en logikapp som anropar en Azure-funktion, i Visual Studio. Mer information om serverlösa lösningar i Azure finns i [Azure Serverless med Funktioner och Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Krav

Om du vill skapa en serverlös app i Visual Studio behöver du:

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Följande verktyg. Om du inte redan har dem kan du hämta och installera dem.

  * [Visual Studio 2019, 2017 eller 2015 (Community eller annan utgåva)](https://aka.ms/download-visual-studio). 
  I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017 kontrollerar du att du väljer **Azure-utvecklingsarbetsbelastningen.**

  * [Microsoft Azure SDK för .NET (version 2.9.1 eller senare)](https://azure.microsoft.com/downloads/). 
  Läs mer om [Azure SDK för .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps Tools för den Visual Studio-version du vill ha:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Se till att starta om Visual Studio när installationen är klar.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) för lokalt felsökning av funktioner.

* Tillgång till webben när du använder den inbäddade Logic App Designer.

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp. 
  Om du t.ex. använder Dynamics CRM Online-anslutningen kontrollerar Designer om CRM-instansen har några standardegenskaper och anpassade egenskaper.

## <a name="create-a-resource-group-project"></a>Skapa ett resursgruppsprojekt

För att komma igång skapar du ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) för din serverlösa app. I Azure skapar du resurser inom en *resursgrupp*, vilket är en logisk samling som du använder för att organisera, hantera och distribuera resurser för en hel app som en enda tillgång. För en serverlös app i Azure innehåller resursgruppen resurser för både Azure Logic Apps och Azure Functions. Läs mer om [Azure-resursgrupper och resurser](../azure-resource-manager/management/overview.md).

1. Starta Visual Studio och logga in med ditt Azure-konto.

1. Gå till **Arkiv**-menyn och välj **Nytt** > **Projekt**.

   ![Skapa nytt projekt i Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Under **Installerad** väljer du **Visual C#** eller **Visual Basic**. Välj sedan **Cloud** > **Azure Resource Group**.

   > [!NOTE]
   > Om **projektet Cloud-** eller **Azure Resource Group** inte finns kontrollerar du att du har installerat Azure SDK för Visual Studio.

   Om du använder Visual Studio 2019 gör du så här:

   1. I rutan **Skapa ett nytt projekt** väljer du projektmallen Azure Resource **Group** för Visual C# eller Visual Basic och väljer sedan **Nästa**.

   1. Ange namn och annan projektinformation som du vill använda för Azure-resursgruppen. När du är klar väljer du **Skapa**.

1. Ge projektet ett namn och en plats och välj sedan **OK**.

   Visual Studio uppmanar dig att välja en mall i malllistan. 
   I det här exemplet används en Azure QuickStart-mall så att du kan skapa en serverlös app som innehåller en logikapp och ett anrop till en Azure-funktion.

   > [!TIP]
   > I scenarier där du inte vill fördevisa din lösning till en Azure-resursgrupp kan du använda den tomma **Logic App-mallen,** som bara skapar en tom logikapp.

1. Välj **Azure QuickStart (github.com/Azure/azure-quickstart-templates)** i listan **Visa mallar från den här plats.**

1. I sökrutan anger du "logic-app" som filter. Välj mallen **101-logic-app-and-function-app** från resultatet.

   ![Välj Azure QuickStart-mall](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio skapar och öppnar en lösning för ditt resursgruppsprojekt. 
   Azure QuickStart-mallen som du valde skapar en distributionsmall med namnet azuredeploy.json i resursgruppprojektet. Den här distributionsmallen innehåller definitionen för en enkel logikapp som utlöses av en HTTP-begäran, anropar en Azure-funktion och returnerar resultatet som ett HTTP-svar.

   ![Ny serverlös lösning](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Distribuera sedan din lösning till Azure. Du måste göra detta innan du kan öppna distributionsmallen och granska resurserna för din serverlösa app.

## <a name="deploy-your-solution"></a>Distribuera din lösning

Innan du kan öppna logikappen i Logic App Designer i Visual Studio måste du ha en Azure-resursgrupp som redan har distribuerats i Azure. Designern kan sedan skapa anslutningar till resurser och tjänster i logikappen. För den här uppgiften följer du dessa steg för att distribuera din lösning från Visual Studio till Azure-portalen:

1. Välj **Distribuera** > **ny**på snabbmenyn i Solution Explorer.

   ![Skapa ny distribution för resursgrupp](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Om de inte redan är markerade väljer du din Azure-prenumeration och den resursgrupp som du vill distribuera till. Välj sedan **Distribuera**.

   ![Distributionsinställningar](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Om rutan **Redigera parametrar** visas anger du de resursnamn som ska användas för logikappen och din Azure-funktionsapp vid distributionen och sparar sedan dina inställningar. Se till att du använder ett globalt unikt namn för din funktionsapp.

   ![Ange namn för logikappen och funktionsappen](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   När Visual Studio startar distributionen till den angivna resursgruppen visas lösningens distributionsstatus i fönstret Visual **Studio-utdata.** 
   När distributionen är klar finns logikappen live i Azure-portalen.

## <a name="edit-your-logic-app-in-visual-studio"></a>Redigera logikappen i Visual Studio

Om du vill redigera logikappen efter distributionen öppnar du logikappen med hjälp av Logic App Designer i Visual Studio.

1. Välj **Öppna med Logic App Designer**på snabbmenyn i filen azuredeploy.json i Solution Explorer .

   ![Öppna azuredeploy.json i Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

1. När rutan **Egenskaper för Logikapp** visas väljer du din Azure-prenumeration under **Prenumeration**om den inte redan är markerad. Under **Resursgrupp**väljer du den resursgrupp och plats där du distribuerade lösningen och väljer sedan **OK**.

   ![Egenskaper för logikapp](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   När Logic App Designer har öppnats kan du fortsätta att lägga till steg eller ändra arbetsflödet och spara uppdateringarna.

   ![Öppnad logikapp i Logic App Designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Skapa ditt Azure Functions-projekt

Om du vill skapa projekt och funktion funktioner med JavaScript, Python, F#, PowerShell, Batch eller Bash följer du stegen i [Arbeta med Azure Functions Core Tools](../azure-functions/functions-run-local.md). Om du vill utveckla din Azure-funktion med hjälp av C# i lösningen använder du ett C#-klassbibliotek genom att följa stegen i [Publicera ett .NET-klassbibliotek som en funktionsapp](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Distribuera funktioner från Visual Studio

Distributionsmallen distribuerar alla Azure-funktioner som du har i din lösning från Git-repoen som anges av variabler i filen azuredeploy.json. Om du skapar och skapar ditt Functions-projekt i din lösning kan du kontrollera projektet i Git-källkontrollen (till exempel GitHub eller Azure DevOps) och sedan uppdatera variabeln `repo` så att mallen distribuerar din Azure-funktion.

## <a name="manage-logic-apps-and-view-run-history"></a>Hantera logikappar och visa körningshistorik

För logikappar som redan distribuerats i Azure kan du fortfarande redigera, hantera, visa körningshistorik för och inaktivera dessa appar från Visual Studio.

1. Öppna **Cloud Explorer**på **Visa-menyn** i Visual Studio .

1. Under **Alla prenumerationer**väljer du den Azure-prenumeration som är associerad med de logikappar som du vill hantera och väljer sedan **Använd**.

1. Välj logikapp under **Logic Apps.** På appens snabbmeny väljer du **Öppna med Logic App Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

Nu kan du hämta den redan publicerade logikappen till resursgruppsprojektet. Så även om du kan ha startat en logikapp i Azure-portalen kan du fortfarande importera och hantera appen i Visual Studio. Mer information finns i [Hantera logikappar med Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera Logic Apps i Visual Studio](manage-logic-apps-with-visual-studio.md)
