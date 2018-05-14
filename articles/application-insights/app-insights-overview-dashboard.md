---
title: Instrumentpanel för program insikter översikt | Microsoft Docs
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
ms.openlocfilehash: bccb56ad45d9054a437bf2d85e74a8d81fbc3db1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="overview-dashboard-preview"></a>Översikt över instrumentpanelen (förhandsgranskning)

Application Insights tillhandahåller alltid en sammanfattande översiktsrutan att snabbt och på snabb utvärdering av programmets hälsotillstånd och prestanda. Starta en ny snabbare mer flexibel miljö på 15 maj 2018 släpps som en förhandsgranskning. På den 29 maj 2018 kommer klassiska översikt upplevelsen tas bort.

## <a name="how-do-i-test-out-the-new-experience"></a>Hur jag testa den nya upplevelsen?

Den 15 maj börjar den nya miljön visas i Application Insights under: _Undersök_ > _översikt (förhandsgranskning)_.

![Översikt över Preview](.\media\app-insights-overview-dashboard\01.png)

Detta kommer att starta den nya standard översikt över instrumentpanelen:

![Översikt över förhandsgranskningsfönstret](.\media\app-insights-overview-dashboard\02.png)

## <a name="better-performance"></a>Bättre prestanda

Valet av intervallet för tid har förenklats till ett enkelt gränssnitt för en enda klickning.

![Tidsintervall](.\media\app-insights-overview-dashboard\04.png)

Övergripande höjts prestanda avsevärt. Varje standardläget dynamiskt KPI panelen är kopplad till motsvarande Application Insights-funktion. Till exempel välja misslyckade begäranden startas den _fel_ fönstret:

![Fel](.\media\app-insights-overview-dashboard\03.png)

## <a name="application-dashboard"></a>Instrumentpanel för program

Instrumentpanel för program utnyttjar den befintliga instrumentpanel tekniken i Azure för att ange en enda helt anpassningsbar vy för programmets hälsotillstånd och prestanda.

Åtkomst till standard instrumentpanelen väljer _instrumentpanel för program_ i det övre vänstra hörnet.

![Instrumentpanelsvy](.\media\app-insights-overview-dashboard\0009.png)

Om det här är första gången du startas ansluter till instrumentpanelen en standardvy:

![Instrumentpanelsvy](.\media\app-insights-overview-dashboard\06.png)

Medan du kan hålla standardvyn om du tycker att det, du kan också lägga till och ta bort från instrumentpanelen bästa passar behoven i din grupp.

Om du vill gå tillbaka till översikt upplevelsen bara välja:

![Översikt över knappen](.\media\app-insights-overview-dashboard\07.png)

Det finns också en ny knapp _PIN-kod delar_.

![Översikt över knappen](.\media\app-insights-overview-dashboard\008.png)

Detta replikerar en liten kända funktion från den klassiska översikt som gör att du kan vidta någon av panelerna från den gamla översikt upplevelsen _(aviseringar, tillgänglighet, Live mått, användning, proaktiv identifieringar och programavbildningen)_ och Lägg till dem i anpassade instrumentpaneler. 

Vid standardvärdet _instrumentpanel för program_ redan har vi lagt till dessa paneler. Men om du skapar ytterligare anpassade instrumentpaneler, eller om någon i gruppen tar bort en klassiska panel och du vill lägga till den, _PIN-kod delar_ innehåller funktionerna i något enkelt att hitta plats.
