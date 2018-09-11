---
title: Serverlös scenario – skapa customer insights instrumentpanel med Azure-tjänster | Microsoft Docs
description: Hantera feedback från kunder, sociala data med mera genom att skapa en kund-instrumentpanel med Azure Logic Apps och Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 6ba274bb3ff3679b4a44950db168215c54f3ade6
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299754"
---
# <a name="create-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Skapa strömmande customer insights instrumentpanel med Azure Logic Apps och Azure Functions

Azure erbjuder serverfria verktyg som hjälper dig att snabbt skapa och kör appar i molnet, utan att behöva tänka på infrastrukturen. I den här självstudien kan du skapa en instrumentpanel som utlöser på feedback från kunder, analyserar feedback med machine learning och publicerar insikter till en källa, till exempel Power BI eller Azure Data Lake.

För den här lösningen kan du använda dessa viktiga Azure-komponenter för appar utan server: [Azure Functions](https://azure.microsoft.com/services/functions/) och [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps ger en serverlös motor i molnet så att du kan skapa orkestreringar över serverlös komponenter och ansluta till över 200 tjänster och API: er. Azure Functions erbjuder serverlös databehandling i molnet. Den här lösningen använder Azure Functions för flagga kunden tweets som baseras på fördefinierade nyckelord.

I det här scenariot skapar du en logikapp som utlöser på att söka efter feedback från kunder. Vissa anslutningar att hjälp du svara på feedback från kunder inkluderar Outlook.com, Office 365, undersökningen apa, Twitter, och en [HTTP-begäran från ett webbformulär](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Arbetsflödet som du skapar övervakar en hashtag på Twitter.

Du kan [skapa hela lösningen i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) och [distribuera lösningen med Azure Resource Manager-mall](../logic-apps/logic-apps-create-deploy-template.md). En video genomgång som visar hur du skapar den här lösningen [se den här videon på Channel 9](http://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Utlös för kunddata

1. Skapa en tom logikapp i Azure portal eller Visual Studio. 

   Om du är nybörjare till logic apps, granska de [Snabbstart för Azure-portalen](../logic-apps/quickstart-create-first-logic-app-workflow.md) eller [Snabbstart för Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. I Logic App Designer, hitta och Lägg till Twitter-utlösare som har den här åtgärden: **när en ny tweet publiceras**

3. Konfigurera utlösaren att lyssna efter tweets som baseras på ett nyckelord eller en hashtagg.

   På avsökningen-baserade utlösare, till exempel Twitter-utlösare Anger upprepning-egenskapen hur ofta logikappen ska söka efter nya objekt.

   ![Exempel på Twitter-utlösare][1]

Den här logikappen utlöses nu på alla nya tweets. Du kan sedan ta och analysera tweet-data så att du bättre kan förstå sentiment uttryckt. 

## <a name="analyze-tweet-text"></a>Analysera tweet-text

Du kan använda för att identifiera sentimentet bakom text [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. I Logic App Designer under utlösaren väljer **nytt steg**.

2. Hitta den **textanalys** connector.

3. Välj den **identifiera Sentiment** åtgärd.

4. Om du uppmanas, ange en giltig Cognitive Services-nyckel för Text Analytics-tjänsten.

5. Under **Begärandetext**väljer den **Tweettext** fält som ger tweet-text som indata för analys.

Du kan nu använda flera andra relevanta anslutningsappar och åtgärder när du har hämtat tweet data och insikter om tweeten:

* **Power BI – lägga till rader till strömmande datauppsättning**: Visa inkommande tweets på en Power BI-instrumentpanel.
* **Azure Data Lake - Lägg till filen**: Lägg till kunddata till en Azure Data Lake-datamängd som ska ingå i analytics-jobb.
* **SQL – lägga till rader**: Store data i en databas för senare hämtning.
* **Slack - skicka meddelande**: meddela en Slack-kanal om negativ feedback som kan kräva åtgärder.

Du kan också skapa och en Azure fungerar så att du kan utföra anpassad bearbetning på dina data. 

## <a name="process-data-with-azure-functions"></a>Bearbeta data med Azure Functions

Innan du skapar en funktion måste du skapa en funktionsapp i Azure-prenumerationen. För din logikapp direkt anropar en funktion måste också funktionen ha HTTP utlösa-bindningen, till exempel ska du använda den **HttpTrigger** mall. Lär dig [hur du skapar din första funktionsapp och funktion i Azure-portalen](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Det här scenariot använder du tweet-text som begärandetexten för din Azure-funktion. Definiera den logik som avgör huruvida tweet-texten innehåller ett nyckelord eller en fras i funktionskoden. Behåll funktionen som enkla eller komplexa som krävs för scenariot.
I slutet av funktionen returnerar ett svar till logikappen med vissa data, till exempel, en enkel booleskt värde som `containsKeyword` eller ett komplext objekt.

> [!TIP]
> Du kommer åt ett komplext svar från en funktion i en logikapp genom att använda den **parsa JSON** åtgärd.

När du är klar sparar funktionen och Lägg till funktion som en åtgärd i logikappen som du skapar.

## <a name="add-azure-function-to-logic-app"></a>Lägg till Azure-funktion i logic app

1. I Logic App Designer under den **identifiera Sentiment** åtgärd, Välj **nytt steg**.

2. Hitta den **Azure Functions** anslutningen och välj sedan den funktion som du skapade.

3. Under **Begärandetext**väljer **Tweettext**.

![Konfigurerade Azure Function-steg][2]

## <a name="run-and-monitor-your-logic-app"></a>Köra och övervaka din logikapp

Du kan använda de omfattande felsökning och övervakning av funktioner som Azure Logic Apps tillhandahåller i Azure portal, Visual Studio eller via Azure REST API: er och SDK: er för att granska alla aktuella eller tidigare körningar för din logikapp.

För att enkelt testa din logikapp i Logic App Designer, Välj **kör utlösaren**. Utlösaren söker efter tweets som baseras på ditt angivna schema tills en tweet som uppfyller dina kriterier hittas. När du kör utvecklas visar designverktyget en livevy över körningens.

Till föregående vy i körningshistorik i Visual Studio eller Azure-portalen: 

* Öppna Visual Studio Cloud Explorer. Hitta din logikapp, öppna appens snabbmenyn. Välj **öppen körningshistorik**.

* Hitta din logikapp i Azure-portalen. På menyn för din logikapp, Välj **översikt**. 

## <a name="create-automated-deployment-templates"></a>Skapa mallar för automatisk distribution

När du har skapat en lösning för logic app kan du samla in och distribuera din app som en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-overview.md#template-deployment) till alla Azure-regioner i världen. Du kan använda den här funktionen båda för att ändra parametrar för att skapa olika versioner av din app och för att integrera din lösning i Azure-Pipelines. Du kan även inkludera Azure Functions i dina Distributionsmall så att du kan hantera hela lösningen med alla beroenden som en enda mall. Lär dig [hur du skapar logic app-distributionsmallar](../logic-apps/logic-apps-create-deploy-template.md).

En exempel-distributionsmallen med en Azure-funktion, kontrollera den [Snabbstart för Azure-mallagret](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Nästa steg

* [Hitta andra exempel och scenarier för Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png