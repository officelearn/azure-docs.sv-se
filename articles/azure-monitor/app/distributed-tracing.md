---
title: Distribuerad spårning i Azure Application Insights | Microsoft Docs
description: Innehåller information om Microsofts support för distribuerad spårning genom våra lokala vidarebefordrare och samarbete inom OpenCensus-projekt
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7bc04748f2a5b8caa8f589140dd46f0650b7b390
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898856"
---
# <a name="what-is-distributed-tracing"></a>Vad är distribuerad spårning?

Ankomsten av moderna molnet och [mikrotjänster](https://azure.com/microservices) arkitekturer har gett upphov till enkla, oberoende av varandra distribuerbar tjänster som kan hjälpa dig att minska kostnaderna samtidigt som de ökar tillgängligheten och dataflöde. Men även om dessa förflyttningar har gjort enskilda tjänster som är lättare att förstå hela, de har gjort övergripande system svårare att anledning om och felsöka.

I monolitisk arkitekturer har vi tagit emot för att felsöka med anropsstackar. Anropsstackar är fantastiska verktyg för att visa flödet av körningen (Method A anropade metoden B, som kallas metoden C), tillsammans med detaljer och parametrar om var och en av dessa anrop. Det här är bra för monoliter bryts eller tjänster som körs på en enda process, men hur vi Felsöka när anropet är för en gräns för processen, inte bara en referens i den lokala stacken? 

Det är där distribuerad spårning kommer in.  

Distribuerad spårning motsvarar anropsstackar för moderna molnet och arkitektur för mikrotjänster, med hjälp av en förenklad prestanda-profiler som har utlösts i. Azure Monitor innehåller två upplevelser för att använda distribuerade spårningsdata. Först är vår [transaktionsdiagnostik](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) vy, som fungerar som en anropsstack med en time-dimension som har lagts till i. Vyn transaction diagnostics ger insyn i en enda transaktion/förfrågan och är användbart för att söka efter de grundläggande orsakerna till problem med tillförlitlighet och prestandaflaskhalsar på varje begäran.

Azure Monitor erbjuder även en [programkartan](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) som aggregerar många transaktioner för att visa en topologisk vy över hur system som interagerar, och de genomsnittliga prestanda- och Felfrekvens. 

## <a name="how-to-enable-distributed-tracing"></a>Så här aktiverar du distribuerad spårning

Att aktivera distribuerad spårning tjänster i ett program är lika enkelt som att lägga till rätt SDK eller bibliotek som ska varje tjänst, baserat på det språk som tjänsten har implementerats i.

## <a name="enabling-via-application-insights-sdks"></a>Aktivera via Application Insights SDK: er

Application Insights SDK för .NET, .NET Core, Java, Node.js och JavaScript alla stöder internt distribuerad spårning. Instruktioner för installation och konfiguration av varje Application Insights SDK finns nedan:

* [NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

Med rätt Application Insights SDK installerats och konfigurerats, samlas automatiskt spårningsinformation för populära ramverk, bibliotek och tekniker med automatisk SDK-beroende-insamlare. En fullständig lista över stödda tekniker finns i [i dokumentationen för automatisk insamling av beroende](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Dessutom teknik kan spåras manuellt med ett anrop till [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) på den [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Aktivera via OpenCensus

Förutom SDK: er för Application Insights stöder Application Insights även distribuerad spårning via [OpenCensus](https://opencensus.io/). OpenCensus är en öppen källkod, leverantörsoberoende enkel distribution av bibliotek för mått-samling och distribuerad spårning för tjänster. Det gör också att communityn för öppen källkod att aktivera distribuerad spårning med populära teknikerna som Redis Memcached eller MongoDB. [Microsoft samarbetar på OpenCensus med flera andra övervaknings- och cloud partner](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Att lägga till funktioner för distribuerad spårning till ett program med OpenCensus, först [installera och konfigurera Application Insights lokala vidarebefordrare](./../../azure-monitor/app/opencensus-local-forwarder.md). Därifrån kan du konfigurera OpenCensus för att dirigera distribuerade spårningsdata via lokala vidarebefordraren. Båda [Python](./../../azure-monitor/app/opencensus-python.md) och [Gå](./../../azure-monitor/app/opencensus-go.md) stöds.

Webbplatsen OpenCensus underhåller API-referensdokumentation för [Python](https://opencensus.io/api/python/trace/usage.html) och [Gå](https://godoc.org/go.opencensus.io), samt som olika olika guider för att använda OpenCensus. 

## <a name="next-steps"></a>Nästa steg

* [OpenCensus Python-användningsguiden](https://opencensus.io/api/python/trace/usage.html)
* [Programkarta](./../../azure-monitor/app/app-map.md)
* [Prestandaövervakning för slutpunkt till slutpunkt](./../../azure-monitor/learn/tutorial-performance.md)
