---
title: Instrumentpanelen Skapa kundinsikter
description: Hantera kundfeedback, data i sociala medier med mera genom att skapa en kundinstrumentpanel med Azure Logic Apps och Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: e300bf9c9aa0acf0bed6426eb73f690f9a38bd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980432"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Skapa en instrumentpanel för kundinsikter för direktuppspelning med Azure Logic Apps och Azure Functions

Azure erbjuder [serverlösa](https://azure.microsoft.com/solutions/serverless/) verktyg som hjälper dig att snabbt skapa och vara värd för appar i molnet, utan att behöva tänka på infrastruktur. I den här självstudien kan du skapa en instrumentpanel som utlöser feedback från kunder, analyserar feedback med maskininlärning och publicerar insikter till en källa, till exempel Power BI eller Azure Data Lake.

För den här lösningen använder du dessa viktiga Azure-komponenter för serverlösa appar: [Azure Functions](https://azure.microsoft.com/services/functions/) och Azure [Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps tillhandahåller en serverlös arbetsflödesmotor i molnet så att du kan skapa orkestreringar över serverlösa komponenter och ansluta till över 200 tjänster och API:er. Azure Functions tillhandahåller serverlös databehandling i molnet. Den här lösningen använder Azure Functions för att flagga kundweets baserat på fördefinierade nyckelord.

I det här fallet skapar du en logikapp som utlöser när du hittar feedback från kunder. Vissa kopplingar som hjälper dig att svara på feedback från kunder är Outlook.com, Office 365, Survey Monkey, Twitter och en [HTTP-begäran från ett webbformulär](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Arbetsflödet som du skapar övervakar en hashtagg på Twitter.

Du kan [skapa hela lösningen i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) och distribuera lösningen med Azure Resource [Manager-mallen](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). En videogenomgång som visar hur du skapar den här lösningen [finns i den här channel 9-videon](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Utlösare på kunddata

1. Skapa en tom logikapp i Azure-portalen eller Visual Studio. 

   Om du inte har tidigare i logikappar kan du läsa [snabbstarten för Azure-portalen](../logic-apps/quickstart-create-first-logic-app-workflow.md) eller [snabbstarten för Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. I Logic App Designer, hitta och lägga till Twitter utlösare som har denna åtgärd: **När en ny tweet publiceras**

3. Ställ in utlösaren för att lyssna efter tweets baserat på ett nyckelord eller en hashtagg.

   På avsökningsbaserade utlösare, till exempel Twitter-utlösaren, avgör egenskapen återkommande hur ofta logikappen söker efter nya objekt.

   ![Exempel på Twitter-utlösare][1]

Denna logik app bränder nu på alla nya tweets. Du kan sedan ta och analysera tweet data så att du bättre kan förstå de känslor som uttrycks. 

## <a name="analyze-tweet-text"></a>Analysera tweettext

Om du vill identifiera sentimentet bakom viss text kan du använda [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. Välj **Nytt steg**under utlösaren i Logic App Designer.

2. Leta reda på **textanalyskopplingen.**

3. Välj åtgärden **Identifiera sentiment.**

4. Om du uppmanas till det anger du en giltig Cognitive Services-nyckel för textanalystjänsten.

5. Under **Begär brödtext**väljer du fältet **Tweet Text,** som tillhandahåller tweettexten som indata för analys.

När du har fått tweet data och insikter om tweet, kan du nu använda flera andra relevanta kontakter och deras åtgärder:

* **Power BI - Lägg till rader i strömmande datauppsättning:** Visa inkommande tweets på en Power BI-instrumentpanel.
* **Azure Data Lake - Lägg till fil:** Lägg till kunddata i en Azure Data Lake-datauppsättning som ska inkluderas i analysjobb.
* **SQL - Lägg till rader**: Lagra data i en databas för senare hämtning.
* **Slack - Skicka meddelande**: Meddela en Slack-kanal om negativ feedback som kan kräva åtgärd.

Du kan också skapa och en Azure-funktion så att du kan utföra anpassad bearbetning på dina data. 

## <a name="process-data-with-azure-functions"></a>Bearbeta data med Azure-funktioner

Innan du skapar en funktion skapar du en funktionsapp i din Azure-prenumeration. För att logikappen ska kunna anropa en funktion direkt måste funktionen ha en HTTP-utlösare som binder, till exempel använda **httptriggermallen.** Lär dig hur du [skapar din första funktionsapp och funktion i Azure-portalen](../azure-functions/functions-create-first-azure-function-azure-portal.md).

I det här scenariot använder du tweettexten som förfrådetext för din Azure-funktion. I funktionskoden definierar du logiken som avgör om tweettexten innehåller ett nyckelord eller en fras. Håll funktionen så enkel eller komplex som behövs för scenariot.
I slutet av funktionen returnerar du ett svar till logikappen med vissa data, till exempel ett enkelt booleskt värde, till exempel `containsKeyword` eller ett komplext objekt.

> [!TIP]
> Om du vill komma åt ett komplext svar från en funktion i en logikapp använder du åtgärden **Parse JSON.**

När du är klar sparar du funktionen och lägger sedan till funktionen som en åtgärd i logikappen som du skapar.

## <a name="add-azure-function-to-logic-app"></a>Lägga till Azure-funktion i logikapp

1. Välj **Nytt steg**under åtgärden **Identifiera sentiment** i Logic App Designer.

2. Leta reda på **Azure Functions-kopplingen** och välj sedan den funktion som du skapade.

3. Under **Begärantext**väljer du **Tweet Text**.

![Konfigurerat Azure-funktionssteg][2]

## <a name="run-and-monitor-your-logic-app"></a>Köra och övervaka logikappen

Om du vill granska aktuella eller tidigare körningar för logikappen kan du använda de omfattande felsöknings- och övervakningsfunktionerna som Azure Logic Apps tillhandahåller i Azure-portalen, Visual Studio eller via Azure REST-API:er och SDK:er.

Om du enkelt vill testa logikappen väljer du **Kör utlösare**i Logic App Designer . Utlösaravsökningen för tweets baserat på ditt angivna schema tills en tweet som uppfyller dina kriterier hittas. Medan körningen fortskrider visar designern en livevisning för den körningen.

Så här visar du tidigare körningshistorik i Visual Studio eller Azure-portalen: 

* Öppna Visual Studio Cloud Explorer. Hitta logikappen, öppna appens snabbmeny. Välj **Öppna körhistorik**.

  > [!TIP]
  > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

* Hitta logikappen i Azure-portalen. På logikappens meny väljer du **Översikt**. 

## <a name="create-automated-deployment-templates"></a>Skapa mallar för automatisk distribution

När du har skapat en logikapplösning kan du samla in och distribuera din app som en [Azure Resource Manager-mall](../azure-resource-manager/templates/overview.md) till alla Azure-regioner i världen. Du kan använda den här funktionen både för att ändra parametrar för att skapa olika versioner av din app och för att integrera din lösning i Azure Pipelines. Du kan också inkludera Azure Functions i distributionsmallen så att du kan hantera hela lösningen med alla beroenden som en enda mall. Lär dig hur du [automatiserar distribution av logikappar](logic-apps-azure-resource-manager-templates-overview.md).

En exempeldistributionsmall med en Azure-funktion finns i [Azure-snabbstartsmallens databas](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Nästa steg

* [Hitta andra exempel och scenarier för Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
