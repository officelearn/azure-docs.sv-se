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
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="application-insights-overview-dashboard-preview"></a>Instrumentpanel för program Översikt över Insights (förhandsversion)

Application Insights tillhandahåller alltid en sammanfattande översiktsrutan att snabbt och på snabb utvärdering av programmets hälsotillstånd och prestanda. Den nya förhandsversion översikt över instrumentpanelen tillhandahåller en snabbare mer flexibelt.

## <a name="how-do-i-test-out-the-new-experience"></a>Hur jag testa den nya upplevelsen?

 I Application Insights under: _översikt_väljer _försök ny översikt innan den blir erfarenhet som standard_.

![Översikt över Preview](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

Detta kommer att starta den nya standard översikt över instrumentpanelen:

![Översikt över förhandsgranskningsfönstret](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>Bättre prestanda

Valet av intervallet för tid har förenklats till ett enkelt gränssnitt för en enda klickning.

![Tidsintervall](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

Övergripande höjts prestanda avsevärt. Varje standardläget dynamiskt KPI panelen är kopplad till motsvarande Application Insights-funktion. Till exempel välja misslyckade begäranden startas den _fel_ fönstret:

![Fel](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Instrumentpanel för program

Instrumentpanel för program utnyttjar den befintliga instrumentpanel tekniken i Azure för att ange en enda helt anpassningsbar vy för programmets hälsotillstånd och prestanda.

Åtkomst till standard instrumentpanelen väljer _instrumentpanel för program_ i det övre vänstra hörnet.

![Instrumentpanelsvy](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Om det här är första gången du startas ansluter till instrumentpanelen en standardvy:

![Instrumentpanelsvy](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

Medan du kan hålla standardvyn om du tycker att det, du kan också lägga till och ta bort från instrumentpanelen bästa passar behoven i din grupp.

> [!NOTE]
> Alla användare med åtkomst till Application Insights-resursen dela samma program för instrumentpanelen. Ändringar som görs av en användare kommer att ändra vyn för alla användare.

Om du vill gå tillbaka till översikt upplevelsen bara välja:

![Översikt över knappen](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>Nästa steg

- [Trattar](usage-funnels.md)
- [Kvarhållning](app-insights-usage-retention.md)
- [Användarflöden](app-insights-usage-flows.md)
- [Instrumentpaneler](app-insights-dashboards.md)
