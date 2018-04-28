---
title: Övervaka Docker-program i Azure Application Insights | Microsoft Docs
description: Docker prestandaräknarna, händelser och undantag kan visas i Application Insights, tillsammans med telemetri från av appar.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: a0476e2f0bf08f76b45e1342ec38137e46008cb1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-docker-applications-in-application-insights"></a>Övervaka Docker-program i Application Insights
Livscykelhändelser och prestanda prestandaräknare från [Docker](https://www.docker.com/) kan ritas upp behållare på Application Insights. Installera den [Programinsikter](https://hub.docker.com/r/microsoft/applicationinsights/) bilden i en behållare i värden och den visar prestandaräknare för värden och för andra bilder.

Med Docker, kan du distribuera dina appar i lightweight-behållare med alla beroenden. De kan köras på en värddator som kör en Docker-motorn.

När du kör den [Application Insights bild](https://hub.docker.com/r/microsoft/applicationinsights/) på Docker-värden som du får följande fördelar:

* Livscykel telemetri om alla behållare som körs på värd - starta, stoppa och så vidare.
* Prestandaräknare för alla behållare. CPU, minne, nätverksanvändning och mer.
* Om du [installerat Application Insights SDK för Java](app-insights-java-live.md) i appar som körs i behållare, all telemetri för dessa appar har ytterligare egenskaper som identifierar datorn behållare och värden. Om du har instanser av en app som körs i mer än en värddator kan du exempelvis enkelt filtrera din app telemetri av värden.

![Exempel](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Konfigurera Application Insights-resurs
1. Logga in på [Microsoft Azure-portalen](https://azure.com) och öppna Application Insights-resurs för din app, eller [skapa en ny](app-insights-create-new-resource.md). 
   
    *Vilken resurs som ska jag använda?* Om de appar som körs på värden har utvecklats av någon annan, så du behöver [skapar en ny Application Insights-resurs](app-insights-create-new-resource.md). Detta är där du kan visa och analysera telemetrin. (Välj Allmänt för typ av app).
   
    Men om du utvecklar appar, så vi hoppas att du [lagt till Application Insights SDK](app-insights-java-live.md) dem. Om de är alla verkligen komponenter i ett enda affärsprogram du kan konfigurera dem att skicka telemetri till en resurs och du använder samma resursen för att visa data för Docker livscykel och prestanda. 
   
    Ett tredje scenario är att de flesta av apparna som du utvecklat, men du använder separata resurser för att visa sina telemetri. I så fall måste du förmodligen också vill skapa en separat resurs för Docker-data. 
2. Lägga till panelen Docker: Välj **lägga till panelen**Dra panelen Docker från galleriet och klicka sedan på **klar**. 
   
    ![Exempel](./media/app-insights-docker/03.png)

> [!NOTE]
> Översiktsfönstret i Application Insights nu är låst och tillåter inte att lägga till paneler från galleriet. Du kan fortfarande lägga till panelen Docker enligt beskrivningen ovan via instrumentpanelen för Azure-gränssnittet.

3. Klicka på den **Essentials** listrutan och kopiera nyckeln Instrumentation. Du kan använda den som visar SDK var att skicka telemetri om dess.

    ![Exempel](./media/app-insights-docker/02-props.png)

Håll webbläsarfönstret praktiskt när du kommer tillbaka till den snart att titta på din telemetri.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Kör en Application Insights på värden
Nu när du har någonstans för att kunna visa telemetrin kan ställa du in av app som samlar in och skicka den.

1. Ansluta till Docker-värden. 
2. Redigera din instrumentation nyckeln i det här kommandot och kör det:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Endast en Application Insights-avbildning krävs per Docker-värd. Om programmet distribueras på flera Docker-värdar, kör du kommandot på varje värd.

## <a name="update-your-app"></a>Uppdatera ditt program
Om ditt program är försett med den [Application Insights SDK för Java](app-insights-java-get-started.md), Lägg till följande rad i filen ApplicationInsights.xml i projektet, under den `<TelemetryInitializers>` element:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Detta lägger till Docker information, till exempel behållare och värd-id varje telemetri objekt som skickats från din app.

## <a name="view-your-telemetry"></a>Visa telemetrin
Gå tillbaka till Application Insights-resurs i Azure-portalen.

Klicka på via Docker-panelen.

Inom kort visas data som kommer från Docker-appen, särskilt om du har andra behållare som körs på Docker-motorn.

Här är några av de vyer som du kan hämta.

### <a name="perf-counters-by-host-activity-by-image"></a>Prestandaräknarna av värden, aktivitet efter bilden
![Exempel](./media/app-insights-docker/10.png)

![Exempel](./media/app-insights-docker/11.png)

Klicka på värden eller image-namn för mer information.

Klicka på diagram, rutnätet rubriken för att anpassa vyn, eller Använd Lägg till diagram. 

[Mer information om metrics explorer](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>Docker behållaren händelser
![Exempel](./media/app-insights-docker/13.png)

För att undersöka enskilda händelser klickar du på [Sök](app-insights-diagnostic-search.md). Sök och filtrera efter de händelser som du vill. Klicka på alla händelser för att få mer information.

### <a name="exceptions-by-container-name"></a>Undantag av behållarens namn
![Exempel](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Docker-kontexten lades till i appen telemetri
Telemetri som skickas från programmet försett med AI SDK, utökat med Docker kontext för begäran:

![Exempel](./media/app-insights-docker/16.png)

Processortid och tillgängligt minnesprestandaräknare utökat grupperade efter Docker behållarnamn:

![Exempel](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Frågor och svar
*Vad Application Insights mig som jag inte kan hämta från Docker?*

* Specificering av prestandaräknare av behållaren och image.
* Integrera behållare och app-data i en instrumentpanel.
* [Exportera telemetri](app-insights-export-telemetry.md) för ytterligare analys till en databas, Power BI eller andra instrumentpanelen.

*Hur får jag telemetri från själva appen?*

* Installera Application Insights SDK i appen. Lär dig hur för: [Java-webbappar](app-insights-java-get-started.md), [Windows web apps](app-insights-asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg

* [Application Insights för Java](app-insights-java-get-started.md)
* [Application Insights för Node.js](app-insights-nodejs.md)
* [Application Insights för ASP.NET](app-insights-asp-net.md)
