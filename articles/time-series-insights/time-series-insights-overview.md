---
title: 'Översikt: Vad är Azure Time Series Insights? - Insikter i Azure-tidsserier | Microsoft-dokument'
description: Introduktion till Azure Time Series Insights, en ny tjänst för analys av tidsseriedata och IoT-lösningar.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 59149b2ca598104d8aca9b4e5e60194a8f6398bf
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269966"
---
# <a name="what-is-azure-time-series-insights"></a>Vad är Azure Time Series Insights?

Azure Time Series Insights är byggd för att lagra, visualisera och fråga stora mängder tidsseriedata, till exempel som genereras av IoT-enheter. Om du vill lagra, hantera, köra frågor mot och visualisera tidsseriedata i molnet är Time Series Insights sannolikt rätt val för dig.

[![Flödesschema för Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Time Series Insights har fyra nyckeluppgifter:

- Den är helt integrerad med molngateways som Azure IoT Hub och Azure Event Hubs. Det ansluter enkelt till dessa händelsekällor och parsar JSON från meddelanden och strukturer som innehåller data i rena rader och kolumner. Det kopplar metadata till telemetri och indexerar dina data i en kolumnbaserad lagring.
- Time Series Insights hanterar lagring av dina data. För att se till att data alltid är lättillgängliga lagras dina data i minnet och SSD i upp till 400 dagar. Du kan interaktivt fråga miljarder händelser på några sekunder – på begäran.
- Time Series Insights ger out-of-the-box visualisering via Time Series Insights explorer.
- Time Series Insights tillhandahåller en frågetjänst, både i Time Series Insights explorer och genom att använda API:er som är lätta att integrera för att bädda in tidsseriedata i anpassade program.

Om du skapar ett program för intern förbrukning eller för externa kunder att använda kan du använda Time Series Insights som en backend. Du kan använda den för att indexera, lagra och aggregera tidsseriedata. Om du vill skapa en anpassad visualisering och användarupplevelse överst använder du [Klienten SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Time Series Insights är också utrustad med flera [Query API:er](how-to-shape-query-json.md) för att aktivera dessa anpassade scenarier.

Tidsseriedata representerar ändringar i en tillgång eller en process över tid. Tidsseriedata indexeras efter tidsstämplar, och tiden är den mest meningsfulla axeln längs vilken sådana data är ordnade. Tidsseriedata levereras vanligtvis i sekventiell ordning, så de behandlas som en infogning i stället för en uppdatering till databasen.

Det kan vara en utmaning att lagra, indexera, fråga, analysera och visualisera tidsseriedata i stora volymer.
Azure Time Series Insights fångar och lagrar varje ny händelse som en rad, och förändring mäts effektivt med tiden. Som ett resultat kan du titta bakåt för att dra insikter från det förflutna för att förutsäga framtida förändringar.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Läs mer om Azure Time Series Insights, den molnbaserade IoT-analysplattformen.</br>

[![Video](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primära scenarier

- Lagra tidsseriedata på ett skalbart sätt.

   I grunden har Time Series Insights en databas som utformats särskilt för tidsseriedata. Eftersom time series insights är skalbart och hanterat helt hanterar de arbetet med att lagra och hantera händelser.

- Utforska data i nära realtid.

   Time Series Insights tillhandahåller en utforskare som visualiserar alla data som strömmas in i en miljö. Kort efter att du har anslutit till en händelsekälla kan du visa, utforska och fråga händelsedata i Time Series Insights. Data hjälper dig att validera om en enhet avger data som förväntat och övervakar en IoT-tillgång för hälsa, produktivitet och övergripande effektivitet.

- Utför rotorsaksanalys och identifiera avvikelser.

   Time Series Insights har verktyg som mönster och perspektivvyer för att genomföra och spara analys av grundorsaker med flera steg. Time Series Insights fungerar också med aviseringar tjänster som Azure Stream Analytics så att du kan visa aviseringar och upptäckta avvikelser i nära realtid i Time Series Insights explorer.

- Få en global vy över tidsseriedata som strömmas från olika platser för jämförelse av flera tillgångar eller webbplatser.

   Du kan ansluta flera händelsekällor till en Time Series Insights-miljö. På så sätt kan du visa data som strömmas in från flera, olika platser tillsammans i nära realtid. Användare kan dra nytta av den här synligheten för att dela data med företagsledare. De kan samarbeta bättre med domänexperter som kan använda sin expertis för att lösa problem, tillämpa bästa praxis och dela med sig av lärdomar.

- Skapa ett kundprogram ovanpå Time Series Insights.

   Time Series Insights exponerar REST Query API:er som du kan använda för att skapa program som använder tidsseriedata.

## <a name="capabilities"></a>Funktioner

- **Kom igång snabbt:** Azure Time Series Insights kräver inte förhandsdataförberedelser, så du kan snabbt ansluta till miljontals händelser i din IoT-hubb eller händelsehubb. När du har anslutit kan du visualisera och interagera med sensordata för att snabbt validera dina IoT-lösningar. Du kan interagera med dina data utan att skriva kod och du behöver inte lära dig ett nytt språk. Time Series Insights ger en detaljerad, fritextfrågeyta för avancerade användare och peka-och-klicka-utforskning.

- **Insikter i nära realtid**: Time Series Insights kan inta miljontals sensorhändelser per dag, med en minuts latens. Time Series Insights hjälper dig att få insikter i dina sensordata. Använd den för att upptäcka trender och avvikelser, utföra rotorsaksanalyser och undvika kostsamma driftstopp. Korskorrelation mellan realtidsdata och historiska data hjälper dig att hitta dolda trender i data.

- **Skapa anpassade lösningar:** Bädda in Azure Time Series Insights-data i dina befintliga program. Du kan också skapa nya anpassade lösningar med TIDSSERIE Insights REST API:er. Skapa anpassade vyer som kan du dela och med dig av, så att andra också kan ta del av dina insikter.

- **Skalbarhet**: Time Series Insights är utformat för att stödja IoT i stor skala. Det kan mata in mellan 1 och 100 miljoner händelser per dag, med ett standardomfång för kvarhållning på 31 dagar. Du kan visualisera och analysera livedataströmmar i nära realtid, tillsammans med historiska data.

## <a name="get-started"></a>Kom igång

Följ de här stegen för att komma igång.

1. Etablera en Time Series Insights-miljö i Azure-portalen.
1. Anslut till en händelsekälla som ett IoT-nav eller en händelsehubb.
1. Ladda upp referensdata. Det här är ingen tilläggstjänst.
1. Granska och visa dina data på några minuter med utforskaren Time Series Insights.

## <a name="explorer"></a>Utforskaren

Visa, analysera och upptäck trender i dina data med hjälp av Utforskaren för Azure Time Series Insights.

![Time Series Insights-utforskaren](media/overview/time-series-insights-explorer-panel.png)

Lär dig hur du använder Utforskaren för [Azure Time Series Insights](time-series-insights-explorer.md) och hämtar insikter från dina data.

## <a name="next-steps"></a>Nästa steg

- Utforska Azure Time Series Insights allmänna [tillgänglighetsfri demonstrationsmiljö](./time-series-quickstart.md).

- Läs mer om hur du [planerar din time series insights-miljö.](time-series-insights-environment-planning.md)
