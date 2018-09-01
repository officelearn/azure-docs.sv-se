---
title: Instrumentpanel med Azure Application Insights | Microsoft Docs
description: Övervaka program med Azure Application Insights och instrumentpanel med funktioner.
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
ms.date: 08/31/2018
ms.author: mbullwin
ms.openlocfilehash: 313be519662570debdd7c78a43bbfe7c27e63296
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344638"
---
# <a name="application-insights-overview-dashboard"></a>Application Insights instrumentpanel

Application Insights tillhandahåller alltid en sammanfattning av översiktsfönstret för att snabbt, med ett ögonkast bedömning av programmets hälsotillstånd och prestanda. Den nya instrumentpanelen är snabbare mer flexibel.

## <a name="how-do-i-test-out-the-new-experience"></a>Hur jag testa den nya upplevelsen?

Den nya översikt över instrumentpanelen startar nu som standard:

![Översikt över förhandsgranskningsfönstret](.\media\app-insights-overview-dashboard\overview.png)

## <a name="better-performance"></a>Bättre prestanda

Val av tidsintervall har förenklats till ett enkelt klick-gränssnitt.

![Tidsintervall](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

Övergripande har prestanda avsevärt ökat. Du har ett klick åtkomst till populära funktioner som **Search** och **Analytics**. Varje standard dynamiskt uppdaterar KPI panel ger inblick i motsvarande Application Insights-funktioner. Mer information om misslyckades begäranden väljer **fel** under den **Undersök** rubrik:

![Fel](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Instrumentpanel för program

Instrumentpanel för program utnyttjar den befintliga instrumentpanelen tekniken i Azure för att tillhandahålla en helt anpassningsbar enkel vy av dina programmets hälsotillstånd och prestanda.

Åtkomst till standard instrumentpanelen Välj _instrumentpanel för program_ i det övre vänstra hörnet.

![Instrumentpanelsvy](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Om det här är första gången du åtkomst till instrumentpanelen, startas så en standardvy:

![Instrumentpanelsvy](.\media\app-insights-overview-dashboard\0001-dashboard.png)

Om du vill att den kan du låta standardvyn. Eller du kan också lägga till och ta bort från instrumentpanelen till sätt som passar behoven i ditt team.

> [!NOTE]
> Alla användare med åtkomst till Application Insights-resursen dela samma program för instrumentpanelen. Ändringar som görs av en användare kommer att ändra vyn för alla användare.

Gå tillbaka till översikt upplevelsen bara välja:

![Översikt över knappen](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>Nästa steg

- [Trattar](usage-funnels.md)
- [Kvarhållning](app-insights-usage-retention.md)
- [Användarflöden](app-insights-usage-flows.md)
- [Instrumentpaneler](app-insights-dashboards.md)
