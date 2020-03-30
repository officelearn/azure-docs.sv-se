---
title: Översiktsinstrumentpanel för Azure Application Insights | Microsoft-dokument
description: Övervaka program med Azure Application Insights och Översiktsinstrumentpanelen.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e5188972d9058b85a9765c7d33f6209b37245d7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669904"
---
# <a name="application-insights-overview-dashboard"></a>Instrumentpanelen Översikt över programinsikter

Application Insights har alltid tillhandahållit en sammanfattande översiktsfönster för att möjliggöra en snabb och snabb bedömning av programmets hälsa och prestanda. Den nya översiktsinstrumentpanelen ger en snabbare mer flexibel upplevelse.

## <a name="how-do-i-test-out-the-new-experience"></a>Hur testar jag den nya upplevelsen?

Den nya översiktsinstrumentpanelen lanseras nu som standard:

![Förhandsgranskningsfönstret översikt](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Bättre prestanda

Valet av tidsintervall har förenklats till ett enkelt gränssnitt med ett klick.

![Tidsintervall](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Den totala utvecklingen har ökat kraftigt. Du har en klick tillgång till populära funktioner som **Sök** och **Analytics**. Varje standardpanel för dynamisk uppdatering av KPI ger insikt i motsvarande application insights-funktioner. Om du vill veta mer om misslyckade begäranden väljer du Fel under **granskningshuvudet:** **Investigate**

![Fel](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Instrumentpanel för program

Instrumentpanelen för program utnyttjar den befintliga instrumentpanelstekniken i Azure för att tillhandahålla en helt anpassningsbar enrutasvy av programmets hälsa och prestanda.

Om du vill komma åt standardinstrumentpanelen väljer du _Programinstrumentpanel_ i det övre vänstra hörnet.

![Instrumentpanelsvy](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Om det är första gången du öppnar instrumentpanelen startar en standardvy:

![Instrumentpanelsvy](./media/overview-dashboard/0001-dashboard.png)

Du kan behålla standardvyn om du vill ha den. Eller så kan du också lägga till och ta bort från instrumentpanelen för att bäst passa teamets behov.

> [!NOTE]
> Alla användare med åtkomst till application insights-resursen delar samma programinstrumentpanel. Ändringar som görs av en användare ändrar vyn för alla användare.

Om du vill navigera tillbaka till översiktsupplevelsen väljer du bara:

![Knappen Översikt](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Felsökning

Om du väljer **Konfigurera panelinställningar** och ange ett anpassat tidsintervall som överstiger 31 dagar visas inte instrumentpanelen längre än 31 dagars data, även med standarddatalagring på 90 dagar. Det finns för närvarande ingen lösning för det här beteendet.

## <a name="next-steps"></a>Nästa steg

- [Trattar](../../azure-monitor/app/usage-funnels.md)
- [Kvarhållning](../../azure-monitor/app/usage-retention.md)
- [Användarflöden](../../azure-monitor/app/usage-flows.md)
