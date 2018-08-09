---
title: Diagnostisera och lösa problem i Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du diagnostiserar, felsöka och lösa vanliga problem som kan uppstå i din Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: b05b824d8d35351030ca466566f14e4249d4b99d
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626628"
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnostisera och lösa problem i din Time Series Insights-miljö

## <a name="problem-1-no-data-is-shown"></a>Problem 1: Inga data visas
Det finns flera vanliga orsaker varför du inte kanske se dina data i den [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Möjlig orsak s: händelse källdata är inte i JSON-format
Azure Time Series Insights har stöd för JSON-data. JSON-exempel finns [stöds JSON-former](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Möjlig orsak B: händelse källa nyckel saknas för en behörighet som krävs
* För en IoT-hubb, måste du ange den nyckel som har **tjänsten ansluta** behörighet.

   ![IoT Hub-tjänsten anslutningsbehörighet](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Som visas i den föregående bilden är någon av principerna **iothubowner** och **service** skulle fungera, eftersom båda har **tjänsten ansluta** behörighet.
   
* För en händelsehubb, måste du ange den nyckel som har **lyssna** behörighet.

   ![Event hub listen behörighet](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Som visas i den föregående bilden är någon av principerna **läsa** och **hantera** skulle fungera, eftersom båda har **lyssna** behörighet.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Möjlig orsak C: konsumentgruppen tillhandahålls inte är exklusivt för Time Series Insights
Under registreringen av am IoT-hubb eller en händelsehubb kan ange du konsumentgrupp som ska användas för läsning av data. Den här konsumentgruppen måste **inte** delas. Om konsumentgruppen delas underliggande event hub automatiskt kopplas från någon av läsarna slumpmässigt. Ange en unik konsumentgrupp för Time Series Insights att läsa från.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problem 2: Vissa data visas, men vissa saknas
När du kan se data delvis, men data som släpar efter, finns det flera möjligheter att tänka på:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Möjlig orsak s: miljön komma begränsas
Detta är ett vanligt problem när miljöer har etablerats efter skapandet av en händelsekälla med data.  Azure IoT-hubbar och händelser Hubs lagra data upp till sju dagar.  TSI startar alltid från den äldsta händelsen (FIFO) inom händelsekällan.  Så om du har fem miljoner händelser i en händelsekälla när du ansluter till en S1-, integrerade TSI miljö TSI läser en miljon händelser per dag.  Detta visas för att se ut som om TSI har fem dagars fördröjning vid en första titt.  Vad som faktiskt händer är att miljön har begränsats.  Om du har gamla händelser i din händelsekälla kan du itu med ett av två sätt:

- Ändra din händelsekälla gräns för datakvarhållning för att ta bort gamla händelser som du inte vill ska visas i TSD
- Etablera en större miljö (sett till antalet enheter) för att öka genomströmning på gamla händelser.  Med exemplet ovan, om du ökar den samma S1-miljön till fem enheter under en dag, bör miljön inspelad nu inom dagen.  Om din stabilt händelse i produktionsmiljön är 1 miljon eller mindre händelser/dag, kan du minska kapaciteten för händelsen tillbaka ned till en enhet när den har fått allt.  

Gränsen tillämpas utifrån den miljö SKU-typen och kapacitet. Alla händelsekällor i miljön dela den här kapaciteten. Om händelsekällan för din IoT Hub eller event hub är push-överföra data utöver de tvingande gränserna, visas begränsning och en fördröjning.

Följande diagram visar en Time Series Insights-miljö som har en SKU för S1 och en kapacitet på 3. Kan den ingående 3 miljoner händelser per dag.

![Aktuell miljö SKU-kapacitet](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Anta exempelvis att den här miljön är mata in meddelanden från en event hub. Notera den ingående hastighet som visas i följande diagram:

![Exempel inledningshändelser priset för en händelsehubb](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

I diagrammet visas är den dagliga ingångshändelser avgiften ~ 67,000 meddelanden. Priset motsvarar ungefär 46 meddelanden varje minut. Om varje event hub-meddelande är utplattad till en enda Time Series Insights-händelse, ser den här miljön ingen begränsning. Om varje event hub-meddelande är utplattad på 100 Time Series Insights-händelser ska sedan 4,600 händelser matas in varje minut. En S1 SKU-miljö som har en kapacitet på 3 kan endast 2 100 ingångshändelser varje minut (1 miljon händelser per dag = 700 händelser per minut på 3 enheter = 2 100 händelser per minut). Därför kan du se en fördröjning på grund av begränsning. 

En översikt över hur förenkling logic fungerar, se [stöds JSON-former](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Rekommenderade Lösningssteg för långa begränsning
Öka SKU-kapaciteten för din miljö för att åtgärda fördröjningen. Mer information finns i [så här skalar du din Time Series Insights-miljö](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Möjlig orsak B: inledande inmatning av historiska data som orsakar långsam ingress
Om du ansluter en befintlig händelsekälla, är det troligt att din IoT Hub eller event hub redan har data i den. Miljön startar hämta data från början av den händelsekälla kvarhållningsperiod för meddelandet.

Detta är standardbeteendet och kan inte åsidosättas. Du kan engagera begränsning och det kan ta en stund att komma ifatt med mata in historiska data.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Rekommenderade Lösningssteg av stora inledande inmatning
Om du vill åtgärda fördröjningen, gör du följande:
1. Öka SKU-kapacitet till det högsta tillåtna värdet (10 i det här fallet). När kapaciteten ökas startar processen ingående fånga upp mycket snabbare. Du kan visualisera hur snabbt du Ja via Tillgänglighetsdiagrammet i den [Time Series Insights explorer](https://insights.timeseries.azure.com). Du debiteras för ökad kapacitet.
2. När fördröjningen fått allt, minska SKU-kapacitet till normal ingångshändelser.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem 3: Min händelsekällans *egenskapsnamn för tidsstämpeln* inställningen fungerar inte
Se till att namnet och värdet överensstämmer med följande regler:
* Egenskapsnamnet för tidsstämpeln är _skiftlägeskänsliga_.
* Tidsstämpel-egenskapsvärde som kommer från din händelsekälla som en JSON-sträng som ska ha formatet _åååå-MM-ddTHH. FFFFFFFK_. Ett exempel på sådana en sträng är ”2008-04-12T12:53Z”.

Det enklaste sättet att se till att din *egenskapsnamn för tidsstämpeln* samlas in och fungerar är att använda TSI-Utforskaren.  TSI-Utforskaren med hjälp av diagrammet, väljer en tidsperiod när du har angett den *egenskapsnamn för tidsstämpeln*.  Högerklicka på markeringen och välj den *utforska händelser* alternativet.  Första kolumnrubriken ska vara din *egenskapsnamn för tidsstämpeln* har en *($ts)* bredvid ordet *tidsstämpel*, snarare än:
- *(abc)* , vilket skulle indikera TSI läser datavärdena som strängar
- *Ikon för kalender*, vilket skulle indikera TSI läser datavärdet är *datetime*
- *#*, som visar att TSI läser datavärdena som ett heltal


## <a name="next-steps"></a>Nästa steg
- För ytterligare hjälp kan du starta en konversation på den [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Du kan också använda [Azure-supporten](https://azure.microsoft.com/support/options/) för vilka supportalternativ.
