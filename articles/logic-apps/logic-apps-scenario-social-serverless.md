---
title: "Scenario – skapa en kund insikter instrumentpanel med Azure serverlösa | Microsoft Docs"
description: "Ett exempel på hur du kan skapa en instrumentpanel om du vill hantera kundfeedback, sociala data och mycket mer med Azure Logikappar och Azure Functions."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
ms.openlocfilehash: 0b6e118cb13ab8185d8eeb42bec6147155967967
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Skapa en realtid kunden insikter instrumentpanel med Azure Logikappar och Azure Functions

Azure-serverlösa verktyg ger kraftfulla funktioner för att snabbt skapa och vara värd för program i molnet, utan att tänka på infrastrukturen.  I det här scenariot skapar vi en instrumentpanel om du vill utlösa på kundfeedback, analysera feedback med machine learning och publicera insikter en källa som Power BI eller Azure Data Lake.

## <a name="overview-of-the-scenario-and-tools-used"></a>Översikt över scenariot och verktyg som används

För att implementera den här lösningen använder vi två viktiga komponenter av serverlösa appar i Azure: [Azure Functions](https://azure.microsoft.com/services/functions/) och [Azure Logikappar](https://azure.microsoft.com/services/logic-apps/).

Logic Apps är en serverlösa arbetsflödesmotorn i molnet.  Den ger orchestration över serverlösa komponenter och även ansluter till fler än 100 tjänster och API: er.  I det här scenariot skapar vi en logikapp för att utlösa kommentarer från kunder.  Kopplingar som kan underlätta reagera på kundfeedback bland Outlook.com, Office 365, undersökning apa, Twitter och en HTTP-begäran [från ett webbformulär](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/).  Vi ska övervaka en hashtaggar på Twitter för arbetsflödet nedan.

Funktioner ger serverlösa beräkning i molnet.  I det här scenariot använder vi Azure Functions för att flaggan tweets från kunder baserat på ett antal fördefinierade nyckelord.

Hela lösningen kan vara [skapa i Visual Studio](logic-apps-deploy-from-vs.md) och [distribueras som en del av en resursmall för](logic-apps-create-deploy-template.md).  Det finns också videogenomgång av scenariot [på Channel 9](http://aka.ms/logicappsdemo).

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>Skapa logikapp för att utlösa på kundinformation

Efter [att skapa en logikapp](logic-apps-create-a-logic-app.md) i Visual Studio eller Azure-portalen:

1. Lägga till en utlösare för **på nya Tweets** från Twitter
2. Konfigurera utlösare för att lyssna på tweets på ett nyckelord eller en hashtaggar.

   > [!NOTE]
   > Egenskapen upprepning för utlösaren avgör hur ofta logikappen söker efter nya objekt på avsökning-baserade utlösare

   ![Exempel på Twitter-utlösare][1]

Den här appen används nu på alla nya tweets.  Vi kan sedan ta tweet data och lära dig mer sentiment uttryckt.  För den här vi använder den [Azure kognitiva Service](https://azure.microsoft.com/services/cognitive-services/) att identifiera sentiment text.

1. Klicka på **nytt steg**
1. Välj eller Sök efter den **textanalys** koppling
1. Välj den **identifiera Sentiment** igen
1. Om du uppmanas ange en giltig nyckel kognitiva tjänster för tjänsten textanalys
1. Lägg till den **Tweet Text** som text att analysera.

Nu när vi har tweet data och insikter om tweet kan ett antal andra kopplingar vara relevanta:
* Power BI - lägga till rader i Dataset strömning: Visa tweets realtid på en Power BI-instrumentpanel.
* Azure Data Lake - tilläggsfilen: lägga till kundinformation till en Azure Data Lake-dataset för att inkludera i analytics-jobb.
* SQL - lägga till rader: lagra data i en databas för senare hämtning.
* Slack - skicka meddelande: varning en slack-kanal på negativ feedback som kräver åtgärder.

En Azure-funktion kan även användas för att göra flera anpassade beräkningen ovanpå data.

## <a name="enriching-the-data-with-an-azure-function"></a>Utöka data med en Azure-funktion

Innan vi kan skapa en funktion som vi behöver ha en funktionsapp i vår Azure-prenumeration.  Information om hur du skapar en Azure-funktion i portalen kan [finns här](../azure-functions/functions-create-first-azure-function-azure-portal.md)

För en funktion som ska anropas direkt från en logikapp, den måste ha en HTTP utlösa bindning.  Vi rekommenderar att du använder den **HttpTrigger** mall.

Begärandetexten för funktionen Azure skulle vara tweet texten i det här scenariot.  I Funktionskoden definiera logik på om tweet texten innehåller ett nyckelord eller en fras.  Själva funktionen kunde hållas enligt enkla eller avancerade som behövs för scenariot.

Returnera ett svar till logikappen med vissa data i slutet av funktionen.  Detta kan bero på ett enkelt booleskt värde (t.ex. `containsKeyword`), eller ett komplext objekt.

![Steg för konfigurerade Azure-funktion][2]

> [!TIP]
> Vid åtkomst till ett komplext svar från en funktion i en logikapp instruktionen parsa JSON.

När funktionen har sparats går att lägga till logikappen skapade ovan.  I logikappen:

1. Klicka om du vill lägga till en **nytt steg**
1. Välj den **Azure Functions** koppling
1. Välj om du vill välja en befintlig funktion och bläddra till den funktion som har skapats
1. Skicka i den **Twittra Text** för den **begäran**

## <a name="running-and-monitoring-the-solution"></a>Köra och övervaka lösningen

En av fördelarna med redigering serverlösa orkestreringarna i Logic Apps är omfattande felsöknings- och övervakningsfunktionerna.  Alla kör (aktuella eller historiska) kan visas från Visual Studio och Azure-portalen eller via REST-API och SDK: er.

Ett av de enklaste sätten att testa en logikapp använder den **kör** knappen i designern.  Klicka på **kör** fortsätter att avsöka utlösaren 5 sekunder tills en händelse identifieras och ger en live-vyn som kör fortskrider.

Tidigare kör händelser kan visas i bladet översikt i Azure-portalen eller med hjälp av Visual Studio Cloud Explorer.

## <a name="creating-a-deployment-template-for-automated-deployments"></a>Skapa en Distributionsmall för automatiserad distribution

När en lösning har utvecklats, kan de hämtats och distribueras via en mall för Azure-distribution till alla Azure-region i världen.  Detta är användbart för båda ändra parametrarna för olika versioner av det här arbetsflödet, men också för att integrera den här lösningen i en version och utgåva pipeline.  Information om hur du skapar en Distributionsmall finns [i den här artikeln](logic-apps-create-deploy-template.md).

Azure Functions kan också införlivas i Distributionsmall - så att hela lösningen med alla beroenden kan hanteras som en mall.  Ett exempel på en mall för distribution av funktionen finns i den [Azure quickstart mallen databasen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Nästa steg

* [Visa andra exempel och scenarier för Logikappar i Azure](logic-apps-examples-and-scenarios.md)
* [Titta på en videogenomgång om hur du skapar den här lösningen slutpunkt till slutpunkt](http://aka.ms/logicappsdemo)
* [Lär dig hur du hanterar och fånga undantag från inom en logikapp](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png