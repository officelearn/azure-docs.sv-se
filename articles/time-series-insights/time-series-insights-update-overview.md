---
title: 'Översikt: Vad är förhandsversionen av Azure Time Series Insights? - Insikter i Azure-tidsserier | Microsoft-dokument'
description: Lär dig mer om ändringar, förbättringar och funktioner i förhandsversionen av Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 1e4f72300752c93659db4edd4610464dbebf2503
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77014427"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Vad är Azure Time Series Insights (förhandsversion)?

Förhandsversionen av Azure Time Series Insights är ett PaaS-erbjudande (end-to-end platform-as-a-service). Du kan använda den för att samla in, bearbeta, lagra, analysera och fråga data på Sakernas Internet (IoT) skala - data som är mycket kontextualiserade och optimerade för tidsserier. 

Time Series Insights är utformad för ad hoc-datautforskning och operativ analys. Det är ett utbyggbart och anpassat tjänsteerbjudande som uppfyller de breda behoven hos industriella IoT-distributioner.

## <a name="video"></a>Video

Läs mer om förhandsversionen av Azure Time Series Insights.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definition av IoT-data

Industriella IoT-data i tillgångsintensiva organisationer saknar ofta strukturell konsekvens på grund av enheternas och sensorernas varierande karaktär i en industriell miljö. Data från dessa strömmar kännetecknas av betydande luckor och ibland skadade meddelanden och felaktiga avläsningar. IoT-data är ofta meningsfulla i samband med ytterligare dataindata som kommer från första parts eller tredje källor, till exempel CRM eller ERP som lägger till kontext för på arbetsflöden. Indata från datakällor från tredje part, till exempel väderdata, kan bidra till att öka telemetriströmmar i en viss installation. 

Allt detta innebär, bara en bråkdel av data får användas för operativa och affärsändamål, och analys kräver kontextualisering. Industridata är ofta historiciserade för djupgående analys över längre tidsperioder för att förstå och korrelera trender. Omvandlingen av insamlad IoT-data till användbara insikter kräver: 

* Databehandling för att rensa, filtrera, interpolera, transformera och förbereda data för analys.
* En struktur för att navigera genom och förstå data, det vill att normalisera och kontextualisera data.
* Kostnadseffektiv lagring för lång eller oändlig lagring av bearbetade (eller härledda) data och rådata.

Sådana uppgifter ger konsekvent, omfattande, aktuell och korrekt information för affärsanalys och rapportering.

Följande bild visar ett typiskt IoT-dataflöde.

[![IoT-dataflöde](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights för industriell IoT

IoT landskapet är varierat med kunder som spänner över en mängd olika industrisegment, inklusive tillverkning, fordon, energi, verktyg, smarta byggnader och rådgivning. I det här breda utbudet av industriella IoT-lösningar utvecklas fortfarande molnbaserade lösningar som tillhandahåller omfattande analyser som är inriktade på storskaliga IoT-data. 

Azure Time Series Insights tillgodoser det här marknadsbehovet genom att tillhandahålla en nyckelfärdig, heltäckande IoT-analyslösning med omfattande semantisk modellering för kontextualisering av tidsseriedata, tillgångsbaserade insikter och användarupplevelse i bästa klassen för identifiering. trend, avvikelsedetektering och operativ intelligens. 

En omfattande operativ analysplattform i kombination med våra interaktiva datautforskningsfunktioner kan du använda Time Series Insights för att härleda mer värde av data som samlats in från IoT-tillgångar. Förhandsversionserbjudandet stöder: 

* Lagringslösning i flera lager med stöd för varm och kall analys som ger kunderna möjlighet att dirigera data mellan varmt och kallt för interaktiva analyser via varma data samt operativ information under årtionden av historiska data. 

    *   En mycket interaktiv varm analyslösning för att utföra frekventa och ett stort antal frågor över kortare tidsintervalldata 
    *   En skalbar, högpresterande och kostnadsoptimerad tidsseriedatasjö baserad på Azure Storage som gör det möjligt för kunder att trenda år av tidsseriedata på några sekunder. 

* Semantisk modellstöd som beskriver domänen och metadata som är associerade med de härledda och råa signalerna från tillgångar och enheter.

* Flexibel analysplattform för att lagra historiska tidsseriedata i det kundägda Azure Storage-kontot, vilket gör det möjligt för kunder att äga sina IoT-data. Data lagras i Apache Parquet-format med öppen källkod som möjliggör anslutning och interop i en mängd olika datascenarier, inklusive prediktiv analys, maskininlärning och andra anpassade beräkningar som görs med välbekanta tekniker, inklusive Spark, Databricks och Jupyter.

* Omfattande analyser med förbättrade fråge-API:er och användarupplevelse som kombinerar tillgångsbaserade datainsikter med omfattande ad hoc-dataanalys med stöd för interpolation, skalär- och aggregationsfunktioner, kategoriska variabler, punktdiagram och tidsförskjutningstid seriesignaler för djupgående analys.

*   Plattform i företagsklass för att stödja våra IoT-kunders skal-, prestanda-, säkerhets- och tillförlitlighetsbehov.

* Utöknings- och integrationsstöd för heltäckande analyser. Time Series Insights tillhandahåller en utökningsbar analysplattform för en mängd olika datascenarier. Time Series Insights Power BI-anslutning gör det möjligt för kunder att ta de frågor de gör i Time Series Insights direkt i Power BI för att få enhetlig bild av sin BI- och tidsserieanalys i en enda glasruta.

Följande diagram visar dataflödet på hög nivå.

  [![De viktigaste funktionerna](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights tillhandahåller en skalbar prismodell för användningsbaserad betalning för databearbetning, lagring (data och metadata) och fråga, så att kunderna kan justera sin användning så att de passar deras affärsbehov. 
 
Med införandet av dessa viktiga industriella IoT-funktioner ger Time Series Insights också följande viktiga fördelar.  

| | |
| ---| ---|
| Flerskiktad lagring för tidsseriedata i IoT-skala | Med en delad databehandlingspipeline för intag av data kan du inta data i både varma och kalla butiker. Använd varmlagring för interaktiva frågor och kyl- och fryshus för lagring av stora mängder data. Mer information om hur du drar nytta av resursbaserade frågor med hög prestanda finns [i frågor](./time-series-insights-update-tsq.md). |
| Tidsseriemodell för att kontextualisera råtelemetridata och härleda tillgångsbaserade insikter | Du kan använda tidsseriemodellen för att skapa instanser, hierarkier, typer och variabler för tidsseriedata. Mer information om Time Series-modellen finns i [Time Series Model](./time-series-insights-update-tsm.md).  |
| Jämn och kontinuerlig integrering med andra datalösningar | Data i Time Series Insights kylbutik [lagras](./time-series-insights-update-storage-ingress.md) i Apache Parquet-filer med öppen källkod. Detta möjliggör dataintegration med andra datalösningar, 1:a eller tredje part, för scenarier som inkluderar business intelligence, avancerad maskininlärning och prediktiv analys. |
| Datagranskning i nära realtid | Användargränssnittet i [Azure Time Series Insights Explorer förhandsversion](./time-series-insights-update-explorer.md) innehåller visualisering för alla data som strömmas genom datainmatningspipeline. När du har anslutit en händelsekälla kan du visa, utforska och fråga händelsedata. På så sätt kan du verifiera om en enhet genererar data som förväntat. Du kan också övervaka en IoT-tillgångs hälsotillstånd, produktivitet och övergripande effektivitet. | 
| Utökningsförmåga och integration | Azure Time Series Insights Power BI Connector-integreringen är tillgänglig direkt i tidsserieutforskarens användarupplevelse via **alternativet Exportera,** så att kunderna kan exportera de tidsseriefrågor de skapar i vår användarupplevelse direkt till Power BI-skrivbordet och visa sina tidsseriediagram tillsammans med andra BI-analyser. Detta öppnar dörren till en ny klass av scenarier för industriella IoT-företag som har investerat i Power BI genom att tillhandahålla en enda glasruta över analyser från olika datakällor, inklusive IoT-tidsserier. | 
| Anpassade program som bygger på Time Series Insights-plattformen | Time Series Insights stöder [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). SDK innehåller omfattande kontroller och förenklad åtkomst till frågor. Använd SDK för att skapa anpassade IoT-program ovanpå Time Series Insights som passar dina affärsbehov. Du kan även använda [fråge-API:er](./time-series-insights-update-tsq.md) för Time Series Insights för att driva data till anpassade IoT-program. |

## <a name="next-steps"></a>Nästa steg

Kom igång med förhandsversionen av Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Snabbstartsguide](./time-series-insights-update-quickstart.md)

Läs mer om användarsituationer:

> [!div class="nextstepaction"]
> [Användarsituationer i förhandsversionen av Azure Time Series Insights](./time-series-insights-update-use-cases.md)