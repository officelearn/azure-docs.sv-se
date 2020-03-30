---
title: Distribuerad spårning i Azure Application Insights | Microsoft-dokument
description: Ger information om Microsofts support för distribuerad spårning genom vårt partnerskap i OpenCensus-projektet
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c2f384370c3ceaf24164e4a27adc05b1a1e1ddf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294963"
---
# <a name="what-is-distributed-tracing"></a>Vad är distribuerad spårning?

Tillkomsten av moderna moln- och [mikrotjänstarkitekturer](https://azure.com/microservices) har gett upphov till enkla, oberoende driftsättstjänster som kan bidra till att minska kostnaderna och samtidigt öka tillgängligheten och dataflödet. Men medan dessa rörelser har gjort enskilda tjänster lättare att förstå som helhet, har de gjort övergripande system svårare att resonera om och felsöka.

I monolitiska arkitekturer har vi vant oss vid felsökning med anropsstackar. Anropsstackar är briljanta verktyg för att visa körningsflödet (metod A som kallas metod B, som kallas metod C), tillsammans med detaljer och parametrar om vart och ett av dessa anrop. Detta är bra för monoliter eller tjänster som körs på en enda process, men hur felsöker vi när anropet är över en processgräns, inte bara en referens på den lokala stacken? 

Det är där distribuerad spårning kommer in.  

Distribuerad spårning motsvarar anropsstaplar för moderna moln- och mikrotjänstarkitekturer, med tillägg av en förenklad prestandaprofilering kastas in. I Azure Monitor tillhandahåller vi två upplevelser för att använda distribuerade spårningsdata. Den första är vår [transaktionsdiagnostikvy,](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) som är som en anropsstack med en tidsdimension som läggs till. Transaktionsdiagnostikvyn ger insyn i en enda transaktion/begäran och är användbar för att hitta orsaken till tillförlitlighetsproblem och flaskhalsar i prestanda per begäran.

Azure Monitor erbjuder också en [programkartvy](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) som sammanställer många transaktioner för att visa en topologisk vy över hur systemen interagerar och vilka genomsnittliga prestanda och felfrekvenser det är. 

## <a name="how-to-enable-distributed-tracing"></a>Aktivera distribuerad spårning

Att aktivera distribuerad spårning över tjänsterna i ett program är lika enkelt som att lägga till rätt SDK eller bibliotek till varje tjänst, baserat på det språk som tjänsten implementerades i.

## <a name="enabling-via-application-insights-sdks"></a>Aktivera via Application Insights SDK:er

Programstatistiken för SDK:er för .NET, .NET Core, Java, Node.js och JavaScript stöder alla distribuerad spårning internt. Instruktioner för att installera och konfigurera varje Program Insights SDK finns nedan:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [Javascript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

Med rätt Application Insights SDK installerat och konfigurerat samlas spårningsinformation automatiskt in för populära ramverk, bibliotek och tekniker av SDK-beroende automatiska samlare. Den fullständiga listan över tekniker som stöds finns i [dokumentationen för automatisk insamling av beroende.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)

 Dessutom kan all teknik spåras manuellt med ett anrop till [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) på [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Aktivera via OpenCensus

Förutom SDK:erna för Application Insights stöder Application Insights även distribuerad spårning via [OpenCensus](https://opencensus.io/). OpenCensus är en öppen källkod, leverantörsagnostiker, enda distribution av bibliotek för att tillhandahålla måttsamling och distribuerad spårning för tjänster. Det gör det också möjligt för öppen källkod att möjliggöra distribuerad spårning med populära tekniker som Redis, Memcached eller MongoDB. [Microsoft samarbetar på OpenCensus med flera andra övervaknings- och molnpartners](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

OpenCensus webbplats underhåller API-referensdokumentation för [Python](https://opencensus.io/api/python/trace/usage.html) och [Go](https://godoc.org/go.opencensus.io), samt olika guider för att använda OpenCensus. 

## <a name="next-steps"></a>Nästa steg

* [Användningsguide för OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Programkarta](./../../azure-monitor/app/app-map.md)
* [Övervakning av prestanda från slutna till slutna dagar](./../../azure-monitor/learn/tutorial-performance.md)
