---
title: Övervaka Docker-program i Azure Application Insights | Microsoft Docs
description: Prestandaräknare för docker, händelser och undantag kan visas på Application Insights, tillsammans med telemetri från behållarbaserade appar.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: mbullwin
ms.openlocfilehash: a4e4ca1ca4878a2a405b12413e4378a2cb79aef6
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999126"
---
# <a name="monitor-docker-applications-in-application-insights"></a>Övervaka Docker-program i Application Insights

Livscykelhändelser och prestanda-räknare från [Docker](https://www.docker.com/) behållare kan vara läggas till på Application Insights. Installera den [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) bild i en behållare i värden och den visar prestandaräknare för värden, samt för andra avbildningar.

Med Docker, kan du distribuera dina appar i Förenklade behållare som är klar med alla beroenden. De ska köras på en värddator som kör en Docker-motor.

När du kör den [Application Insights bild](https://hub.docker.com/r/microsoft/applicationinsights/) på din Docker-värd som du får följande fördelar:

* Livscykel telemetri om alla behållare som körs på värd - starta, stoppa och så vidare.
* Prestandaräknare för alla behållare. Processor, minne, nätverksanvändning och mer.
* Om du [installerat Application Insights SDK för Java](../../azure-monitor/app/java-live.md) i appar som körs i behållare, all telemetri till apparna har ytterligare egenskaper som identifierar den behållare och värd-datorn. Om du har instanser av en app som körs på flera värden, kan du till exempel enkelt filtrera apptelemetrin av värden.

> [!NOTE]
> Den här lösningen är inaktuell. Mer information om våra befintliga investeringar i behållarövervakning rekommenderar vi checka ut [Azure Monitor för behållare](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

## <a name="set-up-your-application-insights-resource"></a>Konfigurera Application Insights-resursen

1. Logga in på [Microsoft Azure-portalen](https://azure.com) och öppna Application Insights-resurs för din app; eller [skapa en ny](../../application-insights/app-insights-create-new-resource.md). 
   
    *Vilken resurs som ska jag använda?* Om de appar som körs på värden har utvecklats av någon annan, måste du [skapa en ny Application Insights-resurs](../../application-insights/app-insights-create-new-resource.md). Detta kan du visa och analysera telemetri. (Välj Allmänt för typ av app).
   
    Men om du utvecklar appar, så vi hoppas att du [har lagts till Application Insights SDK](../../azure-monitor/app/java-live.md) dem. Om de är alla verkligen komponenter i ett enda affärsprogram, du kan konfigurera alla för att skicka telemetri till en resurs och du använder den samma resursen för att visa data för Docker livscykel och prestanda. 
   
    Ett tredje scenario är att du har utvecklat de flesta av apparna, men du använder separata resurser för att visa sina telemetri. I så fall kan du förmodligen också vill du skapa en separat resurs för Docker-data.

2. Klicka på den **Essentials** listrutan och kopierar Instrumenteringsnyckeln. Du kan använda detta för att uppmana SDK att skicka dess telemetri.

Hålla webbläsarfönstret till hands när du kommer tillbaka till den snart för att titta på din telemetri.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Köra Övervakaren Application Insights på värden

Nu när du har en plats för att visa telemetri kan konfigurera du appen med behållare som kommer samla in och skicka den.

1. Anslut till din Docker-värd.
2. Redigera din instrumentationsnyckel i det här kommandot och kör den:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Endast en Application Insights-avbildning krävs per Docker-värd. Om programmet distribueras på flera Docker-värdar, upprepar du kommandot på varje värd.

## <a name="update-your-app"></a>Uppdatera din app
Om ditt program är utrustade med det [Application Insights SDK för Java](../../azure-monitor/app/java-get-started.md), lägga till följande rad i filen ApplicationInsights.xml i projektet, under den `<TelemetryInitializers>` element:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Detta lägger till uppgifter om Docker-behållare och värd-id till varje telemetriobjekt som skickas från din app.

## <a name="view-your-telemetry"></a>Visa telemetrin
Gå tillbaka till Application Insights-resursen i Azure-portalen.

Klicka dig igenom Docker-panelen.

Data som kommer från Docker-appen visas inom kort särskilt om du har andra behållare som körs på Docker-motorn.

### <a name="docker-container-events"></a>Händelser för docker-behållare
![Exempel](./media/docker/13.png)

Om du vill undersöka enskilda händelser, klickar du på [Search](../../azure-monitor/app/diagnostic-search.md). Sök och filtrera för att hitta händelser som du vill. Klicka på en händelse om du vill ha mer information.

### <a name="exceptions-by-container-name"></a>Undantag av ett behållarnamn
![Exempel](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Docker-kontext som lagts till i apptelemetri
Begärandetelemetri som skickas från programmet som är utrustade med AI SDK, berikats med Docker kontextinformation.

## <a name="q--a"></a>Frågor och svar
*Vad Application Insights ger mig som jag inte kan hämta från Docker?*

* Detaljer för prestandaräknare genom att behållare och bild.
* Integrera behållare och app-data i en instrumentpanel.
* [Exportera telemetri](export-telemetry.md) för vidare analys till en databas, Power BI eller andra instrumentpanel.

*Hur får jag telemetri från själva appen?*

* Installera Application Insights SDK i appen. Lär dig hur för: [Java-webbappar](../../azure-monitor/app/java-get-started.md), [Windows webbappar](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg

* [Application Insights för Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights för Node.js](../../azure-monitor/app/nodejs.md)
* [Application Insights för ASP.NET](../../azure-monitor/app/asp-net.md)
