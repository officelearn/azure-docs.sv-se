---
title: Konfigurera säkerhet för att komma åt och hantera Azure tid serien Insights | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar säkerhet och behörigheter som hanteringsåtkomst principer och data åtkomstprinciper för säker Azure tid serien insikter.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 4306d22f03faa55fb6fc8be1a359aea3410e8038
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653818"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Bevilja åtkomst till en Time Series Insights-miljö med Azure Portal

Time Series Insights-miljöer har två oberoende typer av principer för åtkomst.

* Hantera åtkomstprinciper
* Dataåtkomstprinciper

Båda principerna beviljar Azure Active Directory-huvudkonton (användare och appar) olika behörigheter i en viss miljö. Säkerhetsobjekt (användare och appar) måste tillhöra en active directory (kallas även Azure-klient) som är associerade med prenumerationen som innehåller miljön.

Principer för hantering av åtkomst beviljar behörigheter som rör konfigurationen av miljön, som
*   Skapandet och borttagningen av miljön, händelsekällor, referensdatamängder och
*   hantering av principerna för dataåtkomst.

Principerna för dataåtkomst beviljar behörigheter för att utfärda datafrågor, manipulera referensdata i miljön och sparade delade frågor och perspektiv som är associerade till miljön.

De två typerna av principer möjliggör en tydlig uppdelning mellan åtkomst till hantering av miljön och till data inuti miljön. Det är exempelvis möjligt att ställa in en miljö så att ägaren/författaren av miljön tas bort från åtkomst till data. Dessutom kan beviljas användare och tjänster som ska kunna läsa data från miljön ingen åtkomst till miljöns konfiguration.

## <a name="grant-data-access"></a>Bevilja åtkomst till data
Följ dessa steg om du vill bevilja åtkomst till data för en annan användare:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Hitta tid serien insikter miljön. Typen **tidsserier** i den **Sök** rutan. Välj **serie miljön** i sökresultaten. 

3. Välj Time Series Insights-miljön från listan.
   
4. Välj **principerna dataåtkomst**och välj **+ Lägg till**.
  ![Hantera tid serien insikter källan - miljö](media/data-access/getstarted-grant-data-access1.png)

5. Välj **väljer användaren**.  Sök efter användarnamn eller e-postadress att hitta användaren som du vill lägga till. Klicka på **Välj** att bekräfta valet. 

   ![Hantera Time Series Insights-källan – lägg till](media/data-access/getstarted-grant-data-access2.png)

6. Välj **Välj rolltjänster**. Välj rollen åtkomstbehörighet för användaren:
   - Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare i miljön. 
   - Annars väljer **Reader** att tillåta användaren fråga efter data i miljön och spara frågor för personliga (inte delade) i miljön.

   Välj **Ok** bekräfta valet av rollen.

   ![Hantera Time Series Insights-källan – välj användare](media/data-access/getstarted-grant-data-access3.png)

8. Välj **Ok** i den **Välj användarroll** sidan.

   ![Hantera Time Series Insights-källan – välj roll](media/data-access/getstarted-grant-data-access4.png)

9. Den **principerna dataåtkomst** sidan visas användarna och roller för varje användare.

   ![Hantera Time Series Insights-källan – resultat](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Nästa steg
* Läs [hur du lägger till en Händelsehubb händelsekälla Azure tid serien Insights miljön](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Skicka händelser](time-series-insights-send-events.md) käll-händelse.
* Visa din miljö i [tid serien insikter explorer](https://insights.timeseries.azure.com).
