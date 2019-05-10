---
title: Konfigurera säkerhet för att komma åt och hantera Azure Time Series Insights Preview | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar säkerheten och behörigheterna som hanteringsåtkomst principer och data åtkomstprinciper för att skydda Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: adb30cc3af50b71cdc03e41d847bd4d824cffea6
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236390"
---
# <a name="grant-data-access-to-an-environment"></a>Bevilja åtkomst till en miljö

Den här artikeln beskriver de två typerna av principer för förhandsversionen av Azure Time Series Insights-åtkomst.

## <a name="sign-in-to-tsi"></a>Logga in på TSI

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Leta upp din Time Series Insights-miljö. Ange `Time Series` i den **Search** box. Välj **Time Series Environment** i sökresultatet.
1. Välj Time Series Insights-miljön från listan.

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg om du vill bevilja åtkomst till data för en annan användare.

1. Välj **Dataåtkomstprinciper**, och välj sedan **+ Lägg till**.

    [![Data-access-one](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Välj **Välj användare**. Sök efter användarnamn eller e-postadress att hitta användaren som du vill lägga till. Klicka på **Välj** att bekräfta valet.

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Välj **Välj roll**. Välj rätt roll för användaren:

    * Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer **läsare** Tillåt användare att köra frågor mot data i miljön och spara personliga, inte delade frågor i miljön.

   Välj **OK** att bekräfta valet rollen.

    [![Data-access-three](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Välj **OK** på den **Välj användarroll** sidan.

    [![Data-access-four](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Bekräfta att den **Dataåtkomstprinciper** på sidan visas användarna och roller för varje användare.

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Ange gäståtkomst från en annan AAD-klient

`Guest` är inte en hanteringsroll. Det är en term som används för ett konto som bjuds in från en klient till en annan. När gästkontot bjuds in till klientens katalog, kan det ha samma åtkomstkontroll som tillämpas på det som ett annat konto. Du kan bevilja åtkomst till en Time Series Insights-miljö med hjälp av bladet åtkomstkontroll (IAM). Eller du kan ge åtkomst till data i miljön via bladet principerna för dataåtkomst. Mer information om Azure Active Directory (Azure AD) klient gäståtkomst [användare i Lägg till Azure Active Directory B2B-samarbetet i Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Följ dessa steg om du vill bevilja åtkomst till en Time Series Insights-miljö till en Azure AD-användare från en annan klient.

1. Välj **Dataåtkomstprinciper**, och välj sedan **+ bjuda in**.

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Ange e-postadressen för användaren som du vill bjuda in. E-postadressen måste vara associerad med Azure AD. Du kan också inkludera ett personligt meddelande med inbjudan.

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Leta efter bekräftelse bubblan som visas på skärmen.

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Välj **Välj användare**. Sök efter e-postadress du inbjuden för att hitta användaren som du vill lägga till gästanvändare. Klicka på **Välj** att bekräfta valet.

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Välj **Välj roll**. Välj rätt roll för gästanvändaren:

    * Välj **deltagare** om du vill tillåta användare att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer **läsare** Tillåt användare att köra frågor mot data i miljön och spara personliga, inte delade frågor i miljön.

   Välj **OK** att bekräfta valet rollen.

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Välj **OK** på den **Välj användarroll** sidan.

1. Bekräfta att den **Dataåtkomstprinciper** sidan listar gästanvändaren och roller för varje gästanvändare.

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Gästanvändaren måste nu gör för att komma åt miljön i Azure-klient som du inbjuden dem. Först accepterar de inbjudan som du har skickat. Den här inbjudan skickas via e-post till den e-postadress som du använde i steg 5. De väljer **börjar** att godkänna.

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Gästanvändaren accepterar sedan de behörigheter som är associerade med administratörens organisation.

    [![Data-access-thirteen](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. När gästanvändaren har loggat in till den e-postadressen du använde för att bjuda in dem, och de har accepterat inbjudan, går till insights.azure.com. En gång där de väljer Avataren bredvid sin e-postadress i det övre högra hörnet på skärmen.

    [![Data-access-fourteen](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Nästa gäst användaren väljer din Azure-klient från directory nedrullningsbara menyn. Den här klienten är det som du inbjuden dem.

    [![Data-access-fifteen](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

När gästanvändaren väljer din klient, visas Time Series Insights-miljö som du har angett dem åtkomst. De nu har alla funktioner som är associerade med den roll som du har angett dem med i **steg 5**.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du lägger till en händelsekälla för Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) till Time Series Insights-miljön.

* Skicka [händelser till händelsekällan](./time-series-insights-send-events.md).

* Visa [miljön i förhandsversionen av Time Series Insights explorer](./time-series-insights-update-explorer.md).
