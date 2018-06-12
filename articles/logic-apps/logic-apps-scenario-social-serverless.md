---
title: Serverlösa scenario – skapa en kund insikter instrumentpanel med Azure | Microsoft Docs
description: Lär dig hur du kan hantera kundfeedback, sociala media data och mycket mer genom att skapa en kund-instrumentpanel med Azure Logikappar och Azure Functions
keywords: ''
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jehollan; LADocs
ms.openlocfilehash: 3ee3ec3107cf8aad834e8201405c9aa833d838af
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299968"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Skapa en strömmande kunden insikter instrumentpanel med Azure Logikappar och Azure Functions

Azure erbjuder serverlösa verktyg som hjälper dig snabbt bygga och värden appar i molnet, utan att tänka på infrastrukturen. I den här kursen kan du skapa en instrumentpanel som utlösare på kundfeedback, analyserar feedback med machine learning och publicerar insikter till en källa, till exempel Power BI eller Azure Data Lake.

För den här lösningen använder dessa Azure nyckelkomponenterna för serverlösa appar: [Azure Functions](https://azure.microsoft.com/services/functions/) och [Azure Logikappar](https://azure.microsoft.com/services/logic-apps/).
Med Azure Logikappar tillhandahåller en serverlösa arbetsflödesmotorn i molnet så att du kan skapa orkestreringarna för serverlösa komponenter och ansluta till 200 + tjänster och API: er. Azure Functions erbjuder serverlösa databehandling i molnet. Den här lösningen använder Azure Functions för flagga kunden tweets baserat på fördefinierade nyckelord.

I det här scenariot skapar du en logikapp som utlöser på att hitta feedback från kunder. Vissa kopplingar att hjälp du svara på kundfeedback inkluderar Outlook.com, Office 365, undersökning apa, Twitter, och en [HTTP-begäran från ett webbformulär](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Arbetsflödet som du skapar övervakar en hashtaggar på Twitter.

Du kan [skapa hela lösningen i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) och [distribuera lösningen med Azure Resource Manager-mall](../logic-apps/logic-apps-create-deploy-template.md). En video genomgång som visar hur du skapar den här lösningen [det här videoklippet Channel 9](http://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Initierar den kundinformation

1. Skapa en tom logikapp i Azure-portalen eller Visual Studio. 

   Om du har använt logikappar granskar den [Snabbstartsguide för Azure-portalen](../logic-apps/quickstart-create-first-logic-app-workflow.md) eller [Snabbstart för Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. Sök i logik App Designer och lägga till Twitter-utlösare som har den här åtgärden: **när en ny tweet skickas**

3. Ställ in utlösaren att lyssna efter tweets baserat på ett nyckelord eller en hashtaggar.

   Egenskapen upprepning avgör hur ofta logikappen söker efter nya objekt på avsökning-utlösare, till exempel Twitter-utlösare.

   ![Exempel på Twitter-utlösare][1]

Den här logikapp utlöses nu på alla nya tweets. Du kan sedan ta och analysera tweet data så att du bättre kan förstå våra uttryckt. 

## <a name="analyze-tweet-text"></a>Analysera tweet text

Du kan använda för att identifiera sentiment bakom text [Azure kognitiva Services](https://azure.microsoft.com/services/cognitive-services/).

1. I logik App Designer utlösaren, Välj under **nytt steg**.

2. Hitta de **textanalys** koppling.

3. Välj den **identifiera Sentiment** åtgärd.

4. Om du uppmanas ange en giltig nyckel kognitiva tjänster för Text Analytics-tjänsten.

5. Under **brödtext i begäran**, Välj den **Tweet Text** som anger tweet texten som indata för analys.

När du har fått tweet data och insikter om tweet kan nu du använda flera andra relevanta kopplingar och åtgärder:

* **Power BI - lägga till rader i Dataset strömning**: Visa inkommande tweets på en Power BI-instrumentpanel.
* **Azure Data Lake - tilläggsfilen**: lägga till kundinformation till en Azure Data Lake-dataset för att inkludera i analytics-jobb.
* **SQL - lägga till rader**: lagra data i en databas för senare hämtning.
* **Slack - skicka meddelande**: meddela en Slack-kanal om negativ feedback som kan kräva åtgärder.

Du kan också skapa och en Azure fungera så att du kan utföra anpassad bearbetning på dina data. 

## <a name="process-data-with-azure-functions"></a>Bearbeta data med Azure Functions

Innan du skapar en funktion kan du skapa en funktionsapp i din Azure-prenumeration. Dessutom för din logikapp att anropa en funktion direkt funktionen måste ha en HTTP utlösa bindning, till exempel använder den **HttpTrigger** mall. Läs [hur du skapar din första funktionsapp och funktionen i Azure portal](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Använda tweet texten i det här scenariot som begärandetexten för din Azure-funktion. Definiera den logik som avgör om tweet texten innehåller ett nyckelord eller en fras i funktionskoden. Behåll funktionen som enkla eller avancerade som behövs för scenariot.
I slutet av funktionen returnera ett svar till logikappen med vissa data, till exempel, en enkel booleskt värde som `containsKeyword` eller ett komplext objekt.

> [!TIP]
> Om du vill komma åt ett komplext svar från en funktion i en logikapp, använder den **parsa JSON** åtgärd.

När du är klar kan du spara funktionen och sedan lägga till funktionen som en åtgärd i logikappen som du utvecklar.

## <a name="add-azure-function-to-logic-app"></a>Lägg till Azure-funktionen i logikapp

1. I logik App Designer under den **identifiera Sentiment** åtgärd, Välj **nytt steg**.

2. Hitta de **Azure Functions** koppling och välj sedan den funktion som du skapade.

3. Under **brödtext i begäran**väljer **Tweet Text**.

![Steg för konfigurerade Azure-funktion][2]

## <a name="run-and-monitor-your-logic-app"></a>Köra och övervaka din logikapp

Du kan använda de omfattande felsökning och övervakningsfunktionerna med Azure Logikappar i Azure-portalen, Visual Studio eller via Azure REST API: er och SDK om du vill granska alla aktuella eller tidigare körs för din logikapp.

Om du vill testa enkelt din logikapp i logik App Designer, Välj **köra utlösaren**. Utlösaren avsöker för tweets baserat på angivna schemat tills en tweet som uppfyller dina kriterier hittas. När du kör fortlöper visar designern en live-vyn för att köras.

För att visa föregående kör historik i Visual Studio eller Azure-portalen: 

* Öppna Visual Studio Cloud Explorer. Hitta din logikapp, öppna appens snabbmenyn. Välj **öppna kör historik**.

* Hitta logikappen i Azure-portalen. Välj på menyn din logikapp **översikt**. 

## <a name="create-automated-deployment-templates"></a>Skapa mallar för automatisk distribution

När du har skapat en logik app lösning du kan avbilda och distribuera appen som en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-overview.md#template-deployment) till någon Azure-region i världen. Du kan använda den här funktionen för både för att ändra parametrar för att skapa olika versioner av appen och integrera din lösning i en version och utgåva pipeline. Du kan även inkludera Azure Functions i mallen för distribution så att du kan hantera hela lösningen med alla beroenden som en mall. Läs [hur du skapar logiken mallar för distribution av appen](../logic-apps/logic-apps-create-deploy-template.md).

En exempel-Distributionsmall med en Azure-funktion, kontrollera den [Azure quickstart mallen databasen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Nästa steg

* [Hitta andra exempel och scenarier för Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png