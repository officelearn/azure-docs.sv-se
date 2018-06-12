---
title: Programavbildningen i Azure Application Insights | Microsoft Docs
description: Övervaka komplexa program topologier med program-karta
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.reviewer: Soubhagya.Dash
ms.author: mbullwin
ms.openlocfilehash: 539becf272194a116355c6a0491042d40e1e7494
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293970"
---
# <a name="application-map-triage-distributed-applications"></a>Programavbildningen: Prioritering distribuerade program
Programavbildningen hjälper dig att upptäcka flaskhalsar eller fel surfpunkter för alla komponenter i det distribuerade programmet. Varje nod på kartan representerar en programkomponent eller dess beroenden; har hälsa KPI och aviseringar status. Du kan klicka på via från valfri komponent till mer detaljerad diagnostik, till exempel Application Insights händelser. Om din app använder Azure-tjänster, kan du också klicka via Azure Diagnostics, till exempel SQL Database Advisor-rekommendationer.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenter är oberoende av varandra distribuerbara delar i tillämpningsprogrammet distribueras/mikrotjänster. Utvecklare och åtgärder team har kod-nivå eller tillgång till telemetri som genereras av dessa programkomponenter. 

* Komponenter som skiljer sig från ”observerade” externa beroenden, till exempel SQL, EventHub etc. som team/organisationen inte kanske har åtkomst till (kod eller telemetri).
* Komponenterna köras i valfritt antal instanser för server-roll-behållare.
* Komponenter kan vara separat Application Insights instrumentation nycklar (även om prenumerationer är olika) eller olika roller som rapporterar till en enda Application Insights instrumentation nyckel. Förhandsgranska kartan upplevelsen visar komponenterna oavsett hur de är konfigurerade.

## <a name="composite-application-map-preview"></a>Sammansatta programavbildningen (förhandsgranskning)
*Detta är en tidig förhandsgranskning och vi kommer att lägga till fler funktioner till den här kartan. Vi vill gärna höra dina synpunkter på den nya upplevelsen. Du kan växla mellan förhandsgranskning och klassiska upplevelser enkelt.*

Aktivera ”sammansatta programavbildningen” från den [förhandsgranskningar listan](app-insights-previews.md), eller klicka på ”Preview karta” i växla i det övre högra hörnet. Du kan använda den här växla för att växla tillbaka till den klassiska upplevelsen.
![Aktivera preview karta](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
Den här förhandsgranskningen ersätter tidigare ”flera rollen programavbildningen” förhandsgranskningen. För tillfället används för att visa hela topologi i flera nivåer av komponenten programberoenden. Ge oss dina synpunkter, vi kommer att lägga till flera funktioner som liknar klassiska kartan stöder.

Du kan se fullständiga programmet topologi i flera nivåer av relaterade programkomponenter. Komponenter kan vara olika Application Insights-resurser eller olika roller i en enskild resurs. App-kartan hittar komponenter genom följande anrop för HTTP-beroendet mellan servrar med Application Insights SDK installerad. 

Det här upplevelsen börjar med progressiv identifiering av komponenter. När du först läsa in förhandsgranskningen utlöses en uppsättning frågor för att identifiera de komponenter som hör till den här komponenten. En knapp i det övre vänstra hörnet uppdateras med antalet komponenter i ditt program eftersom de identifieras. 
![Förhandsgranska karta](media/app-insights-app-map/preview.png)

När du klickar på ”Uppdatera kartan komponenter” uppdateras kartan med alla komponenter som identifierats tills som pekar.
![Förhandsgranska inlästa karta](media/app-insights-app-map/components-loaded-hierarchical.png)

Den här identifieringen steget är inte nödvändigt om alla komponenter är roller i en enda Application Insights-resurs. Den inledande inläsningen för ett sådant program har alla dess komponenter.

En av de viktiga mål med den nya upplevelsen ska visualisera komplexa topologier med hundratals komponenter. Den nya upplevelsen stöder zoomning och lägger till information som du zooma in. Du kan zooma ut om du vill visa flera komponenter i korthet och fortfarande plats komponenter med högre fel priser. 

![Zooma nivåer](media/app-insights-app-map/zoom-levels.png)

Klicka på någon komponent Se relaterade insikter och gå till prestanda och fel prioritering upplevelse för den komponenten.

![Utfällbar](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>Klassiska programavbildningen

Kartan visar:

* Tillgänglighetstester
* Klient-komponenten (övervakas med JavaScript SDK)
* Server-sida-komponent
* Beroenden för klient- och serverkomponenter

![App-karta](./media/app-insights-app-map/02.png)

Du kan expandera och komprimera beroende länken grupper:

![komprimera](./media/app-insights-app-map/03.png)

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

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Feedback
Lämna feedback via portalen feedback-alternativet.

![MapLink-1-bild](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Nästa steg

* [Azure Portal](https://portal.azure.com)
