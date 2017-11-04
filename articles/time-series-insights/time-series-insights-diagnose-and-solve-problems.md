---
title: "Diagnostisera och lösa problem | Microsoft Docs"
description: "Den här kursen visar hur du diagnostisera och lösa problem i miljön tid serien insikter"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.openlocfilehash: 4e10a009eb67706d927ece5692134d802094cdf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnostisera och lösa problem i miljön tid serien insikter

## <a name="i-dont-see-my-data"></a>Data visas inte
Här följer några orsaker varför du inte kan se dina data i din miljö i det [Azure tid serien Insights-portalen](https://insights.timeseries.azure.com).

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>Din händelsekälla saknar data i JSON-format
Azure tid serien Insights har stöd för JSON-data i dag. JSON-exempel finns [stöds JSON former](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>När du har registrerat din händelsekälla uppgav du nyckeln som har behörigheten som krävs
* För en IoT-hubb måste du ange den nyckel som har **tjänsten ansluta** behörighet.

   ![IoT-hubb tjänsten ansluta behörighet](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Som visas i föregående bild, antingen principer **iothubowner** och **service** skulle fungera eftersom båda **tjänsten ansluta** behörighet.
* För en händelsehubb, måste du ange den nyckel som har **lyssna** behörighet.

   ![Event hub lyssna behörighet](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Som visas i föregående bild, antingen principer **läsa** och **hantera** skulle fungera eftersom båda **lyssna** behörighet.

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>Den angivna konsumentgruppen är inte bara tiden serien insikter
För en IoT-hubb eller en händelsehubb, under registreringen måste vi du ange konsumentgrupp som ska användas för att läsa in dina data. Den här konsumentgrupp får inte delas. Om den delas underliggande händelsehubben automatiskt kopplas från en läsare slumpmässigt.

## <a name="i-see-my-data-but-theres-a-lag"></a>Mina data visas, men det finns en fördröjning
Här finns skäl visas delar av informationen i din miljö i det [tid serien Insights-portalen](https://insights.timeseries.azure.com).

### <a name="your-environment-is-getting-throttled"></a>Hämta begränsas din miljö
Gränsen tillämpas baserat på SKU miljötyp och kapacitet. Alla händelsekällor i miljön dela den här kapaciteten. Om händelsekällan för din IoT-hubb eller händelsehubb är push-överföring av data utöver de tvingande gränserna, ser du begränsning och en fördröjning.

I följande diagram visas en gång serien insikter miljö som har en SKU S1 och en kapacitet på 3. Det kan 3 miljoner ingångshändelser per dag.

![Aktuell miljö artikelnummerkapaciteten](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Anta att den här miljön är vill föra in meddelanden från en händelsehubb med meddelande om ingångs-sats som visas i följande diagram:

![Exempel meddelanden om ingångs-sats för en händelsehubb](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

I diagrammet visas de dagliga ingång frekvensen är ~ 67,000 meddelanden. Denna kurs översätter ungefär 46 meddelanden varje minut. Om hub händelsemeddelande förenklas till en enda gång serien insikter händelse, ser den här miljön ingen begränsning. Om hub händelsemeddelande förenklas till 100 tid serien insikter händelser, ska sedan 4,600 händelser vara inhämtas varje minut. En S1 SKU-miljö som har en kapacitet på 3 kan endast 2 100 ingångshändelser varje minut (1 miljon händelser per dag = 700 händelser per minut på 3 enheter = 2 100 händelser per minut). Därför kan du se en fördröjning på grund av begränsning. 

En översikt över hur förenkla logik fungerar, se [stöds JSON former](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Rekommenderade åtgärder
Om du vill åtgärda fördröjningen ökar du kapaciteten SKU för din miljö. Mer information finns i [så här skalar du tid serien insikter miljön](time-series-insights-how-to-scale-your-environment.md).

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>Du sänder historiska data och orsakar långsam ingång
Om du ansluter en befintlig datakälla för händelsen, är det troligt att din IoT-hubb eller händelsehubb redan har data i den. Miljön startar så att data från början av den händelsekälla kvarhållningsperiod för meddelandet. 

Detta är standardinställningen och inte kan åsidosättas. Du kan engagera begränsning och det kan ta en stund att titta på mata in historiska data.

#### <a name="recommended-steps"></a>Rekommenderade åtgärder
Om du vill åtgärda fördröjningen gör du följande:
1. Öka artikelnummerkapaciteten till det högsta tillåtna värdet (10 i det här fallet). När kapaciteten ökas startar processen ingång fånga upp mycket snabbare. Kan du visualisera hur snabbt du Ja via Tillgänglighetsdiagrammet i den [tid serien Insights-portalen](https://insights.timeseries.azure.com). Du debiteras för bättre kapacitet.
2. När fördröjningen fångats, minska artikelnummerkapaciteten tillbaka till normal ingång-hastighet.

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Min händelsekälla *tidsstämpel egenskapsnamn* inställningen fungerar inte
Kontrollera att namnet och värdet överensstämmer med följande regler:
* Egenskapsnamnet tidsstämpel är _skiftlägeskänsliga_.
* Egenskapsvärdet tidsstämpel som kommer från din händelsekälla som JSON-strängen ska ha formatet _åååå-MM-ddTHH. FFFFFFFK_. Ett exempel på sådana sträng är ”2008-04-12T12:53Z”.
