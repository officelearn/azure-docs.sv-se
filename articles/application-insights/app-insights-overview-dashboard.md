---
title: Azure-program insikter översikt över instrumentpanelen | Microsoft Docs
description: Övervaka program med Azure Application Insights och översikt över instrumentpanelen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: mbullwin
ms.openlocfilehash: 4a639b51fd930c5c74ee78b37fbe8e97e39dbc32
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337564"
---
# <a name="application-insights-overview-dashboard"></a>Application Insights översikt över instrumentpanelen

Application Insights tillhandahåller alltid en sammanfattande översiktsrutan att snabbt och på snabb utvärdering av programmets hälsotillstånd och prestanda. Den nya översikt över instrumentpanelen tillhandahåller en snabbare mer flexibelt.

## <a name="how-do-i-test-out-the-new-experience"></a>Hur jag testa den nya upplevelsen?

Den nya översikt över instrumentpanelen startar nu som standard:

![Översikt över förhandsgranskningsfönstret](.\media\app-insights-overview-dashboard\overview-0001.png)

## <a name="better-performance"></a>Bättre prestanda

Valet av intervallet för tid har förenklats till ett enkelt gränssnitt för en enda klickning.

![Tidsintervall](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

Övergripande höjts prestanda avsevärt. Du har en enda klickning åtkomst till vanliga funktioner som **Sök** och **Analytics**. Varje standardläget dynamiskt KPI panelen ger inblick i motsvarande Application Insights-funktioner. Kunde inte begäranden väljer att lära dig mer om **fel** under den **Undersök** huvud:

![Fel](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Instrumentpanel för program

Instrumentpanel för program utnyttjar den befintliga instrumentpanel tekniken i Azure för att ange en enda helt anpassningsbar vy för programmets hälsotillstånd och prestanda.

Åtkomst till standard instrumentpanelen väljer _instrumentpanel för program_ i det övre vänstra hörnet.

![Instrumentpanelsvy](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Om det här är första gången du ansluter till instrumentpanelen, startas en standardvy:

![Instrumentpanelsvy](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

Du kan behålla standardvyn om du vill att den. Eller du kan också lägga till och ta bort från instrumentpanelen till bäst behoven för din grupp.

> [!NOTE]
> Alla användare med åtkomst till Application Insights-resursen dela samma program för instrumentpanelen. Ändringar som görs av en användare kommer att ändra vyn för alla användare.

Om du vill gå tillbaka till översikt upplevelsen bara välja:

![Översikt över knappen](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="access-the-classic-overview-dashboard"></a>Komma åt klassiska översikt över instrumentpanelen

Om du missar gamla översikten väljer **klassiska översikt**.

![Klassiska översikt](.\media\app-insights-overview-dashboard\overview-classic.png)

## <a name="next-steps"></a>Nästa steg

- [Trattar](usage-funnels.md)
- [Kvarhållning](app-insights-usage-retention.md)
- [Användarflöden](app-insights-usage-flows.md)
- [Instrumentpaneler](app-insights-dashboards.md)
