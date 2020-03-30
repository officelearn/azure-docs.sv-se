---
title: Diagnostisera, felsöka och lösa problem – Insikter i Azure Time Series | Microsoft-dokument
description: I den här artikeln beskrivs hur du diagnostiserar, felsöker och löser vanliga problem i azure time series insights-miljön.
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
ms.openlocfilehash: 209df97169c71d910677ffdb2e2b12593882445b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152608"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnostisera och lösa problem i time series insights-miljön

I den här artikeln beskrivs några problem som kan uppstå i azure Time Series Insights-miljön. Artikeln erbjuder potentiella orsaker och lösningar för lösning.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Läs mer om vanliga Time Series Insights-utmaningar och begränsningar av kunderna.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problem: inga data visas

Inga data i Utforskaren för [Azure Time Series Insights](https://insights.timeseries.azure.com) kan uppstå av flera vanliga orsaker:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Orsak A: händelsekälladata är inte i JSON-format

Azure Time Series Insights stöder endast JSON-data. För JSON-exempel läser du [JSON-former som stöds](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Orsak B: händelsekällanyckeln saknar en nödvändig behörighet

* För en IoT-hubb i Azure IoT Hub måste du ange nyckeln som har **tjänstanslutningsbehörighet.** Välj antingen **iothubowner-** eller **serviceprinciper** eftersom båda har **behörighet för tjänstanslutning.**

   [![IoT Hub-tjänst ansluter behörigheter](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* För en händelsenav i Azure Event Hubs måste du ange nyckeln som har **lyssningsbehörighet.** Någon av **läs-** eller **hanteringsprinciperna** fungerar eftersom de båda har **lyssningsbehörighet.**

   [![Behörigheter för att lyssna på händelsehubben](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Orsak C: konsumentgruppen som tillhandahålls är inte exklusiv för Time Series Insights

När du registrerar en IoT-hubb eller en händelsenav är det viktigt att ange den konsumentgrupp som du vill använda för att läsa data. Den här konsumentgruppen *kan inte delas.* Om konsumentgruppen delas kopplar det underliggande IoT-navet eller händelsehubben automatiskt och kopplar slumpmässigt från en av läsarna. Tillhandahålla en unik konsumentgrupp för Time Series Insights att läsa från.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Orsak D: miljön har just etablerats

Data visas i Utforskaren för Time Series Insights inom några minuter efter att miljön och dess data först har skapats.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problem: vissa data visas, men data saknas

När data bara visas delvis och data verkar släpa efter bör du överväga flera möjligheter.

### <a name="cause-a-your-environment-is-being-throttled"></a>Orsak A: din miljö begränsas

[Begränsning](time-series-insights-environment-mitigate-latency.md) är ett vanligt problem när miljöer etableras när du har skapat en händelsekälla som har data. Azure IoT Hub och Azure Events Hubs lagrar data i upp till sju dagar. Time Series Insights börjar alltid med den äldsta händelsen i händelsekällan (först in, först ut eller *FIFO*).

Om du till exempel har 5 miljoner händelser i en händelsekälla när du ansluter till en S1-, time series-statistikmiljö med en enhet läser Time Series Insights cirka 1 miljon händelser per dag. Det kan se ut som Time Series Insights upplever fem dagars latens. Men vad som händer är att miljön stryps.

Om du har gamla händelser i händelsekällan kan du närma dig begränsning på ett av två sätt:

- Ändra lagringsgränserna för händelsekällan för att ta bort gamla händelser som du inte vill visa i Time Series Insights.
- Etablera en större miljöstorlek (antal enheter) för att öka dataflödet för gamla händelser. Med hjälp av föregående exempel, om du ökar samma S1-miljö till fem enheter för en dag, bör miljön komma ikapp inom en dag. Om din steady-state händelseproduktion är 1 miljon eller färre händelser per dag, kan du minska kapaciteten för händelsen till en enhet när den hinner ikapp.

Begränsningsgränsen tillämpas baserat på miljöns SKU-typ och kapacitet. Alla händelsekällor i miljön delar den här kapaciteten. Om händelsekällan för din IoT-hubb eller händelsenav skickar data utanför de påtvingade gränserna får du begränsning och fördröjning.

Följande bild visar en Time Series Insights-miljö som har en SKU på S1 och en kapacitet på 3. Det kan inträngning 3 miljoner händelser per dag.

[![Miljö SKU nuvarande kapacitet](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Anta till exempel att en miljö får meddelanden från en händelsehubb. Den dagliga inträngningsfrekvensen är ~67 000 meddelanden. Den här kursen innebär cirka 46 meddelanden varje minut. 

* Om varje händelsehubbmeddelande förenklas till en enskild Time Series Insights-händelse sker begränsningen inte. 
* Om varje händelsehubbmeddelande förenklas till 100 Time Series Insights-händelser, bör 4 600 händelser intas varje minut. 

En S1 SKU-miljö som har en kapacitet på 3 kan endast gå in 2 100 händelser varje minut (1 miljon händelser per dag = 700 händelser per minut vid tre enheter = 2 100 händelser per minut). 

För en hög nivå förståelse för hur förenkling logik fungerar, läsa [Stöds JSON former](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Rekommenderade upplösningar för överdriven begränsning

För att åtgärda fördröjningen, öka SKU-kapaciteten i din miljö. Mer information finns i [Skala din time series insights-miljö](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Orsak B: första intag av historiska data saktar inträngning

Om du ansluter en befintlig händelsekälla är det troligt att din IoT-hubb eller händelsenav redan innehåller data. Miljön börjar hämta data från början av händelsekällans meddelandekvarhållningsperiod. Den här standardbearbetningen kan inte åsidosättas. Du kan aktivera begränsning. Begränsning kan ta ett tag att komma ikapp när den intar historiska data.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Rekommenderade upplösningar för stora initiala intag

Så här åtgärdar du fördröjningen:

1. Öka SKU-kapaciteten till det högsta tillåtna värdet (10, i det här fallet). När du har ökat kapaciteten börjar ingressprocessen komma ikapp mycket snabbare. Du debiteras för den ökade kapaciteten. Om du vill visualisera hur snabbt du kommer ikapp kan du visa tillgänglighetsdiagrammet i [Utforskaren För Tidsseriestatistik](https://insights.timeseries.azure.com).

2. När fördröjningen är ikapp, minska SKU kapacitet till din normala inträngningshastighet.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problem: data visades tidigare men visas inte längre

TSD intar inte längre data, men händelser strömmas fortfarande till Iot Hub eller Event Hub

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-updating"></a>Orsak A: hubbåtkomstnyckeln har återskapats och din miljö behöver uppdateras

Det här problemet uppstår när nyckeln som anges när du skapar händelsekällan inte längre är giltig. Du skulle se telemetri i din hubb men inga ingående mottagna meddelanden i Time Series Insights. Om du är osäker på om nyckeln har återskapats eller inte kan du söka i aktivitetsloggen för händelsehubbar efter "Skapa eller uppdatera namnområdesauktoriseringsregler" eller söka i "Skapa eller uppdatera IotHub-resurs" för IoT-hubb.

Om du vill uppdatera time series insights-miljön med den nya nyckeln öppnar du hubbresursen i Azure-portalen och kopierar den nya nyckeln. Navigera till din TSD-resurs och klicka på händelsekällor. 

   [![Uppdatera nyckel.](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Välj de händelsekällor som har stoppats från, klistra in den nya nyckeln och klicka på Spara.

   [![Uppdatera nyckel.](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: namninställningen för tidsstämpelns tidsstämpelsegenskap fungerar inte

Kontrollera att namn och värde för tidsstämpelgenskapen överensstämmer med följande regler:

* Namnet på tidsstämpelns egenskap är skiftlägeskänsligt.
* Egenskapsvärdet för tidsstämpel som kommer från händelsekällan som en JSON-sträng bör ha formatet _yyyy-MM-ddTHH:mm:ss. FFFFFFFK_. Ett exempel är **2008-04-12T12:53Z**.

Det enklaste sättet att se till att ditt tidsstämpelegenskapsnamn fångas in och fungerar som det ska är att använda Utforskaren För Tidsseriestatistik. I utforskaren Time Series Insights väljer du en tidsperiod när du har angett egenskapsnamnet för tidsstämpeln. Högerklicka på markeringen och välj sedan alternativet **Utforska händelser.**

Den första kolumnrubriken ska vara namnet på tidsstämpelns egenskap. Bredvid ordet **Tidsstämpel** **($ts)** visas.

Följande värden visas inte:

- *(abc)*: Anger att Time Series Insights läser datavärdena som strängar.
- *Kalenderikon:* Anger att Time Series Insights läser datavärdet som *datetime*.
- *#*: Anger att Time Series Insights läser datavärdena som ett heltal.

## <a name="next-steps"></a>Nästa steg

- Läs om [hur du minskar svarstiden i Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Lär dig hur du [skalar time series insights-miljön](time-series-insights-how-to-scale-your-environment.md).