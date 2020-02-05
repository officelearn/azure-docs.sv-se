---
title: 'Översikt: Vad är Azure Time Series Insights för hands version? -Azure Time Series Insights | Microsoft Docs'
description: Lär dig mer om ändringar, förbättringar och funktioner i Azure Time Series Insights för hands versionen.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014427"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Vad är Azure Time Series Insights (förhandsversion)?

Azure Time Series Insights för hands version är ett PaaS-erbjudande (end-to-end service). Du kan använda den för att samla in, bearbeta, lagra, analysera och fråga data på Sakernas Internet (IoT)-data som är mycket kontextuella och optimerade för Time Series. 

Time Series Insights är utformad för ad hoc-dataundersökning och drift analys. Det är ett utöknings Bart och anpassat tjänst erbjudande som uppfyller de breda behoven i industriella IoT-distributioner.

## <a name="video"></a>Video

Läs mer om Azure Time Series Insights för hands version.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definition av IoT-data

Industriella IoT-data i till gångs intensiva organisationer saknar ofta strukturell konsekvens på grund av den varierande typen av enheter och sensorer i en industriell inställning. Data från dessa data strömmar karakteriseras av betydande luckor och ibland skadade meddelanden och falsk läsning. IoT-data är ofta meningsfulla i samband med ytterligare data inmatning som kommer från första eller tredje källor, till exempel CRM eller ERP som lägger till kontext för slut punkt till slut punkt. Indata från tredje parts data källor som väder data kan bidra till att utöka telemetri strömmar i en specifik installation. 

Allt detta innebär att endast en bråkdel av data används för operativa och affärsmässiga behov, och analys kräver contextualization. Industriella data är ofta historiska för djupgående analys över längre tid i sträck att förstå och korrelera trender. Omvandlingen av insamlad IoT-data till användbara insikter kräver: 

* Databehandling för att rensa, filtrera, interpolera, transformera och förbereda data för analys.
* En struktur för att navigera genom och förstå data, det vill säga för att normalisera och sätta data.
* Kostnads effektiv lagring för lång eller oändlig kvarhållning av bearbetade (eller härledda) data och rå data.

Sådana data ger konsekvent, omfattande, aktuell och korrekt information för företags analys och rapportering.

Följande bild visar ett typiskt IoT-dataflöde.

[![IoT-dataflöde](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights för industriell IoT

IoT-landskapet skiljer sig mellan kunder som sträcker sig över olika bransch segment, inklusive tillverkning, bil, energi, verktyg, smarta byggnader och konsult tjänster. I den här breda serien av industriella IoT-marknader utvecklas molnbaserade lösningar som tillhandahåller omfattande analyser riktade till storskaliga IoT-data i stor skala. 

Azure Time Series Insights åtgärdar marknadens behov genom att tillhandahålla en nyckel färdig lösning för IoT Analytics med omfattande semantisk modellering för contextualization av tids serie data, till gångs-baserade insikter och förstklassig användar upplevelse för identifiering, Trends, avvikelse identifiering och drift information. 

En intensiv drifts analys plattform tillsammans med våra interaktiva data utforsknings funktioner kan du använda Time Series Insights för att härleda mer värde av data som samlas in från IoT-tillgångar. Förhandsversionserbjudandet stöder: 

* Lagrings lösning med extra lager med varm och kall analys som ger kunderna möjlighet att dirigera data mellan varma och kalla för interaktiva analyser över varmt data och drifts information över årtionden av historiska data. 

    *   En mycket interaktiv lösning för varm lösning för att utföra frekventa och många frågor över kortare tids periods data 
    *   En skalbar, genomförd och kostnads optimerad tids serie data Lake baserat på Azure Storage som gör det möjligt för kunder att trenda års tids serie data på några sekunder. 

* Stöd för semantisk modell som beskriver den domän och de metadata som är kopplade till de härledda och råa signalerna från till gångar och enheter.

* Flexibel analys plattform för att lagra historiska tids serie data i kundägda Azure Storage-konto, så att kunderna kan ha ägande rätt till sina IoT-data. Data lagras i Apache Parquet-format med öppen källkod som möjliggör anslutningar och interop i flera olika data scenarier, inklusive förutsägelse analyser, maskin inlärning och andra anpassade beräkningar som gjorts med hjälp av den välkända tekniken, inklusive Spark, Databricks och Jupyter.

* Omfattande analyser med förbättrade API: er för frågor och användar upplevelse som kombinerar till gångs data insikter med omfattande ad hoc-dataanalys med stöd för interpolation, skalärfunktioner och mängd funktioner, kategoriska-variabler, punkt diagram och tids växlings tid serie signaler för djupgående analys.

*   Plattform för företags klass som stöder skalnings-, prestanda-, säkerhets-och Tillförlitlighets behoven hos våra Enterprise IoT-kunder.

* Utöknings-och integrations support för slut punkt till slut punkts analys. Time Series Insights tillhandahåller en utöknings bar analys plattform för flera olika data scenarier. Time Series Insights Power BI Connector gör det möjligt för kunderna att ta de frågor de gör i Time Series Insights direkt till Power BI för att få en enhetlig vy över sin BI-och Time Series-analys i ett enda fönster i glaset.

I följande diagram visas data flödet på hög nivå.

  [![viktiga funktioner](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights ger en skalbar pris modell enligt principen betala per användning för data bearbetning, lagring (data och metadata) och fråga, vilket gör det möjligt för kunderna att justera sin användning så att de passar deras affärs behov. 
 
I introduktionen av de här viktiga industriella IoT-funktionerna har Time Series Insights även följande viktiga fördelar.  

| | |
| ---| ---|
| Lagring med flera lager för IoT-Scale Time Series-data | Med en data behandlings pipeline för delade data för att mata in data kan du mata in data i både varma och kalla butiker. Använd varmt Store för interaktiva frågor och kall lagring för lagring av stora mängder data. Om du vill veta mer om hur du kan dra nytta av högpresterande till gångs frågor, se [frågor](./time-series-insights-update-tsq.md). |
| Time Series-modell för sätta av RAW-telemetri och härledd till gångs-baserade insikter | Du kan använda Time Series-modellen för att skapa instanser, hierarkier, typer och variabler för dina Time Series-data. Mer information om tids serie modellen finns i [tids serie modell](./time-series-insights-update-tsm.md).  |
| Smidig och kontinuerlig integrering med andra data lösningar | Data i Time Series Insights kall lagring [lagras](./time-series-insights-update-storage-ingress.md) i Apache Parquet-filer med öppen källkod. Detta möjliggör data integrering med andra data lösningar, 1st eller tredje part, för scenarier som omfattar Business Intelligence, avancerad maskin inlärning och förutsägelse analys. |
| Datagranskning i nära realtid | Användargränssnittet i [Azure Time Series Insights Explorer förhandsversion](./time-series-insights-update-explorer.md) innehåller visualisering för alla data som strömmas genom datainmatningspipeline. När du har anslutit en händelse källa kan du Visa, utforska och fråga händelse data. På så sätt kan du verifiera om en enhet genererar data som förväntat. Du kan också övervaka en IoT-tillgångs hälsotillstånd, produktivitet och övergripande effektivitet. | 
| Utökning och integrering | Integreringen Azure Time Series Insights Power BI Connector är tillgänglig direkt i användar upplevelsen för Time Series Explorer via **export** alternativet, så att kunderna kan exportera tids serie frågorna som de skapar i vår användar upplevelse direkt till Power BI Skriv bordet och visa sina tids serie diagram tillsammans med andra bi-analyser. Detta öppnar dörren till en ny klass med scenarier för industriella IoT-företag som har investerat i Power BI genom att tillhandahålla ett enda fönster för analys från olika data källor, inklusive IoT Time Series. | 
| Anpassade program som bygger på Time Series Insightss plattformen | Time Series Insights stöder [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). SDK innehåller omfattande kontroller och förenklad åtkomst till frågor. Använd SDK: n för att bygga anpassade IoT-program ovanpå Time Series Insights som passar dina affärs behov. Du kan även använda [fråge-API:er](./time-series-insights-update-tsq.md) för Time Series Insights för att driva data till anpassade IoT-program. |

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Time Series Insights för hands version:

> [!div class="nextstepaction"]
> [Snabb starts guide](./time-series-insights-update-quickstart.md)

Läs mer om användarsituationer:

> [!div class="nextstepaction"]
> [Användarsituationer i förhandsversionen av Azure Time Series Insights](./time-series-insights-update-use-cases.md)