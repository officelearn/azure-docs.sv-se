---
title: Programkarta i Azure Application Insights | Microsoft Docs
description: Övervaka komplexa programtopologier med programkartan
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
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 9b39eef5accec4764f61ab31dd894d368242ee3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094658"
---
# <a name="application-map-triage-distributed-applications"></a>Programavbildning: Hantera distribuerade program

Programavbildning hjälper dig att upptäcka flaskhalsar eller fel-anslutningar för alla komponenter i det distribuerade programmet. Varje nod på kartan representerar en programkomponent eller dess beroenden; och har hälsotillstånd KPI och varnar status. Du kan klicka vidare från valfri komponent till mer detaljerad analys, till exempel Application Insights-händelser. Om appen använder Azure-tjänster kan även klicka vidare till Azure-diagnostik, till exempel SQL Database Advisor-rekommendationer.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenterna är oberoende av varandra distribuerbar delar av programmet distribueras/mikrotjänster. Utvecklare och åtgärder team har kod-nivå eller åtkomst till telemetri som genereras av dessa programkomponenter. 

* Komponenter skiljer sig från ”observerade” externa beroenden, till exempel SQL, EventHub etc. som ditt team/organisation inte kanske har åtkomst till (kod eller telemetri).
* Komponenter som körs på valfritt antal instanser av server-rollen-behållare.
* Komponenterna kan vara olika Application Insights-instrumenteringsnycklar (även om prenumerationer skiljer sig) eller olika roller som rapporterar till en enda Application Insights-instrumenteringsnyckeln. Förhandsgranskningsupplevelsen för kartan visar komponenterna oavsett hur de är konfigurerade.

## <a name="composite-application-map"></a>Sammansatt programkarta

Du kan se hela programmets topologi över flera nivåer av relaterade programkomponenter. Komponenterna kan vara olika Application Insights-resurser eller olika roller i en enskild resurs. Programkartan hittar komponenter av följande HTTP-beroendeanrop mellan servrar med Application Insights SDK installerad. 

Den här upplevelsen börjar med progressiv identifiering av komponenter. När du först läser in programkartan utlöses en uppsättning frågor för att identifiera de komponenter som hör till den här komponenten. En knapp i det övre vänstra hörnet uppdateras med antalet komponenter i ditt program när de upptäcks. 

När du klickar på ”Uppdatera kartkomponenter”, uppdateras kartan med alla komponenter som identifieras förrän som pekar.

Den här identifieringssteget är inte obligatoriskt om alla komponenter är roller i en enda Application Insights-resurs. Den initiala inläsningen för ett sådant program kommer att ha alla dess komponenter.

![Skärmbild av programmet karta](media/app-insights-app-map/001.png)

En av de viktiga mål med den här upplevelsen är för att kunna visualisera komplexa topologier med hundratals komponenter.

Klicka på någon komponent för att se relaterade insikter och gå till den prestanda och fel prioritering upplevelse för respektive komponent.

![Utfällt](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>Undersök fel

Välj **Undersök fel** att starta fönstret fel.

![Skärmbild av undersöka fel-knappen](media/app-insights-app-map/investigate-failures.png)

![Skärmbild av fel-upplevelsen](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>Undersök prestanda

Felsökning av problem väljer du prestanda **Undersök prestanda**

![Skärmbild av Undersök prestanda-knappen](media/app-insights-app-map/investigate-performance.png)

![Skärmbild av prestandaupplevelse](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>Gå till information

Välj **går du till information om** att utforska transaktion slutpunkt till slutpunkt-upplevelse som kan erbjuda vyer som klar för att anropa stack-nivå.

![Skärmbild av knappen Gå till information](media/app-insights-app-map/go-to-details.png)

![Skärmbild av transaktionsinformation för slutpunkt till slutpunkt](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Visa i analys

Fråga och undersöka program data ytterligare klickar på **visa i analys**.

![Skärmbild av vyn i knappen analytics](media/app-insights-app-map/view-in-analytics.png)

![Skärmbild av analytics](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>Aviseringar

Om du vill visa aktiva varningar och de underliggande reglerna som orsakar att aviseringarna ska tiggered, Välj **aviseringar**.

![Skärmbild av knappen för aviseringar](media/app-insights-app-map/alerts.png)

![Skärmbild av analytics](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Feedback
Ge feedback via portalen feedback-alternativet.

![MapLink-1-bild](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>Nästa steg

* [Azure Portal](https://portal.azure.com)
