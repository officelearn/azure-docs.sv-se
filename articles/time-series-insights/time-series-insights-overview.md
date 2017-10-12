---
title: "Översikt över Time Series Insights | Microsoft Docs"
description: "Introduktion till Azure Time Series Insight som är en ny tjänst för dataanalyser av tidsserier och IoT-lösningar"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.openlocfilehash: 1814459e47280af62450a4093140ab6ab9b765fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-time-series-insights"></a>Vad är Azure Time Series Insights?

Azure Time Series Insights är en hanterad molntjänst med komponenter för lagring, analys och visualisering som gör det enkelt att mata in, lagra, utforska och analysera miljarder händelser samtidigt. Med Time Series Insights får du en global vy över dina data som hjälper dig att snabbt validera dina IoT-lösningar och undvika kostsamma avbrott. Du får hjälp med att identifiera dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser nästan i realtid. Time Series Insights matar in Time Series-data från händelsekoordinatorer (som IoT Hubs eller Event Hubs), indexerar data och avvecklar data utifrån en konfigurerbar bevarandeprincip. Användarna använder data antingen via en intuitiv UX eller REST Query-API:er.

![Översikt över Time Series Insight](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Primära scenarier

* Övervaka och validera IoT-lösningar på några minuter.
* Visualisera och analysera IoT-data i större skala.
* Påskynda rotorsaksanalys och avvikelseidentifiering.
* Skapa en global översikt över flera enheter, anläggningar och data.

## <a name="capabilities-and-benefits"></a>Funktioner och fördelar

* **Lätt att komma igång**: Azure Time Series Insights kräver inga direkta dataförberedelser och är otroligt snabbt. Anslut till miljarder händelser i Azure IoT Hub eller din händelsehubb på några minuter. När anslutningen har upprättats kan du visualisera och interagera med dina sensordata på några sekunder och snabbt validera dina IoT-lösningar. Time Series Insights är lätt att använda. Du kan interagera med dina data utan att behöva skriva en enda rad kod.  Du behöver inte heller lära dig något nytt språk. Time Series Insights tillhandahåller en detaljerad frågeyta med fri text för avancerade användare samt ”peka och klicka”-förklaringar för alla.

* **Insikter praktiskt taget i realtid**: Time Series Insights kan mata in hundratals miljoner sensorhändelser per dag med en svarstid på en minut, så att du snabbt kan reagera på förändringar. Med Time Series Insights får du djupare insikter om dina sensordata. Det beror på att du får hjälp med att upptäcka trender och avvikelser snabbt, vilket gör det möjligt att utföra komplicerade rotorsaksanalyser och undvika kostsamma avbrott. Om du aktiverar korskorrelation mellan realtidsdata och historiska data gör Time Series Insights det möjligt att upptäcka dolda trender i datauppsättningarna.

* **Bygga anpassade lösningar**: Bädda in Azure Time Series Insights-data till dina befintliga program eller skapa nya anpassade lösningar med REST-API:er för Time Series Insights. Skapa och dela anpassade vyer som kan du dela och med dig av, så att andra också kan ta del av dina upptäckter.

* **Skalbarhet**: Time Series Insights är utformat för att fungera med IoT-skalning. I förhandsversionen kan mellan 1 och 100 miljoner händelser matas in per dag, med ett standardomfång för kvarhållning på 31 dagar. Du kan visualisera och analysera livedataströmmar nästan i realtid tillsammans med stora mängder historiska data. Framöver kommer frekvensen för inmatning och kvarhållning att öka för att hantera allt större företag.

## <a name="time-series-insights-glossary"></a>Ordlista för Time Series Insights

* **Miljö**: En miljö är en Azure-resurs med ingångs- och lagringskapacitet.  Kunder etablerar miljöer via Azure Portal med den kapacitet som krävs.
* **Händelsekälla**: En händelsekälla härleds från en asynkron meddelandekö för händelser, som Azure Event Hubs.  Time Series Insights ansluts direkt till händelsekällor och matar in data i dataströmmen utan att man behöver skriva kod. Tidserieinsikter stöder för närvarande, Azure Event Hubs och Azure IoT Hubs.
* **Referensdata**: Time Series Insights ger användarna möjlighet att ansluta till Time Series-data med referensdata.  Referensdata kan innehålla metadata om enheter eller andra statiska data som ändras relativt sällan. Time Series Insights ansluter till referensdata med dataströmmar så att användarna kan visualisera och analysera sina data nästan i realtid.
