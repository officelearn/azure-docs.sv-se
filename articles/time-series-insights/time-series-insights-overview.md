---
title: Vad är Azure tid serien Insights? | Microsoft Docs
description: Introduktion till Azure tid serien Insights, en ny tjänst för tid serien dataanalys och IoT-lösningar.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan, jasonh
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 01/26/2018
ms.openlocfilehash: e31cebfd027e93096e233f2963445e4fc50a7e9d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="what-is-azure-time-series-insights"></a>Vad är Azure tid serien Insights?

Tid serien insikter är utformat för att lagra, visualisera och fråga stora mängder tid serie data, till exempel som genererats av IoT-enheter.  Om du vill lagra, hantera, fråga och visualisera tid series-data i molnet, sannolikt tid serien insikter rätt för dig.  

! [Tid serien insikter flödesschema] (media/overview/time-series-insights-flowchart.png)

Tid serien insikter har fyra viktiga jobb:

- Först, den är helt integrerade med molnet gateways som Azure IoT Hub och Händelsehubbar i Azure. Enkelt ansluter till dessa händelsekällor och Parsar JSON från meddelanden och strukturer som innehåller data i ren rader och kolumner. Den ansluter till metadata med telemetri och indexerar dina data i en kolumn store.
- Tid serien insikter hanterar andra, lagring av dina data. För att säkerställa att data alltid är lätt tillgängliga, lagras data i minnet och SSDS i upp till 400 dagar. Du kan interaktivt fråga miljarder händelser i sekunder – på begäran.
- Det tredje ger tid serien insikter out box visualiseringen via TSD explorer.  
- Fjärde, tid serien insikter får du en fråga, både i TSD explorer och genom att använda API: er som är enkel att integrera för inbäddning tid series-data i anpassade program.  

Om du utvecklar ett program för internt bruk eller för externa kunder att använda, kan tid serien insikter användas som en serverdel för indexering, lagring och aggregering tid series-data. Du kan skapa en anpassad visualisering och användarupplevelse längst upp.  Tid serien insikter visar frågan API: er för att aktivera det här scenariot.  

Om du är osäker på om dina data är tidsserier är här vad du bör känna till.  Serien tidsdata representerar hur en process eller ändras med tiden.  Det är unikt i att den har en tidsstämpel och tid är mest användbar som en axel.  Serien tidsdata normalt anländer i tid ordning och brukar betraktas som en insert i stället för en uppdatering till din databas.  Eftersom tiden serien Insights samlar in och lagrar varje ny händelse som en rad, mäts ändras över tiden, vilket gör det lättare att söka bakåt och för att förutsäga framtida ändring.  I stora volymer, kan det vara svårt att lagra, indexering, frågor, analysera och visualisera tid series-data.  

## <a name="primary-scenarios"></a>Primära scenarier

- Spara tid seriedata i ett skalbart sätt.  
  - Kärnan har tid serien insikter en databas med serien tidsdata i åtanke.  Eftersom det blir skalbar och helt hanterad hanterar tid serien insikter arbetet med att lagra och hantera händelser.

- Nära realtid datagranskning.  
  - Tid serien insikter ger en explorer som visualizes alla data som strömmas till en miljö.  Strax efter att ansluta en händelsekälla kan händelsedata visas, utforskade och efterfrågade inom tid serien insikter.  Data är användbart för att verifiera om en enhet avger data som förväntat och övervaka en IoT-tillgång för hälsotillstånd, produktivitet och övergripande effektivitet.  

- Grundorsaken analys och avvikelseidentifiering identifiering.
  - Tid serien insikter har verktyg som mönster och Perspektivvyer för att genomföra och spara Rotorsaksanalys för flera steg.  Dessutom tid serien insikter fungerar tillsammans med aviseringar tjänster som Azure Stream Analytics, så du kan visa aviseringar och identifierade avvikelser i nära realtid i Utforskaren tid serien insikter.  

- En global översikt över tid serie data som strömmas från olika platser för flera asset/plats jämförelse.
  - Du kan ansluta flera händelsekällor till en tid serien insikter.  Det innebär att data som strömmas i från flera olika platser kan visas tillsammans i nära realtid.  Användare kan dra nytta av den här synlighet att dela data med business utfyllnadstecken och möjliggöra bättre samarbete med domän-experter som kan använda sina kunskaper för att lösa problem, tillämpa metodtips och dela learnings.

- Skapa en kund program ovanpå tid serien insikter. 
  - Tid serien insikter exponerar REST frågan API: er så att du kan skapa program som använder tid series-data.

## <a name="capabilities"></a>Funktioner

- **Kom igång snabbt:** Azure tid serien Insights kräver inga direkta data förbereds. Ansluta till miljontals händelser i dina Azure IoT-hubb eller Händelsehubb i minuter. När du är ansluten, visualisera och interagera med sensordata för att snabbt verifiera din IoT-lösningar. Du kan interagera med dina data utan att skriva kod.
Det finns inga nya språket Läs; Tid serien insikter ger en detaljerad, fritext frågan yta för avancerade användare pekar och klickar på undersökning.
- **Nära realtidsinsikter:** tid serien insikter som kan mata in miljontals sensor händelser per dag, med en minuts fördröjning. Tid serien Insights hjälper dig att få insikter om din sensordata genom att hjälpa dig upptäcka trender och avvikelser, genomföra analys av grundorsaken och undviker dyrbar avbrottstid. Om du aktiverar korskorrelation mellan realtidsdata och historiska data gör Time Series Insights det möjligt att upptäcka dolda trender i datauppsättningarna.
- **Skapa anpassade lösningar:** bädda in Azure tid serien Insights-data i dina befintliga program eller skapa nya anpassade lösningar med tiden serien insikter REST API: er. Skapa anpassade vyer som du kan dela för andra att utforska dina insikter.
- **Skalbarhet:** tid serien insikter är utformad för att stödja IoT i större skala. Det kan inkommande trafik från 1 miljon till 100 miljoner händelser per dag, med en Standardkvarhållning span 31 dagar. Du kan visualisera och analysera realtidsdata dataströmmar i nära realtid, tillsammans med historiska data. Går vidare ökar ingång och kvarhållning om du vill hantera företagsnivå.

## <a name="getting-started"></a>Komma igång
Komma igång tar mindre än 5 minuter. 

1.  Få igång etablera en tid serien insikter miljön i Azure-portalen. 
2.  Ansluta en händelsekälla som en Azure IoT-hubb eller Händelsehubb.  
3.  Överför referensdata (detta inte är en ytterligare tjänst).
4.  Visa data i minuter med tiden serien insikter explorer.

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer
Det här diagrammet visar ett exempel på tidsserier insights-data visas via Utforskaren: ![Tid serien insikter explorer] (media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Nästa steg
 - [Utforska Utforskaren tid serien insikter i en miljö för demonstration](./time-series-quickstart.md)
 - [Planera tid serien insikter miljön](time-series-insights-environment-planning.md)

