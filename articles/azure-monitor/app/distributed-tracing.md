---
title: Distribuerad spårning i Azure Application Insights | Microsoft Docs
description: Innehåller information om Microsofts support för distribuerad spårning via vår lokala vidarebefordrare och partnerskap i projektet för openinventering
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 49e61a7677007c52d1a584c4b49ccaadc3a02df3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669598"
---
# <a name="what-is-distributed-tracing"></a>Vad är distribuerad spårning?

Ankomsten av moderna moln-och [mikrotjänster](https://azure.com/microservices) -arkitekturer har gett upphov till enkla, oberoende, oberoende tjänster som kan hjälpa till att minska kostnaderna samtidigt som tillgänglighet och data flöde ökar. Men även om dessa transporter har gjort enskilda tjänster lättare att förstå som helhet, har de gjort övergripande system svårare att ta reda på och felsöka.

I monolitisk-arkitekturer har vi använt för att felsöka anrops stackar. Anrops stackar är klara verktyg för att Visa körnings flödet (metod A kallas metod B, som kallas metod C), tillsammans med information och parametrar om var och en av dessa anrop. Detta är perfekt för Monoliths eller tjänster som körs på en enda process, men hur gör vi för att felsöka när anropet sker över en process gränser, inte bara en referens på den lokala stacken? 

Det är där den distribuerade spårningen kommer in.  

Distributed tracing är motsvarigheten till anrops stackar för moderna moln-och mikrotjänster-arkitekturer, med tillägg av en förenklad prestanda profilering i. I Azure Monitor tillhandahåller vi två upplevelser för att konsumera distribuerade spårnings data. Den första är vår vy för [transaktions-diagnostik](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) , som är som en anrops stack med en tids dimension som lagts till i. Vyn transaktions-diagnostik ger insyn i en enda transaktion/begäran och är användbar för att hitta rotor saken till problem med tillförlitlighet och prestanda Flask halsar per begäran.

Azure Monitor erbjuder också en vy över [program översikt](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) som sammanställer många transaktioner för att visa en Top ologiska vy över hur systemen interagerar och vilka genomsnitts prestanda och fel frekvens som är. 

## <a name="how-to-enable-distributed-tracing"></a>Aktivera distribuerad spårning

Att aktivera distribuerad spårning mellan tjänsterna i ett program är lika enkelt som att lägga till rätt SDK eller bibliotek i varje tjänst, baserat på vilket språk tjänsten implementerades i.

## <a name="enabling-via-application-insights-sdks"></a>Aktivera via Application Insights SDK: er

Application Insights SDK: er för .NET, .NET Core, Java, Node. js och Java Script all stöder distribuerad spårning internt. Instruktioner för att installera och konfigurera varje Application Insights SDK finns nedan:

* [NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python (för hands version)](opencensus-python.md)

När du har installerat och konfigurerat rätt Application Insights SDK samlas spårnings information automatiskt in för populära ramverk, bibliotek och tekniker med SDK-beroenden automatiskt insamlare. En fullständig lista över tekniker som stöds finns i [den beroende automatiska insamlings dokumentationen](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Dessutom kan all teknik spåras manuellt med ett anrop till [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) på [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Aktivera via openräkning

Utöver Application Insights SDK: er Application Insights även stöd för distribuerad spårning via [openräkning](https://opencensus.io/). Openräkning är en öppen källkod, en oberoende, en enda distribution av bibliotek, som tillhandahåller statistik insamling och distribuerad spårning för tjänster. Den gör det också möjligt för communityn för öppen källkod att aktivera distribuerad spårning med populära tekniker som Redis, memcached eller MongoDB. [Microsoft samarbetar om openräkning med flera andra övervaknings-och moln partner](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python (för hands version)](opencensus-python.md) 

Webbplatsen för opensite innehåller API-referens dokumentation för [python](https://opencensus.io/api/python/trace/usage.html) och [Go](https://godoc.org/go.opencensus.io), samt olika guider för att använda openräkning. 

## <a name="next-steps"></a>Nästa steg

* [Användnings guide för openräkning python](https://opencensus.io/api/python/trace/usage.html)
* [Program karta](./../../azure-monitor/app/app-map.md)
* [Prestanda övervakning från slut punkt till slut punkt](./../../azure-monitor/learn/tutorial-performance.md)
