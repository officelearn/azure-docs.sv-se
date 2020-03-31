---
title: Övervakning av programnivå för Azure Service-program
description: Lär dig mer om händelser och loggar på program- och tjänstnivå som används för att övervaka och diagnostisera Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464717"
---
# <a name="application-logging"></a>Programloggning

Instrumentering din kod är inte bara ett sätt att få insikter om dina användare, men också det enda sättet du kan veta om något är fel i ditt program, och att diagnostisera vad som behöver åtgärdas. Även om det tekniskt sett är möjligt att ansluta en felsökning till en produktionstjänst, är det inte vanligt. Så att ha detaljerade instrumenteringsdata är viktigt.

Vissa produkter instrumenterar automatiskt din kod. Även om dessa lösningar kan fungera bra, är manuell instrumentering nästan alltid krävs för att vara specifik för din affärslogik. I slutändan måste du ha tillräckligt med information för att kriminaltekniskt felsöka programmet. Service Fabric-applikationer kan instrumenteras med alla loggningsramverk. I det här dokumentet beskrivs några olika sätt att instrumentera koden och när du ska välja en metod framför en annan. 

Exempel på hur du använder de här förslagen finns i [Lägga till loggning i ditt Service Fabric-program](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Programinsikter SDK

Application Insights har en omfattande integration med Service Fabric ur lådan. Användare kan lägga till AI Service Fabric nuget paket och ta emot data och loggar som skapats och samlats in visas i Azure-portalen. Dessutom uppmanas användare att lägga till sin egen telemetri för att diagnostisera och felsöka sina program och spåra vilka tjänster och delar av deras program som används mest. Klassen [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) i SDK innehåller många sätt att spåra telemetri i dina program. Kolla in ett exempel på hur du instrumenterar och lägger till programinsikter till din ansökan i vår handledning för [att övervaka och diagnostisera en .NET-applikation](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource (EventSource)

När du skapar en Service Fabric-lösning från **EventSource**en mall i Visual Studio genereras en EventSource-härledd klass **(ServiceEventSource** eller **ActorEventSource).** En mall skapas där du kan lägga till händelser för ditt program eller din tjänst. **Namnet EventSource** **måste** vara unikt och bör byta namn från standardmallsträngen MyCompany-&lt;lösningsprojekt&gt;-&lt;&gt;. Om du har flera **EventSource-definitioner** som använder samma namn uppstår ett problem vid körning. Varje definierad händelse måste ha en unik identifierare. Om en identifierare inte är unik inträffar ett körningsfel. Vissa organisationer förtillvisar intervall med värden för identifierare för att undvika konflikter mellan separata utvecklingsteam. Mer information finns i [Vances blogg](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) eller [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET Core-loggning

Det är viktigt att noggrant planera hur du kommer instrument din kod. Rätt instrumenteringsplan kan hjälpa dig att undvika att eventuellt destabilisera din kodbas och sedan behöva reinstrument koden. För att minska riskerna kan du välja ett instrumenteringsbibliotek som [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), som är en del av Microsoft ASP.NET Core. ASP.NET Core har ett [ILogger-gränssnitt](/dotnet/api/microsoft.extensions.logging.ilogger) som du kan använda med valfri leverantör, samtidigt som effekten på befintlig kod minimeras. Du kan använda koden i ASP.NET Core på Windows och Linux, och i hela .NET Framework, så att din instrumenteringskod är standardiserad.

## <a name="next-steps"></a>Nästa steg

När du har valt din loggningsleverantör för att instrumentera dina program och tjänster måste dina loggar och händelser aggregeras innan de kan skickas till någon analysplattform. Läs om [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) och [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) för att bättre förstå några av de rekommenderade alternativen för Azure Monitor.
