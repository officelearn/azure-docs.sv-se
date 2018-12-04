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
ms.date: 11/26/2018
ms.openlocfilehash: 3694ff0db63d685cb63f586062158b4f8acac5cf
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847617"
---
# <a name="grant-data-access-to-an-environment"></a>Bevilja åtkomst till en miljö

Den här artikeln beskriver de två typerna av principer för åtkomst av Azure Time Series Insights (förhandsversion).

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg om du vill bevilja åtkomst till data för en användares huvudnamn:

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Leta upp din Time Series Insights (TSI)-miljö. Typ `Time Series` i den **search** box. Välj **Time Series Environment** i sökresultatet.
1. Välj miljön TSI i listan.
1. Välj **Dataåtkomstprinciper**och välj sedan **+ Lägg till**.

    ![data-åtkomst-en][1]

1. Välj **Välj användare**. Sök efter användarnamn eller e-postadress att hitta användaren som du vill lägga till. Klicka på **Välj** att bekräfta valet.

    ![data access två][2]

1. Välj **Välj roll**. Välj rätt roll för användaren:

    * Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer **läsare** att tillåta användare söker data i miljön och sparar personliga (inte delade) frågor i miljön.

    Välj **Ok** att bekräfta valet rollen.

    ![3-access-data][3]

1. Välj **Ok** i den **Välj användarroll** sidan.

    ![data access fyra][4]

1. Den **Dataåtkomstprinciper** sidan visas användarna och roller för varje användare.

    ! [data access fem[5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>Ange gäståtkomst till en användare från en annan Azure Active Directory-klient

`Guest` är inte en ledningsroll; Det är en term som används för ett konto som har blivit inbjuden från en klient till en annan. När gästkontot har bjudits in till klientens katalog, kan den ha samma åtkomstkontroll tillämpas som något annat konto att bevilja åtkomst till en TSI-miljö med bladet åtkomstkontroll (IAM) eller att bevilja åtkomst till data i miljön via bladet principerna för dataåtkomst. Mer information om Azure Active Directory (AAD)-klient gäståtkomst [användare i Lägg till Azure Active Directory B2B-samarbetet i Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Följ dessa steg om du vill tilldela en TSI-miljö till en AAD-användare från en annan klient gäståtkomst:

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Leta upp din TSI-miljö. Typ **tidsserier** i sökrutan. Välj **Time Series Environment** i sökresultatet.
1. Välj miljön TSI i listan.
1. Välj **Dataåtkomstprinciper**och välj sedan **+ bjuda in**.

    ![sex-access-data][6]

1. Ange användarens e-postadress som du vill bjuda in. Observera att det ska vara ett e-postmeddelande som är associerade med AAD. Du kan också inkludera ett personligt meddelande med inbjudan.

    ![data-åtkomst-sju][7]

1. Du bör se en bekräftelse bubbla som visas på skärmen.

    ![data-åtkomst-8][8]

1. Välj **Välj användare**. Sök efter gästanvändaren som du precis har bjudits in för att hitta användaren som du vill lägga till e-postadress. Klicka på **Välj** att bekräfta valet.

    ![data-åtkomst-nio][9]

1. Välj **Välj** roll. Välj rätt roll för gästanvändaren:

    1. Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.
    1. Annars väljer **läsare** att tillåta användare söker data i miljön och sparar personliga (inte delade) frågor i miljön.
    1. Välj **Ok** att bekräfta valet rollen.

    ![tio-access-data][10]

1. Välj **Ok** i den **Välj användarroll** sidan.

1. Den **Dataåtkomstprinciper** sidan visas nu gästanvändaren och roller för varje gästanvändare.

    ![data-åtkomst-eleven][11]

1. Nu gästanvändaren behöver vidta vissa åtgärder för att komma åt miljön finns i Azure-klient du bara kan du bjuda in dem till. De måste först tacka ja till inbjudan som du just skickade. Den här inbjudan skickas via e-post till den e-postadress som du har bjudits in i steg 5. De ska klicka **börjar**, för att godkänna.

    ![data-åtkomst-tolv][12]

1. Därefter behöver gästanvändaren acceptera de behörigheter som är associerade med administratörens organisation.

    ![data-åtkomst-13][13]

1. Om gästanvändaren är inloggad i e-postadressen du inbjuden och de accepterar inbjudan, de kommer att gå till insights.azure.com. En gång där användaren uppmanas att klicka på Avataren bredvid sin e-post i det övre högra hörnet på skärmen.

    ![data-åtkomst-14][14]

1. Därefter gästanvändaren väljer din Azure-klient från directory nedrullningsbara menyn. Det här är den klient som du inbjuden att.

    ![femton-access-data][15]

1. Slutligen när gästanvändaren väljer din klient, visas så den TSI-miljö som du nyligen har angett dem åtkomst till. De bör nu ha alla funktioner som är kopplade till rollen som du har angett dem i steg 8.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du lägger till en Event Hub-händelsekälla](./time-series-insights-how-to-add-an-event-source-eventhub.md) till Azure TSI-miljön.
* Skicka [händelser till händelsekällan](./time-series-insights-send-events.md).
* Visa [miljön i Time Series Insights explorer](./time-series-insights-update-explorer.md).

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png