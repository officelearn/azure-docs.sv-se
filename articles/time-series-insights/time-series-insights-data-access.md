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
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423382"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Bevilja åtkomst till en Time Series Insights-miljö med Azure Portal

Den här artikeln beskriver de två typerna av principer för åtkomst av Time Series Insights.

## <a name="video"></a>Video: 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>I den här videon beskriver vi skapa och hantera åtkomstprinciper i Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

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

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>Ange gäståtkomst till en användare från en annan AAD-klient

”Gäst” är inte en ledningsroll; Det är en term som används för ett konto som har blivit inbjuden från en klient till en annan. När gästkontot har bjudits in till klientens katalog, kan den ha samma åtkomstkontroll tillämpas som något annat konto att bevilja åtkomst till en TSI-miljö med bladet åtkomstkontroll (IAM) eller att bevilja åtkomst till data i miljön via bladet principerna för dataåtkomst. Mer information om gäståtkomst för AAD-klient finns i den här [dokumentet](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Följ dessa steg om du vill bevilja åtkomst till en Time Series Insights-miljö till en AAD-användare från en annan klient:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din Time Series Insights-miljö. Typ **tidsserier** i den **search** box. Välj **Time Series Environment** i sökresultatet.

3. Välj Time Series Insights-miljön från listan.

4. Välj **Dataåtkomstprinciper**och välj sedan + **bjuda in**.

    ![Hantera Time Series Insights-källan – Bjud in användare](media/data-access/getstarted-grant-data-access6.png)

5. Ange användarens e-postadress som du vill bjuda in. Observera att det ska vara ett e-postmeddelande som är associerade med AAD. Du kan också inkludera ett personligt meddelande med inbjudan.

    ![Hantera Time Series Insights-källan – välj användare](media/data-access/getstarted-grant-data-access7.png)

6. Du bör se en bekräftelse bubbla som visas på skärmen.

    ![Hantera Time Series Insights-källan – Bekräfta användare](media/data-access/getstarted-grant-data-access8.png)

7. Välj **Välj användare**. Sök efter gästanvändaren som du precis har bjudits in för att hitta användaren som du vill lägga till e-postadress. Klicka på **Välj** att bekräfta valet.
  
    ![Hantera Time Series Insights-källan – Bekräfta användare](media/data-access/getstarted-grant-data-access9.png)

8. Välj **Välj roll**. Välj rätt roll för gästanvändaren:

    * Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer **läsare** att tillåta användare söker data i miljön och sparar personliga (inte delade) frågor i miljön.

    Välj **Ok** att bekräfta valet rollen.

    ![Hantera Time Series Insights-källan – välj roll](media/data-access/getstarted-grant-data-access10.png)

9. Välj **Ok** i den **Välj användarroll** sidan.

10. Den **Dataåtkomstprinciper** sidan visas nu gästanvändaren och roller för varje gästanvändare.

    ![Hantera Time Series Insights-källan – bekräfta roll](media/data-access/getstarted-grant-data-access11.png)

11. Nu gästanvändaren behöver vidta vissa åtgärder för att komma åt miljön finns i Azure-klient du bara kan du bjuda in dem till. De måste först tacka ja till inbjudan som du just skickade. Den här inbjudan skickas via e-post till den e-postadress som du har bjudits in i steg 5. De ska klicka ”komma igång”, att godkänna.

    ![Hantera Time Series Insights-källan – Bjud in användare](media/data-access/getstarted-grant-data-access12.png)

12. Därefter behöver gästanvändaren acceptera de behörigheter som är associerade med administratörens organisation.

    ![Hantera Time Series Insights-källan – acceptera behörigheter](media/data-access/getstarted-grant-data-access13.png)

13. Om gästanvändaren är inloggad i e-postadressen du inbjuden och de accepterar inbjudan, de kommer att gå till insights.azure.com. En gång där användaren uppmanas att klicka på Avataren bredvid sin e-post i det övre högra hörnet på skärmen. 

    ![Hantera Time Series Insights-källan – acceptera behörigheter](media/data-access/getstarted-grant-data-access14.png)

14. Därefter gästanvändaren väljer din Azure-klient från directory nedrullningsbara menyn. Det här är den klient som du inbjuden att. 

    ![Hantera Time Series Insights-källan – acceptera behörigheter](media/data-access/getstarted-grant-data-access15.png)

15. Slutligen när gästanvändaren väljer din klient, visas så den Time Series Insights-miljö som du nyligen har angett dem åtkomst till. De bör nu ha alla funktioner som är kopplade till rollen som du har angett dem i steg 8.

## <a name="next-steps"></a>Nästa steg
* Lär dig [hur du lägger till en Event Hub-händelsekälla till Azure Time Series Insights-miljön](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.
* Visa din miljö i [Time Series Insights explorer](https://insights.timeseries.azure.com).
