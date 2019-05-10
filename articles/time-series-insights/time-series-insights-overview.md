---
title: 'Översikt: Vad är Azure Time Series Insights? | Microsoft Docs'
description: Introduktion till Azure Time Series Insights, en ny tjänst för analys av tidsseriedata och IoT-lösningar.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 8c7a24c4c4e071271d16de036ef6896e9d172913
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406334"
---
# <a name="what-is-azure-time-series-insights"></a>Vad är Azure Time Series Insights?

Azure Time Series Insights (TSI) har skapats för lagring, visualisera och fråga stora mängder time series-data, till exempel som genererats av IoT-enheter.  Om du vill lagra, hantera, köra frågor mot och visualisera tidsseriedata i molnet är Time Series Insights sannolikt rätt val för dig.  

![Flödesschema för Time Series Insights](media/overview/time-series-insights-flowchart.png)

Time Series Insights har fyra nyckeluppgifter:

1. Det är helt integrerat med cloud-gatewayer som Azure IoT Hub och Azure Event Hubs. Det ansluter enkelt till dessa händelsekällor och parsar JSON från meddelanden och strukturer som innehåller data i rena rader och kolumner. Det kopplar metadata till telemetri och indexerar dina data i en kolumnbaserad lagring.
1. TSI hanterar lagringen av dina data. För att säkerställa att data alltid är lättillgängliga lagrar det data i minnet och på SSD:er i upp till 400 dagar. Du kan interaktivt köra frågor mot flera miljarder händelser per sekund – på begäran.
1. TSI ger out-of the box visualiseringen via TSI-Utforskaren.  
1. TSI är en tjänst för fråga, både i TSI-Utforskaren och genom att använda API: er som är enkelt att integrera för att bädda in time series-data i anpassade program.  

Om du skapar ett program för intern användning eller externa kunder att använda, kan TSI användas som en backend-server för indexering, lagring och aggregering av time series-data. Du kan skapa en anpassad visualisering och användarupplevelse på översta med hjälp av den [klient-SDK](tutorial-explore-js-client-lib.md). TSI också är utrustad med flera [fråga API: er](how-to-shape-query-json.md) du aktivera de här anpassade scenarier.  

Tidsseriedata representerar ändringar i en tillgång eller en process över tid. Time series-data därför indexeras av tidsstämplar och tid är den mest användbara axeln längs som sådana data ordnas. Time series-data tas emot i sekventiell ordning normalt och vanligtvis behandlas som en infogning i stället för en uppdatering av databasen som ett resultat.

I stora volymer kan det vara svårt att lagra, indexera, fråga, analysera och visualisera tidsseriedata.
Men Azure Time Series Insights samlar in och lagrar alla nya händelser som en rad och ändra mäts effektivt över tid, så att du kan se ut bakåtkompatibilitet att få insikter från tidigare och för att förutsäga framtida ändring.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Läs mer om Azure Time Series Insights, analysplattform för molnbaserad IoT.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primära scenarier

- Spara tidsseriedata på ett skalbart sätt.  
  - I grunden har Time Series Insights en databas som utformats särskilt för tidsseriedata.  Eftersom Time Series Insights är både skalbart och fullständigt hanterat klarar det av arbetet med att lagra och hantera händelser.

- Datagranskning i nära realtid.  
  - Time Series Insights tillhandahåller en utforskare som visualiserar all dataströmning till en miljö.  När en händelsekälla har anslutits går det snart därefter att visa, granska och köra frågor mot händelsedata i Time Series Insights.  Dessa data är användbara för att verifiera om en enhet skickar ut data som förväntat och övervaka en IoT-tillgångs hälsa, produktivitet och övergripande effektivitet.  

- Rotorsaksanalys och avvikelseidentifiering.
  - I Time Series Insights finns verktyg såsom mönster och perspektivvyer för att genomföra och spara rotorsaksanalys i flera steg.  Dessutom fungerar Time Series Insights tillsammans med aviseringstjänster som Azure Stream Analytics så att aviseringar och identifierade avvikelser kan visas i nära realtid i Time Series Insights-utforskaren.  

- En global vy över strömning av tidsseriedata från olika platser för jämförelser mellan flera tillgångar/anläggningar.
  - Du kan ansluta flera händelsekällor till en Time Series Insights-miljö.  Det innebär att data som strömmas in från flera olika platser kan visas tillsammans i nära realtid.  Användare kan dra nytta av den här informationen för att dela data med företagsledare och för att uppnå bättre samarbete med domänexperter, som i sin tur kan lösa problem, tillämpa metodtips och dela med sig av sina kunskaper.

- Skapa ett kundprogram ovanpå Time Series Insights. 
  - Time Series Insights gör REST-fråge-API:er tillgängliga så att du kan skapa program som använder tidsseriedata.

## <a name="capabilities"></a>Funktioner

- **Kom i gång snabbt:** Azure Time Series Insights kräver inga direkta dataförberedelser. Anslut till miljontals händelser i Azure IoT Hub eller din händelsehubb på några minuter. När anslutningen har upprättats kan du visualisera och interagera med dina sensordata och snabbt validera dina IoT-lösningar. Du kan interagera med dina data utan att skriva kod.
Du behöver inte heller lära dig något nytt språk. Time Series Insights tillhandahåller en detaljerad frågeyta med fri text för avancerade användare samt ”peka och klicka”-förklaringar.

- **Insikter i nära realtid:** Time Series Insights kan mata in miljontals sensorhändelser per dag med en svarstid på en minut. Med Time Series Insights får du insikter om dina sensordata. Det beror på att du får hjälp med att upptäcka trender och avvikelser, vilket gör det möjligt att utföra rotorsaksanalyser och undvika kostsamma avbrott. Om du aktiverar korskorrelation mellan realtidsdata och historiska data gör Time Series Insights det möjligt att upptäcka dolda trender i datauppsättningarna.

- **Skapa anpassade lösningar:** Bädda in Azure Time Series Insights-data till dina befintliga program eller skapa nya anpassade lösningar med REST-API:er för Time Series Insights. Skapa anpassade vyer som kan du dela och med dig av, så att andra också kan ta del av dina insikter.

- **Skalbarhet:** Time Series Insights är utformat för att fungera med IoT-skalning. Det kan mata in mellan 1 och 100 miljoner händelser per dag, med ett standardomfång för kvarhållning på 31 dagar. Du kan visualisera och analysera livedataströmmar nästan i realtid tillsammans med historiska data. Framöver kommer frekvensen för inmatning och kvarhållning öka för att hantera växande företag.

## <a name="getting-started"></a>Komma igång

Det tar mindre än 5 minuter att komma igång.

1. Kom igång genom att etablera en Time Series Insights-miljö i Azure Portal.
1. Anslut en händelsekälla, till exempel en Azure IoT-hubb eller en händelsehubb.  
1. Ladda upp referensdata (detta inte är en ytterligare tjänst).
1. Visa data på några minuter med Time Series Insights-utforskaren.

## <a name="time-series-insights-explorer"></a>Time Series Insights-utforskaren

I det här diagrammet finns ett exempel på tidsserieinsiktsdata som visas via utforskaren: ![Time Series Insights-utforskaren](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Nästa steg

- Utforska Azure Time Series Insights är allmänt tillgänglig [gratis demonstrationsmiljö](./time-series-quickstart.md).

- Läs mer om [planera Time Series Insights](time-series-insights-environment-planning.md) miljö.
