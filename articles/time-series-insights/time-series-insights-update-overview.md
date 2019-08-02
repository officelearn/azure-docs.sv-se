---
title: 'Översikt: Förhandsversion av Azure Time Series Insights | Microsoft Docs'
description: Översikt av Azure Time Series Insights förhandsversion.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 7d411e8d41f3026476ca8344e8ff9736701d6af6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736173"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Vad är Azure Time Series Insights för hands version?

Azure Time Series Insights för hands version är ett PaaS-erbjudande (end-to-end service). Den används för att samla in, bearbeta, lagra, analysera och fråga mycket sammanhangsbaserade, tidsbaserad, tidsbaserad och optimerad IoT-Scale-data. Time Series Insights är idealiskt för ad hoc-dataundersökning och drift analys. Time Series Insights är en unikt utbyggbar och anpassad tjänst som uppfyller de omfattande behoven för industriella IoT-distributioner.

> [!TIP]
> Funktioner i allmän tillgänglighet (GA) finns i [Översikt över Azure Time Series Insights ga](time-series-insights-overview.md).

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-preview-br"></a>Läs mer om Azure Time Series Insights för hands version. </br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>Definiera IoT-data

IoT-data är alla industriella data som är tillgängliga i till gångs intensiva organisationer. IoT-data är ofta mycket ostrukturerade eftersom de skickas från tillgångar som registrerar mått med ganska mycket störningar. Dessa mått omfattar temperatur, rörelse och fuktighet. Dessa dataströmmar kännetecknas ofta av stora luckor, skadade meddelanden och falska värden. Data från dessa strömmar måste rensas innan någon analys kan ske.

IoT-data är ofta bara betydelsefullt i samband med ytterligare datainmatningar som kommer från första partskällor, till exempel CRM eller ERP. Inmatningar kan också komma från datakällor från tredje part, till exempel väder eller plats.

Därför används bara en bråkdel av dessa data för drifts- och affärsbehov. Sådana data ger konsekvent, omfattande, aktuell och korrekt information för företagsrapportering och -analys. Omvandlingen av insamlad IoT-data till användbara insikter kräver:

* Databehandling för att rensa, filtrera, interpolera, transformera och förbereda data för analys.
* En struktur för att navigera och förstå data, alltså för att normalisera och kontextualisera data.
* Kostnadseffektiv lagring för lång eller obegränsad kvarhållning för bearbetade eller härledda data och rådata från flera årtionden.

Ett typiskt IoT-dataflödet visas i den efterföljande bilden.

  ![IoT-dataflöde][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights för industriell IoT

Dagens IoT datalandskap är mångskiftande. Kunder från branscher inom tillverkning, fordon, energi, allmännyttiga tjänster, smarta byggnader och konsultverksamhet. Scenarier innehåller ad hoc-dataundersökning där data formen är okänd. Scenarier innefattar också operativ analys schematiserade eller uttryckligen modellerade data för att driva operativ effektivitet. Dessa scenarier finns normalt bredvid varandra och stöder olika användningsfall. Plattformsfunktioner som är nyckeln till framgång för industriella IoT-företag och deras digitala revolution är:

- Flera lager lagring, både varma och kalla.
- Möjligheten att lagra decennier av Time Series-data.
- Möjligheten att uttryckligen modellera och optimera frågor för tillgångsbaserad driftsinformation.

Time Series Insights är ett omfattande PaaS-erbjudande från slut punkt till slut punkt för IoT data utforskning och Operational Insights. Time Series Insights är en fullständigt hanterad molntjänst för analys av tidsseriedata i IoT-skala.

Du kan lagra rådata i ett minnesinternt arkiv utan schema. Du kan sedan utföra interaktiva ad hoc-frågor via en distribuerad frågemotor och API. Använd den rika användarupplevelsen för att visualisera miljarder händelser på några sekunder. Läs mer om [datagranskningsfunktioner](./time-series-insights-overview.md).

Time Series Insights tillhandahåller även funktioner för åtgärdsinformation som för närvarande är en förhandsversion. Tillsammans med interaktiv datagranskning och driftsinformation kan du använda Time Series Insights för att härleda mer värde ur data som samlas in från IoT-tillgångar. Förhandsversionserbjudandet stöder:

* Ett skalbart och kostnads optimerat data lager för tids serier. Den här molnbaserade IoT-lösningen kan svänga flera års tidsseriedata på några sekunder.
* Stöd för semantisk modell som beskriver den domän och de metadata som är kopplade till härledda och icke härledda signaler från till gångar och enheter.
* En förbättrad användar upplevelse som kombinerar till gångs data insikter med omfattande ad hoc data analys. Den här kombinationen styr affärs- och driftsinformation.
* Integrering med avancerade verktyg för maskininlärning och analys. Verktygen inkluderar Azure Databricks, Apache Spark, Azure Machine Learning, Jupyter-notebooks och Power BI. Verktygen hjälper dig att hantera utmaningarna med tidsseriedata och öka effektiviteten.

Tillsammans tillhandahålls åtgärdsinformation och datagranskning med en enkel användningsbaserad prismodell för databehandling, lagring och frågor. Den här faktureringsmodellen passar varierande affärsbehov.

Det här avancerade dataflödesdiagrammet visar uppdateringarna.

  ![De viktigaste funktionerna][2]

Med introduktionen av dessa viktiga funktioner för industriell IoT tillhandahåller Time Series Insights följande viktiga fördelar.

| | |
| ---| ---|
| Lagring med flera lager för IoT-Scale Time Series-data | Du kan lagra data i varm lagring för interaktiva frågor med en vanlig databehandlingspipeline för att mata in data. Du kan även lagra data i kall lagring för stora mängder data. Dra nytta av högpresterande tillgångsbaserade [frågor](./time-series-insights-update-tsq.md). |
| Time Series-modell för sätta av RAW-telemetri och härledd till gångs-baserade insikter | Kontextualisera råtelemetridata med den beskrivande [tidsseriemodellen](./time-series-insights-update-tsm.md). Härled omfattande driftsinformation med högpresterande och kostnadsoptimerade enhetsbaserade frågor. |
| Smidig och kontinuerlig integrering med andra data lösningar | Data i Time Series Insights [lagras](./time-series-insights-update-storage-ingress.md) i Apache Parquet-filer med öppen källkod. Den här integreringen med andra datalösningar, oavsett om de är från första eller tredje part, är enkel för slutpunkt-till-slutpunkt scenarier. Scenarierna innefattar Business Intelligence, avancerad maskininlärning och förutsägelseanalys. |
| Datagranskning i nära realtid | Användargränssnittet i [Azure Time Series Insights Explorer förhandsversion](./time-series-insights-update-explorer.md) innehåller visualisering för alla data som strömmas genom datainmatningspipeline. Strax efter att du ansluter en händelsekälla kan du visa, utforska och fråga efter händelsedata. På så sätt kan du verifiera om en enhet genererar data som förväntat. Du kan också övervaka en IoT-tillgångs hälsotillstånd, produktivitet och övergripande effektivitet. |
| Rotorsaksanalys och avvikelseidentifiering | [Azure Time Series Insights Explorer förhandsversion](./time-series-insights-update-explorer.md) stöder både mönster och perspektivvyer för att genomföra och spara rotorsaksanalys i flera steg. I kombination med Azure Stream Analytics kan du använda Time Series Insights för att upptäcka varningar och avvikelser i nära realtid. |
| Anpassade program som bygger på Time Series Insights plattform | Time Series Insights stöder [JavaScript SDK](./tutorial-explore-js-client-lib.md). SDK innehåller omfattande kontroller och förenklad åtkomst till frågor. Använd SDK för att skapa anpassade IoT-program ovanpå Time Series Insights som passar just dina affärsbehov. Du kan även använda [fråge-API:er](./time-series-insights-update-tsq.md) för Time Series Insights för att driva data till anpassade IoT-program. |

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Time Series Insights för hands version:

> [!div class="nextstepaction"]
> [Läs snabbstartsguiden](./time-series-insights-update-quickstart.md)

Läs mer om användarsituationer:

> [!div class="nextstepaction"]
> [Användarsituationer i förhandsversionen av Azure Time Series Insights](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview-one.png
[2]: media/v2-update-overview/overview-two.png
