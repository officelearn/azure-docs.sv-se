---
title: 'Översikt: Vad är Azure Time Series Insights? | Microsoft Docs'
description: Introduktion till Azure Time Series Insights, en ny tjänst för analys av tidsseriedata och IoT-lösningar.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: ce18b0060fe0bd0f9d5a8308fe1358e300c04441
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165628"
---
# <a name="what-is-azure-time-series-insights"></a>Vad är Azure Time Series Insights?

Azure Time Series Insights är utformat för att lagra, visualisera och fråga stora mängder time series-data, till exempel som genererats av IoT-enheter. Om du vill lagra, hantera, köra frågor mot och visualisera tidsseriedata i molnet är Time Series Insights sannolikt rätt val för dig. 

![Flödesschema för Time Series Insights](media/overview/time-series-insights-flowchart.png)

Time Series Insights har fyra nyckeluppgifter:

- Det är helt integrerat med cloud-gatewayer som Azure IoT Hub och Azure Event Hubs. Det ansluter enkelt till dessa händelsekällor och parsar JSON från meddelanden och strukturer som innehåller data i rena rader och kolumner. Det kopplar metadata till telemetri och indexerar dina data i en kolumnbaserad lagring.
- Time Series Insights hanterar lagringen av dina data. För att säkerställa att data alltid är lätta att komma åt, lagras dina data i minnet och SSD i upp till 400 dagar. Du kan interaktivt fråga miljarder händelser i sekunder – på begäran.
- Time Series Insights tillhandahåller out-of the box visualiseringen via Time Series Insights explorer. 
- Time Series Insights är en tjänst för fråga, både i Time Series Insights explorer och genom att använda API: er som är enkelt att integrera för att bädda in time series-data i anpassade program.

Om du skapar ett program för intern användning eller för externa kunder att använda kan du använda Time Series Insights som en serverdel. Du kan använda den för att index, lagra och sammanställda time series-data. Om du vill skapa en anpassad visualisering och användarupplevelse överst, använda den [klient-SDK](tutorial-explore-js-client-lib.md). Time Series Insights är även utrustad med flera [fråga API: er](how-to-shape-query-json.md) du aktivera de här anpassade scenarier.

Tidsseriedata representerar ändringar i en tillgång eller en process över tid. Time series-data indexeras av tidsstämplar och tid är den mest användbara axeln längs som sådana data ordnas. Time series-data kommer vanligtvis i sekventiell ordning, så det brukar betraktas som en infogning i stället för en uppdatering av din databas.

Det kan vara en utmaning att lagra, indexera, fråga, analysera och visualisera time series-data i stora volymer.
Azure Time Series Insights samlar in och lagrar alla nya händelser som en rad och ändra mäts effektivt över tid. Därför kan du titta bakåt om du vill hämta insikter från tidigare för att förutsäga framtida ändring.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Läs mer om Azure Time Series Insights, analysplattform för molnbaserad IoT.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primära scenarier

- Store time series-data på ett skalbart sätt. 

   I grunden har Time Series Insights en databas som utformats särskilt för tidsseriedata. Eftersom det är skalbar och fullständigt hanterad, hanterar Time Series Insights arbetet med att lagra och hantera händelser.

- Utforska data nästan i realtid. 

   Time Series Insights innehåller även en Utforskare som hjälper dig att visualiserar alla data som strömmar till en miljö. Strax efter att du ansluter till en händelsekälla kan du visa, utforska och fråga efter händelsedata i Time Series Insights. Dessa data hjälper dig att verifiera om en enhet genererar data som förväntat och övervaka en IoT-tillgång för hälsotillstånd, produktivitet och övergripande effektivitet. 

- Utför Rotorsaksanalys och identifiera avvikelser.

   Time Series Insights har verktyg som mönster och Perspektivvyer för att utföra och spara långvariga Rotorsaksanalys. Time Series Insights även fungerar med aviseringar tjänster som Azure Stream Analytics så att du kan visa aviseringar och identifierade avvikelser i nära realtid i Time Series Insights explorer. 

- Få en global översikt över time series-data som strömmas från olika platser för flera tillgång eller plats jämförelse.

   Du kan ansluta flera händelsekällor till en Time Series Insights-miljö. På så sätt kan du visa data som strömmas i från flera olika platser samtidigt i nära realtid. Användare kan dra nytta av den här synlighet för att dela data med företagsledare. De kan samarbeta bättre med domän-experter som kan utnyttja sina befintliga kunskaper för att lösa problem, gäller bästa praxis och dela erfarenheter.

- Skapa ett kundprogram ovanpå Time Series Insights. 

   Time Series Insights exponerar REST Query-API: er som du kan använda för att bygga program som använder time series-data.

## <a name="capabilities"></a>Funktioner

- **Kom igång snabbt**: Azure Time Series Insights kräver inte betalats i förskott dataförberedelse, så du kan snabbt ansluta till flera miljoner händelser i din IoT hub eller event hub. När du ansluter kan du visualisera och interagera med sensordata för att snabbt verifiera dina IoT-lösningar. Du kan interagera med dina data utan att skriva kod och du behöver inte lära dig ett nytt språk. Time Series Insights tillhandahåller en detaljerad, fritext frågan yta för avancerade användare och peka och klicka på utforskning.

- **Nära realtid**: Time Series Insights kan mata in flera miljoner sensorhändelser per dag, med en minuts fördröjning. Time Series Insights hjälper dig att få insikter om dina sensordata. Du kan använda den för att upptäcka trender och avvikelser, utföra rotorsaksanalyser och undvika kostsamma avbrott. Korskorrelation mellan realtidsdata och historiska data hjälper dig att hitta dolda trender i data.

- **Skapa anpassade lösningar**: Bädda in Azure Time Series Insights-data i dina befintliga program. Du kan också skapa nya anpassade lösningar med API: er för Time Series Insights REST. Skapa anpassade vyer som kan du dela och med dig av, så att andra också kan ta del av dina insikter.

- **Skalbarhet**: Time Series Insights är utformat för att fungera med IoT-skalning. Det kan mata in mellan 1 och 100 miljoner händelser per dag, med ett standardomfång för kvarhållning på 31 dagar. Du kan visualisera och analysera live dataströmmar i nära realtid tillsammans med historiska data.

## <a name="get-started"></a>Kom igång

Följ dessa steg för att komma igång.

1. Etablera en Time Series Insights-miljö i Azure-portalen.
1. Anslut till en händelsekälla som en IoT-hubb eller en event hub. 
1. Ladda upp referensdata. Det är inte en ytterligare tjänst.
1. Visa data på några minuter med Time Series Insights-utforskaren.

## <a name="time-series-insights-explorer"></a>Time Series Insights-utforskaren

Det här diagrammet visar ett exempel på tidsserie insights-data som visas via Time Series Insights explorer.

![Time Series Insights-utforskaren](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Nästa steg

- Utforska Azure Time Series Insights är allmänt tillgänglig [gratis demonstrationsmiljö](./time-series-quickstart.md).
- Mer information om hur du [planera Time Series Insights](time-series-insights-environment-planning.md) miljö.
