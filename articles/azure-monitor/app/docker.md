---
title: Övervaka Docker-program i Azure Application Insights | Microsoft-dokument
description: Docker perf-räknare, händelser och undantag kan visas på Application Insights, tillsammans med telemetrin från de behållarapparna.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5ff3d6a2b31425a3fb6857c8c6aed45f7ef18d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669615"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Övervaka Docker-program i Application Insights (inaktuella)

> [!NOTE]
> Denna lösning har inaktuell. Om du vill veta mer om våra aktuella investeringar i containerövervakning rekommenderar vi att du checkar ut [Azure Monitor för behållare](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

Livscykelhändelser och prestandaräknare från [Docker-behållare](https://www.docker.com/) kan kartläggas på Application Insights. Installera [application insights-avbildningen](https://hub.docker.com/r/microsoft/applicationinsights/) i en behållare i värden, och den visar prestandaräknare för värden, liksom för de andra bilderna.

Med Docker distribuerar du dina appar i lätta behållare komplett med alla beroenden. De kommer att köras på alla värdmaskiner som kör en Docker Engine.

När du kör [application insights-avbildningen](https://hub.docker.com/r/microsoft/applicationinsights/) på Docker-värden får du följande fördelar:

* Livscykeltelemetri om alla behållare som körs på värden - start, stopp och så vidare.
* Prestandaräknare för alla behållare. CPU, minne, nätverksanvändning med mera.
* Om du [har installerat Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md) i apparna som körs i behållarna, kommer all telemetri för dessa appar att ha ytterligare egenskaper som identifierar behållaren och värddatorn. Om du till exempel har instanser av en app som körs i mer än en värd kan du enkelt filtrera din apptelemetri efter värd.

## <a name="set-up-your-application-insights-resource"></a>Konfigurera din application insights-resurs

1. Logga in på [Microsoft Azure-portalen](https://azure.com) och öppna application insights-resursen för din app. eller [skapa en ny](../../azure-monitor/app/create-new-resource.md ). 
   
    *Vilken resurs ska jag använda?* Om de appar som du kör på värden har utvecklats av någon annan måste du [skapa en ny Application Insights-resurs](../../azure-monitor/app/create-new-resource.md ). Här kan du visa och analysera telemetrin. (Välj "Allmänt" för apptypen.)
   
    Men om du är utvecklare av apparna hoppas vi att du [har lagt till Application Insights SDK](../../azure-monitor/app/java-get-started.md) i var och en av dem. Om de är alla verkligen komponenter i ett enda affärsprogram kan du konfigurera dem alla för att skicka telemetri till en resurs, och du använder samma resurs för att visa Dockers livscykel- och prestandadata. 
   
    Ett tredje scenario är att du har utvecklat de flesta av apparna, men du använder separata resurser för att visa deras telemetri. I så fall vill du förmodligen också skapa en separat resurs för Docker-data.

2. Klicka på listrutan **Essentials** och kopiera instrumenteringsnyckeln. Du använder detta för att tala om för SDK var dess telemetri ska skickas.

Håll webbläsarfönstret till hands, eftersom du kommer tillbaka till det snart för att titta på din telemetri.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Kör programstatistikövervakaren på din värd

Nu när du har någonstans att visa telemetri, kan du ställa in containerized app som kommer att samla in och skicka den.

1. Anslut till Docker-värden.
2. Redigera instrumenteringsnyckeln i det här kommandot och kör den sedan:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Endast en Application Insights-avbildning krävs per Docker-värd. Om ditt program distribueras på flera Docker-värdar upprepar du kommandot på varje värd.

## <a name="update-your-app"></a>Uppdatera din app
Om programmet är instrumenterat med [Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md)lägger du till följande rad i `<TelemetryInitializers>` filen ApplicationInsights.xml i projektet under elementet:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Detta lägger docker-information till exempel behållar- och värd-ID i varje telemetriobjekt som skickas från din app.

## <a name="view-your-telemetry"></a>Visa telemetrin
Gå tillbaka till din Application Insights-resurs i Azure-portalen.

Klicka dig igenom Docker-panelen.

Du kommer inom kort att se data som kommer från Docker-appen, särskilt om du har andra behållare som körs på Docker-motorn.

### <a name="docker-container-events"></a>Docker-containerhändelser
![Exempel](./media/docker/13.png)

Om du vill undersöka enskilda händelser klickar du på [Sök](../../azure-monitor/app/diagnostic-search.md). Sök och filtrera för att hitta de händelser du vill ha. Klicka på en händelse för att få mer information.

### <a name="exceptions-by-container-name"></a>Undantag efter behållarnamn
![Exempel](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Docker-kontext som lagts till i apptelemetri
Begär telemetri som skickas från programmet som är instrumenterat med AI SDK, är berikad med Docker-kontextinformation.

## <a name="q--a"></a>Vanliga frågor och svar
*Vad ger Application Insights mig som jag inte kan få från Docker?*

* Detaljerad uppdelning av prestandaräknare efter behållare och bild.
* Integrera behållar- och appdata i en instrumentpanel.
* [Exportera telemetri](export-telemetry.md) för vidare analys till en databas, Power BI eller annan instrumentpanel.

*Hur får jag telemetri från själva appen?*

* Installera SDK för programinsikter i appen. Läs mer om: [Java-webbappar](../../azure-monitor/app/java-get-started.md), [Windows webbappar](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg

* [Programinsikter för Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights för Node.js](../../azure-monitor/app/nodejs.md)
* [Programinsikter för ASP.NET](../../azure-monitor/app/asp-net.md)
