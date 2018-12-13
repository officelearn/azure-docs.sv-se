---
title: Azure Time Series Insights Preview-säkerhet – konfigurera säkerheten för att komma åt och hantera Azure Time Series Insights Preview | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar säkerheten och behörigheterna som hanteringsåtkomst principer och data åtkomstprinciper för att skydda Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: 7f8eb66feecd58b766b3414b1bbc6bd4e27bf4f7
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275473"
---
# <a name="grant-data-access-to-an-environment"></a>Bevilja åtkomst till en miljö

Den här artikeln beskriver de två typerna av principer för förhandsversionen av Azure Time Series Insights-åtkomst.

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg om du vill bevilja åtkomst till data för en annan användare.

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Leta upp din Time Series Insights-miljö. Ange `Time Series` i den **Search** box. Välj **Time Series Environment** i sökresultatet.
1. Välj Time Series Insights-miljön från listan.
1. Välj **Dataåtkomstprinciper**, och välj sedan **+ Lägg till**.

    ![data-åtkomst-en][1]

1. Välj **Välj användare**. Sök efter användarnamn eller e-postadress att hitta användaren som du vill lägga till. Klicka på **Välj** att bekräfta valet.

    ![data access två][2]

1. Välj **Välj roll**. Välj rätt roll för användaren:

    * Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer **läsare** Tillåt användare att köra frågor mot data i miljön och spara personliga, inte delade frågor i miljön.

   Välj **OK** att bekräfta valet rollen.

    ![3-access-data][3]

1. Välj **OK** på den **Välj användarroll** sidan.

    ![data access fyra][4]

1. Bekräfta att den **Dataåtkomstprinciper** på sidan visas användarna och roller för varje användare.

    ![Data access fem][5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>Ange gäståtkomst till en användare från en annan Azure Active Directory-klient

`Guest` är inte en hanteringsroll. Det är en term som används för ett konto som bjuds in från en klient till en annan. När gästkontot bjuds in till klientens katalog, kan det ha samma åtkomstkontroll som tillämpas på det som ett annat konto. Du kan bevilja åtkomst till en Time Series Insights-miljö med hjälp av bladet åtkomstkontroll (IAM). Eller du kan ge åtkomst till data i miljön via bladet principerna för dataåtkomst. Mer information om Azure Active Directory (Azure AD) klient gäståtkomst [användare i Lägg till Azure Active Directory B2B-samarbetet i Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Följ dessa steg om du vill bevilja åtkomst till en Time Series Insights-miljö till en Azure AD-användare från en annan klient.

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Leta upp din Time Series Insights-miljö. Ange **tidsserier** i den **Search** box. Välj **Time Series Environment** i sökresultatet.
1. Välj Time Series Insights-miljön från listan.
1. Välj **Dataåtkomstprinciper**, och välj sedan **+ bjuda in**.

    ![sex-access-data][6]

1. Ange e-postadressen för användaren som du vill bjuda in. E-postadressen måste vara associerad med Azure AD. Du kan också inkludera ett personligt meddelande med inbjudan.

    ![data-åtkomst-sju][7]

1. Leta efter bekräftelse bubblan som visas på skärmen.

    ![data-åtkomst-8][8]

1. Välj **Välj användare**. Sök efter e-postadress du inbjuden för att hitta användaren som du vill lägga till gästanvändare. Klicka på **Välj** att bekräfta valet.

    ![data-åtkomst-nio][9]

1. Välj **Välj roll**. Välj rätt roll för gästanvändaren:

    * Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer **läsare** Tillåt användare att köra frågor mot data i miljön och spara personliga, inte delade frågor i miljön.

   Välj **OK** att bekräfta valet rollen.

    ![tio-access-data][10]

1. Välj **OK** på den **Välj användarroll** sidan.

1. Bekräfta att den **Dataåtkomstprinciper** sidan listar gästanvändaren och roller för varje gästanvändare.

    ![data-åtkomst-eleven][11]

1. Gästanvändaren måste nu gör för att komma åt miljön i Azure-klient som du inbjuden dem. Först accepterar de inbjudan som du har skickat. Den här inbjudan skickas via e-post till den e-postadress som du använde i steg 5. De väljer **börjar** att godkänna.

    ![data-åtkomst-tolv][12]

1. Gästanvändaren accepterar sedan de behörigheter som är associerade med administratörens organisation.

    ![data-åtkomst-13][13]

1. När gästanvändaren har loggat in till den e-postadressen du använde för att bjuda in dem, och de har accepterat inbjudan, går till insights.azure.com. En gång där de väljer Avataren bredvid sin e-postadress i det övre högra hörnet på skärmen.

    ![data-åtkomst-14][14]

1. Nästa gäst användaren väljer din Azure-klient från directory nedrullningsbara menyn. Den här klienten är det som du inbjuden dem.

    ![femton-access-data][15]

När gästanvändaren väljer din klient, visas Time Series Insights-miljö som du har angett dem åtkomst. De nu har alla funktioner som är associerade med den roll som du har angett dem med i steg 8.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du lägger till en händelsekälla för Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) till Time Series Insights-miljön.
* Skicka [händelser till händelsekällan](./time-series-insights-send-events.md).
* Visa [miljön i förhandsversionen av Time Series Insights explorer](./time-series-insights-update-explorer.md).

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