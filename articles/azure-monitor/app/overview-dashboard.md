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
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: d1823779f8a8070149811e2349fc9f4281072d38
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497156"
---
# <a name="application-insights-overview-dashboard"></a>Application Insights instrumentpanel

Application Insights tillhandahåller alltid en sammanfattning av översiktsfönstret för att snabbt, med ett ögonkast bedömning av programmets hälsotillstånd och prestanda. Den nya instrumentpanelen är snabbare mer flexibel.

## <a name="how-do-i-test-out-the-new-experience"></a>Hur jag testa den nya upplevelsen?

Den nya översikt över instrumentpanelen startar nu som standard:

![Översikt över förhandsgranskningsfönstret](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Bättre prestanda

Val av tidsintervall har förenklats till ett enkelt klick-gränssnitt.

![Tidsintervall](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Övergripande har prestanda avsevärt ökat. Du har ett klick åtkomst till populära funktioner som **Search** och **Analytics**. Varje standard dynamiskt uppdaterar KPI panel ger inblick i motsvarande Application Insights-funktioner. Mer information om misslyckades begäranden väljer **fel** under den **Undersök** rubrik:

![fel](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Instrumentpanel för program

Instrumentpanel för program utnyttjar den befintliga instrumentpanelen tekniken i Azure för att tillhandahålla en helt anpassningsbar enkel vy av dina programmets hälsotillstånd och prestanda.

Åtkomst till standard instrumentpanelen Välj _instrumentpanel för program_ i det övre vänstra hörnet.

![Instrumentpanelsvy](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Om det här är första gången du åtkomst till instrumentpanelen, startas så en standardvy:

![Instrumentpanelsvy](./media/overview-dashboard/0001-dashboard.png)

Om du vill att den kan du låta standardvyn. Eller du kan också lägga till och ta bort från instrumentpanelen till sätt som passar behoven i ditt team.

> [!NOTE]
> Alla användare med åtkomst till Application Insights-resursen dela samma program för instrumentpanelen. Ändringar som görs av en användare kommer att ändra vyn för alla användare.

Gå tillbaka till översikt upplevelsen bara välja:

![Översikt över knappen](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Felsökning

Om du väljer **konfigurera Panelinställningar** och ange ett anpassat tidsintervall som överstiger 31 dagar instrumentpanelen inte visas längre än 31 dagarnas data, även med en standard-datakvarhållning på 90 dagar. Det finns ingen lösning på problemet.

## <a name="next-steps"></a>Nästa steg

- [Trattar](../../azure-monitor/app/usage-funnels.md)
- [Kvarhållning](../../azure-monitor/app/usage-retention.md)
- [Användarflöden](../../azure-monitor/app/usage-flows.md)