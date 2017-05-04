---
title: "Översikt över Time Series Insights | Microsoft Docs"
description: "Introduktion till Azure Time Series Insight som är en ny tjänst för Time Series-dataanalys och IoT-lösningar"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.lasthandoff: 04/25/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Översikt över Time Series Insights

Azure Time Series Insights är en fullständigt hanterad molntjänst med komponenter för lagring, analys och visualisering som gör det otroligt lätt att mata in, lagra, utforska och analysera miljarder händelser samtidigt. Med Time Series Insights får du en global vy över dina data som hjälper dig att snabbt verifiera din IoT-lösning och undvika kostsamma avbrott på verksamhetskritiska enheter. Du får hjälp med att identifiera dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser i nära realtid. Time Series Insights matar in Time Series-data från händelsekoordinatorer (som IoT Hubs eller Event Hubs), indexerar data och avvecklar data utifrån en konfigurerbar bevarandeprincip. Användarna använder data för sina affärsscenarion antingen via en intuitiv UX eller REST API:er med fråga. 

![Översikt över Time Series Insight](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Primära scenarier

* Validering och övervakning av IoT-lösningar på några minuter
* Intuitiv visualisering och analys av IoT-data i stor skala
* Påskynda rotorsaksanalys och avvikelseidentifiering
* Skapa en global översikt över flera enheter, anläggningar och data

## <a name="key-capabilities-and-benefits"></a>Viktiga funktioner och fördelar

* **Enkelt att komma igång**: Azure Time Series Insights kräver ingen dataförberedelse i början och är otroligt snabb, så du kan ansluta till miljarder händelser i din Azure IoT Hub eller Event Hub på några minuter. När du blivit ansluten kan du visualisera och interagera med dina sensordata på några sekunder för att snabbt verifiera dina IoT-lösningar. Time Series Insights är otroligt intuitivt och lätt att använda. Du kan interagera med dina data utan att behöva skriva en enda rad kod.  Du behöver inte heller lära dig något nytt språk. Time Series Insights tillhandahåller detaljerad, textbaserad frågekörning för avancerade användare samt ”peka och klicka”-förklaringar för nybörjare.

* **Insikter på några sekunder i nästan realtid**: Få ut mer värde av dina Time Series-data med Time Series Insights komponenter för lagring, analys och visualisering – allt på ett ställe. Time Series Insights kan mata in hundratals miljoner sensorhändelser per dag med en svarstid på en minut, så att du snabbt kan reagera på förändringar. Med Time Series Insights får du djupare insikter om dina sensordata. Det beror på att du får hjälp med att upptäcka trender och avvikelser snabbt, vilket gör det möjligt att utföra rotorsaksanalyser och undvika kostsamma avbrott. Om du aktiverar korskorrelation mellan realtidsdata och historiska data gör Time Series Insights det möjligt för användare att upptäcka dolda trender i sina data.

* **Bygga anpassade lösningar**: Bädda in Azure Time Series Insights-data till dina befintliga program eller skapa nya anpassade lösningar med REST-API:er för Time Series Insights.  Dessutom är det busenkelt att skapa och dela anpassade vyer, vilket gör det enklare för andra att utforska dina upptäckter.

* **Skalbarhet**: Time Series Insights är utformat för att stödja IoT-skalning. I förhandsversionen kan det mata in från 1 till 100 miljoner händelser per dag med ett standardomfång för kvarhållning på 31 dagar. Med Time Series Insights kan användarna visualisera och analysera livedataströmmar nästan i realtid tillsammans med stora mängder historiska data. Framöver kommer frekvensen för inmatning och kvarhållning öka för att hantera allt större företag.

## <a name="taxonomy-of-time-series-insights"></a>Time Series Insights-taxonomi

* **Miljö**: En miljö är en Azure-resurs med ingångs- och lagringskapacitet.  Kunder etablerar miljöer via Azure Portal med den kapacitet som krävs.
* **Händelsekälla**: En händelsekälla härleds från en asynkron meddelandekö för händelser, som Azure Event Hubs.  Time Series Insights ansluts direkt till händelsekällor och matar in dataströmmen utan att kräva att användarna skriver en enda rad kod. För närvarande stöder Time Series Insights Azure Event Hubs och Azure IoT Hubs, och i framtiden kommer fler händelsekällor att läggas till.
* **Referensdata**: Time Series Insights ger användarna möjlighet att ansluta till Time Series-data med referensdata.  Referensdata kan innehålla metadata om enheter eller andra statiska data som ändras relativt sällan. Time Series Insights ansluter till referensdata med dataströmmar så att användarna kan visualisera och analysera sina data nästan i realtid.

