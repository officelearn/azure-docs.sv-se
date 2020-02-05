---
title: Diagnostisera, Felsök och lös problem – Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du diagnostiserar, felsöker och löser vanliga problem i din Azure Time Series Insightss miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 35b330f27ba87aa18ce2c2f275a7b19fdae3cb65
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024423"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnostisera och lösa problem i din Time Series Insightss miljö

I den här artikeln beskrivs några problem som kan uppstå i din Azure Time Series Insightss miljö. Artikeln innehåller möjliga orsaker och lösningar för lösning.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Läs om vanliga Time Series Insights kund utmaningar och begränsningar.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: inga data visas

Inga data i [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com) kan uppstå på grund av flera vanliga orsaker:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Orsak till: händelse källans data är inte i JSON-format

Azure Time Series Insights stöder endast JSON-data. För JSON-exempel kan du läsa [JSON-former som stöds](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Orsak B: händelse käll nyckeln saknar en nödvändig behörighet

* För en IoT-hubb i Azure IoT Hub måste du ange den nyckel som har behörighet för **tjänst anslutning** . Välj antingen **iothubowner** -eller **service** principer eftersom båda har behörigheter för **tjänst anslutning** .

   [![IoT Hub service Connect-behörigheter](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* För en Event Hub i Azure Event Hubs måste du ange den nyckel som har **avlyssnings** behörigheter. Någon av metoderna för att **läsa** eller **Hantera** fungerar eftersom båda har **avlyssnings** behörighet.

   [lyssnande behörigheter för ![Event Hub](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Orsak C: den angivna konsument gruppen är inte exklusiv för Time Series Insights

När du registrerar en IoT-hubb eller en händelsehubben är det viktigt att ange den konsument grupp som du vill använda för att läsa data. Det *går inte att dela*den här konsument gruppen. Om konsument gruppen delas, kommer den underliggande IoT-hubben eller händelsehubben att automatiskt koppla bort en av läsarna. Ange en unik konsument grupp för Time Series Insights att läsa från.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Orsak D: miljön har precis etablerats

Data visas i Time Series Insights Explorer inom några minuter efter det att miljön och dess data först har skapats.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: vissa data visas, men data saknas

När data endast visas delvis och data verkar vara i ett släp, bör du överväga flera möjligheter.

### <a name="cause-a-your-environment-is-being-throttled"></a>Orsak A: din miljö begränsas

[Begränsning](time-series-insights-environment-mitigate-latency.md) är ett vanligt problem när miljöer är etablerade när du har skapat en händelse källa som innehåller data. Azure IoT Hub-och Azure Events-hubbar lagrar data i upp till sju dagar. Time Series Insights startar alltid med den äldsta händelsen i händelse källan (först in, först ut eller *FIFO*).

Om du till exempel har 5 000 000 händelser i en händelse källa när du ansluter till en enhet Time Series Insights miljö i S1, Time Series Insights läser cirka 1 000 000 händelser per dag. Det kan se ut som Time Series Insights har fem dagars latens. Men vad som händer är att miljön begränsas.

Om du har gamla händelser i din händelse källa kan du använda begränsning på något av följande sätt:

- Ändra händelse källans kvarhållningsintervall för att hjälpa till att ta bort gamla händelser som du inte vill ska visas i Time Series Insights.
- Etablera en större miljö storlek (antal enheter) för att öka data flödet för gamla händelser. I föregående exempel, om du ökar samma S1-miljö till fem enheter under en dag, bör miljön fångas upp inom en dag. Om din stabila händelse produktion är 1 000 000 eller färre händelser per dag kan du minska händelsens kapacitet till en enhet när den har fångats upp.

Begränsnings gränsen tillämpas baserat på miljöns SKU-typ och kapacitet. Alla händelse källor i miljön delar den här kapaciteten. Om händelse källan för din IoT-hubb eller händelsehubben skickar data bortom de tvingade gränserna får du en begränsning och en fördröjning.

Följande bild visar en Time Series Insights miljö som har en SKU av S1 och en kapacitet på 3. Det kan intränga 3 000 000 händelser per dag.

[![miljö-SKU aktuell kapacitet](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Anta till exempel att en miljö matar in meddelanden från en händelsehubben. Den dagliga ingångs frekvensen är ~ 67 000 meddelanden. Den här hastigheten översätter cirka 46 meddelanden varje minut. 

* Om varje Event Hub-meddelande utplattas till ett enda Time Series Insights-händelse inträffar inte begränsningen. 
* Om varje Event Hub-meddelande utplattas till 100 Time Series Insights händelser ska 4 600-händelser matas in varje minut. 

En S1 SKU-miljö som har en kapacitet på 3 kan bara intränga 2 100 händelser varje minut (1 000 000 händelser per dag = 700 händelser per minut vid tre enheter = 2 100 händelser per minut). 

För en övergripande förståelse för hur förenkling av logik fungerar, kan du läsa [JSON-former som stöds](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Rekommenderade lösningar för överdriven begränsning

Om du vill åtgärda fördröjningen ökar du SKU-kapaciteten för din miljö. Mer information finns [i skala din Time Series Insightss miljö](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Orsak B: inledande inmatning av historiska data går långsamt

Om du ansluter en befintlig händelse källa är det troligt att din IoT-hubb eller händelsehubben redan innehåller data. Miljön börjar hämta data från början av händelse källans kvarhållning av meddelanden. Den här standard bearbetningen kan inte åsidosättas. Du kan engagera begränsning. Begränsningen kan ta en stund att fånga upp när den matar in historiska data.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Rekommenderade lösningar för stor inledande inmatning

Så här åtgärdar du fördröjningen:

1. Öka SKU-kapaciteten till det högsta tillåtna värdet (10, i det här fallet). När du har ökat kapaciteten börjar ingångs processen att fånga upp mycket snabbare. Du debiteras för den ökade kapaciteten. Om du vill visualisera hur snabbt du får upp kan du Visa tillgänglighets diagrammet i [Time Series Insights Explorer](https://insights.timeseries.azure.com).

2. När fördröjningen har fångats upp minskar du SKU-kapaciteten till din normala ingångs taxa.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: min händelse källans egenskaps namn för tidsstämpel fungerar inte

Se till att egenskaps namnet och värdet för tidsstämpeln uppfyller följande regler:

* Egenskaps namnet för tidsstämpeln är Skift läges känsligt.
* Värdet för egenskapen tidsstämpel som kommer från din händelse källa som en JSON-sträng ska ha formatet _ÅÅÅÅ-MM-ddTHH: mm: SS. FFFFFFFK_. Ett exempel är **2008-04-12T12:53Z**.

Det enklaste sättet att se till att namnet på Tidsstämpelns egenskap är infångat och fungerar korrekt är att använda Time Series Insights Explorer. I Time Series Insights Explorer, med hjälp av diagrammet, väljer du en tids period när du har angett namnet på Tidsstämpelns egenskap. Högerklicka på markeringen och välj sedan alternativet för att **utforska händelser** .

Den första kolumn rubriken ska vara namnet på din timestamp-egenskap. Vid **tidsstämpeln**för Word visas **($TS)** .

Följande värden kommer inte att visas:

- *(ABC)* : anger att Time Series Insights läser data värden som strängar.
- *Kalender ikon*: anger att Time Series Insights läser data värdet som *datetime*.
- *#* : anger att Time Series Insights läser data värden som ett heltal.

## <a name="next-steps"></a>Nästa steg

- Läs om [hur du minimerar svars tiden i Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Lär dig [hur du skalar din Time Series Insightss miljö](time-series-insights-how-to-scale-your-environment.md).