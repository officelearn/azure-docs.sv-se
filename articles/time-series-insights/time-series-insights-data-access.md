---
title: Konfigurera säkerhet för att komma åt och hantera Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar säkerheten och behörigheterna som hanteringsåtkomst principer och data åtkomstprinciper för att skydda Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 97c9480d6f2b75d83252bfb6410d7b5f946757ef
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630661"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Bevilja åtkomst till en Time Series Insights-miljö med Azure Portal

Time Series Insights-miljöer har två oberoende typer av principer för åtkomst.

* Hantera åtkomstprinciper
* Dataåtkomstprinciper

Båda principerna beviljar Azure Active Directory-huvudkonton (användare och appar) olika behörigheter i en viss miljö. Huvudkonton (användare och appar) måste tillhöra den active directory (kallas även Azure-klient) som är associerade med prenumerationen som innehåller miljön.

Principer för hantering av åtkomst beviljar behörigheter som rör konfigurationen av miljön, som
*   Skapandet och borttagningen av miljön, händelsekällor, referensdatamängder och
*   hantering av principerna för dataåtkomst.

Principerna för dataåtkomst beviljar behörigheter för att utfärda datafrågor, manipulera referensdata i miljön och sparade delade frågor och perspektiv som är associerade till miljön.

De två typerna av principer möjliggör en tydlig uppdelning mellan åtkomst till hantering av miljön och till data inuti miljön. Det är exempelvis möjligt att konfigurera en miljö så att miljöns ägare/skapare av miljön tas bort från dataåtkomsten. Dessutom kan beviljas användare och tjänster som ska kunna läsa data från miljön inte åtkomst till miljöns konfiguration.

## <a name="grant-data-access"></a>Bevilja åtkomst till data
Följ dessa steg om du vill bevilja åtkomst till data för en användares huvudnamn:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din Time Series Insights-miljö. Typ **tidsserier** i den **search** box. Välj **Time Series Environment** i sökresultatet. 

3. Välj Time Series Insights-miljön från listan.
   
4. Välj **Dataåtkomstprinciper**och välj sedan **+ Lägg till**.
  ![Hantera Time Series Insights-källan – miljö](media/data-access/getstarted-grant-data-access1.png)

5. Välj **Välj användare**.  Sök efter användarnamn eller e-postadress att hitta användaren som du vill lägga till. Klicka på **Välj** att bekräfta valet. 

   ![Hantera Time Series Insights-källan – lägg till](media/data-access/getstarted-grant-data-access2.png)

6. Välj **Välj roll**. Välj rätt roll för användaren:
   - Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön. 
   - Annars väljer **läsare** att tillåta användare söker data i miljön och sparar personliga (inte delade) frågor i miljön.

   Välj **Ok** att bekräfta valet rollen.

   ![Hantera Time Series Insights-källan – välj användare](media/data-access/getstarted-grant-data-access3.png)

8. Välj **Ok** i den **Välj användarroll** sidan.

   ![Hantera Time Series Insights-källan – välj roll](media/data-access/getstarted-grant-data-access4.png)

9. Den **Dataåtkomstprinciper** sidan visas användarna och roller för varje användare.

   ![Hantera Time Series Insights-källan – resultat](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig [hur du lägger till en Event Hub-händelsekälla till Azure Time Series Insights-miljön](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.
* Visa din miljö i [Time Series Insights explorer](https://insights.timeseries.azure.com).
