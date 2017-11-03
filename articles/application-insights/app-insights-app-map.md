---
title: Programavbildningen i Azure Application Insights | Microsoft Docs
description: "En visuell presentation av beroenden mellan komponenter för appar som är märkta med KPI: er och -varningar."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: e1eb2177d6032142781e6e31af6c7f6313d38f4d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="application-map-in-application-insights"></a>Programavbildningen i Application Insights
I [Azure Application Insights](app-insights-overview.md), programavbildningen är en visuell layout av beroenden för programkomponenter. Varje komponent visas KPI: er som belastning, prestanda, fel och varningar som hjälper dig att identifiera någon komponent som orsakar ett prestandaproblem eller fel. Du kan klicka på via från valfri komponent till mer detaljerad diagnostik, till exempel Application Insights händelser. Om din app använder Azure-tjänster, kan du också klicka via Azure Diagnostics, till exempel SQL Database Advisor-rekommendationer.

Du kan fästa en programavbildningen Azure instrumentpanelen, om det är fullt fungerande som andra diagram. 

## <a name="open-the-application-map"></a>Öppna programmet karta
Öppna kartan från bladet översikt för programmet:

![Öppna appen karta](./media/app-insights-app-map/01.png)

![App-karta](./media/app-insights-app-map/02.png)

Kartan visar:

* Tillgänglighetstester
* Klient-komponenten (övervakas med JavaScript SDK)
* Server-sida-komponent
* Beroenden för klient- och serverkomponenter

Du kan expandera och komprimera beroende länken grupper:

![Dölj](./media/app-insights-app-map/03.png)

Om du har många beroenden av en typ (SQL, http-etc.), kan de visas grupperade. 

![grupperade beroenden](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Hitta problem
Varje nod har relevanta nyckeltal, till exempel priser för belastning, prestanda och fel för den komponenten. 

Varning ikoner Markera möjliga problem. En orange varning som innebär att det finns fel i förfrågningar, sidvisningar eller beroendeanrop. Rött innebär en felintervall över 5 procent. Om du vill anpassa dessa tröskelvärlden öppna alternativ.

![fel ikoner](./media/app-insights-app-map/04.png)

Aktiva aviseringar också visa upp: 

![aktiva aviseringar](./media/app-insights-app-map/05.png)

Om du använder SQL Azure, det finns en ikon som visas när det finns rekommendationer om hur du kan förbättra prestanda. 

![Azure rekommendation](./media/app-insights-app-map/06.png)

Klicka på en ikon för att få mer information:

![Azure rekommendation](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Diagnostiska klicka dig igenom
Varje nod på kartan erbjuder riktade klicka dig igenom för diagnostik. Alternativen varierar beroende på noden.

![Serveralternativ](./media/app-insights-app-map/09.png)

Bland alternativen ingår Direktlänkar till dem för komponenter som finns i Azure.

## <a name="filters-and-time-range"></a>Filter och tidsintervall
Som standard sammanfattar kartan alla data som är tillgängliga för det valda tidsintervallet. Men du kan filtrera den om du vill inkludera endast specifika åtgärden namn eller beroenden.

* Åtgärdsnamn: Detta inkluderar både sidvisningar och typer av begäranden på serversidan. Med det här alternativet visar kartan KPI på server-klientsidan noden för de valda åtgärderna. Den visar de beroenden som anropas i kontexten för dessa specifika åtgärder.
* Beroende basnamn: AJAX webbläsare beroenden och beroenden för serversidan. Om du rapporterar anpassade beroendetelemetri med TrackDependency API visas de också här. Du kan välja beroenden som ska visas på kartan. Det här urvalet filtrera för närvarande inte serversidan-förfrågningar, eller på klientsidan sidvisningar.

![Ange filter](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Spara filter
Om du vill spara filter som du har använt fästa den filtrerade vyn till en [instrumentpanelen](app-insights-dashboards.md).

![Fäst vid instrumentpanelen](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Fel-fönstret
När du klickar på en nod i kartan visas ett fel fönstret till höger som sammanfattar fel för noden. Fel grupperas först efter åtgärds-ID och sedan grupperade efter problem-ID.

![Fel-fönstret](./media/app-insights-app-map/error-pane.png)

Klicka på ett fel går du till den senaste instansen av att fel.

## <a name="resource-health"></a>Resurshälsa
För vissa typer av resurser visas resurshälsa överst i felfönstret. Till exempel visas klickar på en SQL-nod databasen hälsa och eventuella aviseringar som har aktiverats.

![Resurshälsa](./media/app-insights-app-map/resource-health.png)

Du kan klicka på resursnamnet att visa standard översikt måtten för resursen.

## <a name="end-to-end-system-app-maps"></a>Slutpunkt till slutpunkt system app maps

*Kräver SDK version 2.3 eller senare*

Om programmet har flera komponenter – till exempel en backend-tjänst dessutom till webb-app - så att du kan visa dem på en inbyggd app-karta.

![Ange filter](./media/app-insights-app-map/multi-component-app-map.png)

App-kartan hittar servernoder genom att följa alla HTTP-beroendeanrop mellan servrar med Application Insights SDK installerad. Varje Application Insights-resurs förväntas innehålla en server.

### <a name="multi-role-app-map-preview"></a>Flera rollen app karta (förhandsgranskning)

Förhandsgranskningsfunktion flera rollen app kartan kan du använda app-karta med flera servrar som skickar data till samma Application Insights-resurs / instrumentation nyckel. Servrar i kartan segmenterade av egenskapen cloud_RoleName på telemetri objekt. Ange *flera rollen programavbildningen* till *på* från förhandsversioner bladet för att aktivera den här konfigurationen.

Den här metoden kan det vara önskvärt i ett micro-services-program, eller i andra scenarier där du vill matcha händelser över flera servrar i en enda Application Insights-resurs.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Feedback
Lämna feedback via portalen feedback-alternativet.

![MapLink-1-bild](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Nästa steg

* [Azure Portal](https://portal.azure.com)
