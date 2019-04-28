---
title: Diagnostisera, felsöka och lösa problem i Azure Time Series Insights | Microsoft Docs
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
ms.custom: seodec18
ms.openlocfilehash: ad739041ebd20f9940e305efb19807df4c73cb8e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759721"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnostisera och lösa problem i din Time Series Insights-miljö

Den här artikeln beskriver några problem som kan uppstå i din Azure Time Series Insights-miljö. Artikeln erbjuder möjliga orsaker och lösningar för matchning.

## <a name="video"></a>Video

### <a name="in-this-video-we-cover-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Vi går igenom vanliga Time Series Insights kundutmaningar och åtgärder för i den här videon:</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-one-no-data-is-shown"></a>Problem med en: inga data visas

Inga data i den [Azure Time Series Insights explorer](https://insights.timeseries.azure.com) kan ha flera vanliga orsaker:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Orsak s: händelse källdata finns inte i JSON-format

Azure Time Series Insights har endast stöd för JSON-data. JSON-exempel finns [stöds JSON-former](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Orsak B: händelse källa nyckel saknas för en behörighet som krävs

* För en IoT-hubb i Azure IoT Hub måste du ange den nyckel som har **tjänsten ansluta** behörigheter. Något av de **iothubowner** eller **service** principer kommer att fungera eftersom de båda har **tjänsten ansluta** behörigheter.

   ![IoT Hub-tjänsten ansluta behörigheter](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

* För en händelsehubb i Azure Event Hubs, måste du ange den nyckel som har **lyssna** behörigheter. Något av de **läsa** eller **hantera** principer kommer att fungera eftersom de båda har **lyssna** behörigheter.

   ![Event hub listen behörigheter](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Orsak C: konsumentgruppen tillhandahålls inte är exklusivt för Time Series Insights

När du registrerar en IoT-hubb eller en event hub, är det viktigt att ställa in konsumentgrupp som du vill använda för att läsa data. Den här konsumentgruppen *kan inte delas*. Om konsumentgruppen delas den underliggande IoT hub eller event hub automatiskt och slumpmässigt kopplar från en av läsarna. Ange en unik konsumentgrupp för Time Series Insights att läsa från.

## <a name="problem-two-some-data-is-shown-but-data-is-missing"></a>Problem med två: vissa data visas, men data saknas

När data visas endast delvis och data verkar vara släpar, bör du överväga alternativen.

### <a name="cause-a-your-environment-is-being-throttled"></a>Orsak s: miljön har begränsats

Begränsning är ett vanligt problem när miljöer etableras när du har skapat en händelsekälla som innehåller data. Azure IoT Hub och Azure händelser Hubs kan du lagra data i upp till sju dagar. Time Series Insights alltid börja med den äldsta händelsen i källan (först in, först ut-metoden, eller *FIFO*).

Om du har 5 miljoner händelser i en händelsekälla när du ansluter till en S1, läser integrerade Time Series Insights-miljö, Time Series Insights cirka 1 miljon händelser per dag. Det verkar som om Time Series Insights har fem dagars svarstid. Vad som händer är dock att miljön har begränsats.

Om du har gamla händelser i din händelsekälla kan ITU du begränsning i ett av två sätt:

- Ändra din händelsekälla gräns för datakvarhållning för att ta bort gamla händelser som du inte vill ska visas i Time Series Insights.
- Etablera en större miljöstorlek (antal enheter) för att öka genomflödet av gamla händelser. Med hjälp av föregående exempel, om du ökar samma S1-miljö till fem enheter under en dag, miljön bör komma ifatt inom en dag. Om din stabil händelse i produktionsmiljön är 1 miljon eller färre händelser per dag, kan du minska kapaciteten för händelsen till en enhet efter att den fångar upp.

Gränsen tillämpas utifrån den miljö SKU-typen och kapacitet. Alla händelsekällor i miljön dela den här kapaciteten. Om händelsekällan för din IoT hub eller event hub skickar data utöver de tvingande gränserna, visas begränsning och en fördröjning.

Följande bild visar en Time Series Insights-miljö som har en SKU för S1 och en kapacitet på 3. Kan den ingående 3 miljoner händelser per dag.

![Aktuell miljö SKU-kapacitet](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Anta exempelvis att den här miljön matar in meddelanden från en event hub. Följande bild visar den ingående hastigheten:

![Exempel inledningshändelser priset för en händelsehubb](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Den dagliga ingångshändelser avgiften är ~ 67,000 meddelanden. Priset som översätts till cirka 46 meddelanden varje minut. Om varje event hub-meddelande är utplattad till en enda Time Series Insights-händelse, inträffar begränsning inte. Om varje event hub-meddelande är utplattad på 100 Time Series Insights-händelser, ska 4,600 händelser matas in varje minut. En S1 SKU-miljö som har en kapacitet på 3 kan endast 2 100 ingångshändelser varje minut (1 miljon händelser per dag = 700 händelser per minut på tre enheter = 2 100 händelser per minut). För den här konfigurationen kan se du en fördröjning på grund av begränsning. 

En översikt över hur förenkling logic fungerar, se [stöds JSON-former](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Rekommenderade lösningar för hög begränsning

Öka SKU-kapaciteten för din miljö för att åtgärda fördröjningen. Mer information finns i [skala din Time Series Insights-miljö](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Orsak B: inledande inmatning av historiska data saktar ingress

Om du ansluter en befintlig händelsekälla, är det troligt att din IoT hub eller event hub redan innehåller data. Miljön startar hämta data från början av den händelsekälla kvarhållningsperiod för meddelandet. Detta är standard-bearbetning och kan inte åsidosättas. Du kan engagera begränsning. Begränsning kan ta en stund att läsa den matar in historiska data.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Rekommenderade lösningar för stora inledande inmatning

Åtgärda fördröjningen:

1. Öka SKU-kapacitet till det högsta tillåtna värdet (i det här fallet 10). När du ökar kapaciteten startar ingress-processen komma ikapp mycket snabbare. Du debiteras för ökad kapacitet. För att visualisera hur snabbt du Ja och så upp, som du kan visa Tillgänglighetsdiagrammet i den [Time Series Insights explorer](https://insights.timeseries.azure.com). 

2. När fördröjningen fått allt, minska SKU-kapacitet till normal ingångshändelser.

## <a name="problem-three-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem med tre: min händelsekälla tidsstämpel egenskapen name inställningen fungerar inte

Se till att den egenskapsnamn för tidsstämpeln och värdet överensstämmer med följande regler:

* Egenskapsnamnet för tidsstämpeln är skiftlägeskänsligt.
* Tidsstämpel-egenskapsvärde som kommer från din händelsekälla som en JSON-sträng som ska ha formatet _åååå-MM-ddTHH. FFFFFFFK_. Ett exempel är **2008-04-12T12:53Z**.

Det enklaste sättet att säkerställa att din egenskapsnamn för tidsstämpeln är skapas och fungerar är att använda i Time Series Insights-Utforskaren. Välj en tidsperiod när du har angett egenskapsnamn för tidsstämpeln i Time Series Insights explorer med hjälp av tabellen. Högerklicka på markeringen och välj sedan den **utforska händelser** alternativet. 

Första kolumnrubriken ska vara egenskapsnamn för tidsstämpeln. Bredvid ordet **tidsstämpel**, bör du se **($ts)**.

Du bör inte se följande värden:

- *(abc)*: Anger att Time Series Insights läser datavärdena som strängar.
- *Ikon för kalender*: Anger att Time Series Insights läser datavärdet är *datetime*.
- *#*: Anger att Time Series Insights läser datavärdena som ett heltal.

## <a name="next-steps"></a>Nästa steg

- Om du behöver hjälp att starta en konversation i den [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights).

- Vilka supportalternativ använda [Azure-supporten](https://azure.microsoft.com/support/options/).
