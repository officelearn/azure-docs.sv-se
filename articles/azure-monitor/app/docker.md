---
title: Övervaka Docker-program i Azure Application Insights | Microsoft Docs
description: Docker perf-räknare, händelser och undantag kan visas på Application Insights, tillsammans med telemetri från appar i behållare.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 66a2481d25c863bbdbf4d72c4683a309918776db
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677928"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Övervaka Docker-program i Application Insights (inaktuell)

> [!NOTE]
> Den här lösningen är föråldrad. Om du vill veta mer om våra aktuella investeringar i behållar övervakning rekommenderar vi att du checkar ut [Azure Monitor för behållare](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

Livs cykel händelser och prestanda räknare från [Docker](https://www.docker.com/) -behållare kan visas i diagram på Application Insights. Installera [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) avbildningen i en behållare på värden och visar prestanda räknare för värden, samt för de andra avbildningarna.

Med Docker distribuerar du dina appar i enkla behållare som slutförs med alla beroenden. De körs på alla värddatorer som kör en Docker-motor.

När du kör [Application Insights avbildningen](https://hub.docker.com/r/microsoft/applicationinsights/) på Docker-värden får du följande fördelar:

* Telemetri för livscykler om alla behållare som körs på värd Start, stopp och så vidare.
* Prestanda räknare för alla behållare. PROCESSOR, minne, nätverks användning och mycket annat.
* Om du har [installerat Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md) i apparna som körs i behållarna, kommer all telemetri för dessa appar att ha ytterligare egenskaper som identifierar behållaren och värddatorn. Om du till exempel har instanser av en app som körs på fler än en värd kan du enkelt filtrera din app-telemetri efter värd.

## <a name="set-up-your-application-insights-resource"></a>Konfigurera din Application Insights-resurs

1. Logga in på [Microsoft Azure-portalen](https://azure.com) och öppna Application Insights resurs för din app. eller [skapa en ny](../../azure-monitor/app/create-new-resource.md ). 
   
    *Vilken resurs ska jag använda?* Om de appar som du kör på värden har utvecklats av någon annan måste du [skapa en ny Application Insights-resurs](../../azure-monitor/app/create-new-resource.md ). Här kan du Visa och analysera telemetri. (Välj Allmänt för typ av app.)
   
    Men om du är utvecklare av appar hoppas vi att du [har lagt till Application Insights SDK](../../azure-monitor/app/java-get-started.md) till var och en av dem. Om de verkligen är komponenter i ett enda affärs program kan du konfigurera alla som ska skicka telemetri till en resurs, och du använder samma resurs för att Visa Docker-livscykel och prestanda data. 
   
    Ett tredje scenario är att du har utvecklat de flesta appar, men du använder separata resurser för att visa sin telemetri. I så fall vill du förmodligen också skapa en separat resurs för Docker-data.

2. Klicka på list rutan **Essentials** och kopiera Instrumentation-tangenten. Du kan använda detta för att berätta för SDK var du ska skicka sin telemetri.

Se till att webbläsarfönstret är praktiskt, precis som du kommer tillbaka till det så snart som möjligt att titta på din telemetri.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Kör Application Insights övervakare på värden

Nu när du har lärt dig att Visa Telemetrin kan du konfigurera den behållare som ska samla in och skicka den.

1. Anslut till Docker-värden.
2. Redigera Instrumentation-nyckeln i det här kommandot och kör den:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Endast en Application Insights avbildning krävs per Docker-värd. Om ditt program har distribuerats på flera Docker-värdar upprepar du kommandot på varje värd.

## <a name="update-your-app"></a>Uppdatera din app
Om ditt program är instrumenterat med [Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md)lägger du till följande rad i filen ApplicationInsights. xml i projektet, under elementet `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Detta lägger till Docker-information, till exempel container-och värd-ID till varje telemetri-objekt som skickas från din app.

## <a name="view-your-telemetry"></a>Visa telemetrin
Gå tillbaka till Application Insights resursen i Azure Portal.

Klicka på den Docker panelen.

Du kommer snart att se data som kommer från Docker-appen, särskilt om du har andra behållare som körs på Docker-motorn.

### <a name="docker-container-events"></a>Docker-behållar händelser
![Exempel](./media/docker/13.png)

Om du vill undersöka enskilda händelser klickar du på [Sök](../../azure-monitor/app/diagnostic-search.md). Sök och filtrera efter de händelser som du vill ha. Klicka på en händelse om du vill ha mer information.

### <a name="exceptions-by-container-name"></a>Undantag per behållar namn
![Exempel](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Docker-kontext har lagts till i app-telemetri
Telemetri för begäran som skickas från det program som är instrumenterat med AI SDK, är omfattande med Docker context-information.

## <a name="q--a"></a>Frågor och svar
*Vad kan Application Insights ge mig som jag inte kan få från Docker?*

* Detaljerad nedbrytning av prestanda räknare per behållare och avbildning.
* Integrera behållare och AppData på en instrument panel.
* [Exportera telemetri](export-telemetry.md) för ytterligare analys till en databas, Power BI eller annan instrument panel.

*Hur gör jag för att hämta telemetri från själva appen?*

* Installera Application Insights SDK i appen. Lär dig mer om: [Java-webbappar](../../azure-monitor/app/java-get-started.md), [Windows Web Apps](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg

* [Application Insights för Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights för Node. js](../../azure-monitor/app/nodejs.md)
* [Application Insights för ASP.NET](../../azure-monitor/app/asp-net.md)
