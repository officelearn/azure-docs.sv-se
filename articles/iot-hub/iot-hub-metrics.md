---
title: "Använda mått för att övervaka Azure IoT Hub | Microsoft Docs"
description: "Hur du använder Azure IoT Hub mått för att utvärdera och övervaka den övergripande hälsan för din IoT-hubbar."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cafe5009b8e96b147b5bbed1957024f6d96feb58
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="understand-iot-hub-metrics"></a>Förstå IoT-hubb mått
IoT-hubb mått får du bättre data om tillståndet för Azure IoT-resurser i din Azure-prenumeration. IoT-hubb mått kan du utvärdera den övergripande hälsan för IoT-hubb-tjänsten och de enheter som är anslutna till den. Användarinriktad statistik är viktiga eftersom de hjälper dig att se vad som händer din IoT-hubb och hjälper dig att grundorsaken problem och utan att behöva kontakta Azure-supporten.

Mått är aktiverade som standard. Du kan visa IoT-hubb mått från Azure-portalen.

## <a name="how-to-view-iot-hub-metrics"></a>Så här visar du mått för IoT-hubb
1. Skapa en IoT-hubb. Du hittar anvisningar om hur du skapar en IoT-hubb i den [Kom igång] [ lnk-get-started] guide.
2. Öppna bladet för din IoT-hubb. Därifrån klickar du på **mått**.
   
    ![][1]
3. Du kan visa måtten för din IoT-hubb och skapa anpassade vyer av dina från bladet mått. Du kan välja att skicka data mått till en slutpunkt för Händelsehubbar eller ett Azure Storage-konto genom att klicka på **diagnostikinställningarna**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT-hubb mått och hur du använder dem.
IoT-hubb innehåller flera mått för att ge dig en översikt över hälsotillståndet för din hubb och det totala antalet anslutna enheter. Du kan kombinera information från flera mått för att rita en större bild av tillståndet för IoT-hubben. I följande tabell beskrivs de mätvärden varje IoT-hubb spårar och hur varje mått relaterar till övergripande status för IoT-hubben.

|Mått|Mått visningsnamn|Enhet|Sammansättningstyp|Beskrivning|
|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Telemetri meddelande Skicka försök|Antal|Totalt|Antal meddelanden enhet till moln telemetri försökte skickas till din IoT-hubb|
|d2c.telemetry.Ingress.Success|Telemetri meddelanden som skickas|Antal|Totalt|Antal enhet till moln telemetri meddelanden som skickats till din IoT-hubb|
|c2d.commands.egress.Complete.Success|Kommandon som slutförd|Antal|Totalt|Antal moln till enhet kommandon har slutförts av enheten|
|c2d.commands.egress.Abandon.Success|Avbrutna kommandon|Antal|Totalt|Antal moln till enhet kommandon avbrutna av enheten|
|c2d.commands.egress.Reject.Success|Kommandon som avvisat|Antal|Totalt|Antal moln till enhet kommandon som avvisats av enheten|
|devices.totalDevices|Totalt antal enheter|Antal|Totalt|Antalet enheter som registrerats för din IoT-hubb|
|devices.connectedDevices.allProtocol|Anslutna enheter|Antal|Totalt|Antalet enheter som är anslutna till din IoT-hubb|
|d2c.telemetry.egress.Success|Telemetri meddelanden|Antal|Totalt|Antal gånger som meddelanden som skrivits till slutpunkter (totalt)|
|d2c.telemetry.egress.dropped|Ignorerade meddelanden|Antal|Totalt|Antal meddelanden som utelämnats eftersom de matchade inte några vägar och fallback vägen har inaktiverats|
|d2c.telemetry.egress.orphaned|Överblivna meddelanden|Antal|Totalt|Antal meddelanden som inte matchar några vägar inklusive återställningsplats flöde|
|d2c.telemetry.egress.Invalid|Ogiltig meddelanden|Antal|Totalt|Antalet meddelanden som inte levereras på grund av inkompatibilitet med slutpunkten|
|d2c.telemetry.egress.fallback|Meddelanden som matchar villkoret återställningsplats|Antal|Totalt|Antal meddelanden som skrivs till fallback-slutpunkten|
|d2c.endpoints.egress.eventHubs|Skicka meddelanden till Event Hub-slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till Event Hub-slutpunkter|
|d2c.endpoints.latency.eventHubs|Meddelandet svarstid för Event Hub-slutpunkter|Millisekunder|Genomsnitt|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång i Event Hub slutpunkt, i millisekunder|
|d2c.endpoints.egress.serviceBusQueues|Skicka meddelanden till Service Bus-kö slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till Service Bus-kö slutpunkter|
|d2c.endpoints.latency.serviceBusQueues|Meddelandet svarstid för Service Bus-kö-slutpunkter|Millisekunder|Genomsnitt|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång till en slutpunkt för Service Bus-kö, i millisekunder|
|d2c.endpoints.egress.serviceBusTopics|Skicka meddelanden till Service Bus-ämne slutpunkter|Antal|Totalt|Antal gånger som meddelanden som skrivits till Service Bus-ämne slutpunkter|
|d2c.endpoints.latency.serviceBusTopics|Meddelandet svarstiden för Service Bus-ämne slutpunkter|Millisekunder|Genomsnitt|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång till en slutpunkt för Service Bus-ämne, i millisekunder|
|d2c.endpoints.egress.builtIn.events|Skicka meddelanden till inbyggd slutpunkt (meddelanden/händelser)|Antal|Totalt|Antal gånger som meddelanden som skrivits till inbyggd slutpunkt (meddelanden/händelser)|
|d2c.endpoints.latency.builtIn.events|Meddelandet svarstid för inbyggd slutpunkt (meddelanden/händelser)|Millisekunder|Genomsnitt|Genomsnittlig svarstid mellan meddelandet telemetriingång till IoT-hubb och meddelandet ingång i inbyggd slutpunkt (meddelanden/händelser), i millisekunder |
|d2c.Twin.Read.Success|Lyckad dubbla läser från enheter|Antal|Totalt|Antal alla lyckade enhet initieras dubbla läser.|
|d2c.Twin.Read.failure|Det gick inte dubbla läsningar av enheter|Antal|Totalt|Antalet alla misslyckades enhet initieras dubbla läsningar.|
|d2c.Twin.Read.size|Svarsstorlek dubbla läsningar av enheter|Byte|Genomsnitt|Medel, min och max för alla lyckade enhet initieras dubbla läsningar.|
|d2c.Twin.Update.Success|Lyckad dubbla uppdateringar från enheter|Antal|Totalt|Antal uppdateringar för alla lyckade enhet initieras dubbla.|
|d2c.Twin.Update.failure|Det gick inte dubbla uppdateringar från enheter|Antal|Totalt|Antalet alla misslyckades enhet initieras dubbla uppdateringar.|
|d2c.Twin.Update.size|Storleken på dubbla uppdateringar från enheter|Byte|Genomsnitt|Medel, min och max storlek för alla lyckade enhet initieras dubbla uppdateringar.|
|c2d.methods.Success|Lyckad direkta metoden anrop|Antal|Totalt|Antal alla lyckade direkt metodanrop.|
|c2d.methods.failure|Det gick inte direkt metod anrop|Antal|Totalt|Det gick inte att direkt metodanrop antalet alla.|
|c2d.methods.requestSize|Storleken på direkta metoden anrop för begäran|Byte|Genomsnitt|Medel, min och max för alla lyckade direkta metodbegäranden.|
|c2d.methods.responseSize|Storlek för direkta metoden anrop|Byte|Genomsnitt|Medel, min och max för alla lyckade direkta metoden svar.|
|c2d.Twin.Read.Success|Lyckad dubbla läser från serverdelen|Antal|Totalt|Antal alla lyckade tillbaka-end-initierad dubbla läser.|
|c2d.Twin.Read.failure|Misslyckade dubbla läser från serverdelen|Antal|Totalt|Antalet alla misslyckades tillbaka-end-initierad dubbla läsningar.|
|c2d.Twin.Read.size|Storlek för dubbla läser från serverdelen|Byte|Genomsnitt|Medel, min och max för alla lyckade tillbaka-end-initierade dubbla läsningar.|
|c2d.Twin.Update.Success|Lyckad dubbla uppdateringar från serverdelen|Antal|Totalt|Antal uppdateringar för alla lyckade tillbaka-end-initierad dubbla.|
|c2d.Twin.Update.failure|Misslyckade dubbla uppdateringar från serverdelen|Antal|Totalt|Antalet alla misslyckades tillbaka-end-initierad dubbla uppdateringar.|
|c2d.Twin.Update.size|Storleken på dubbla uppdateringar från serverdelen|Byte|Genomsnitt|Medel, min och max storlek för alla lyckade tillbaka-end-initierad dubbla uppdateringar.|
|twinQueries.success|Lyckad dubbla frågor|Antal|Totalt|Antal alla lyckade dubbla frågor.|
|twinQueries.failure|Misslyckade dubbla frågor|Antal|Totalt|Antal alla misslyckade dubbla frågor.|
|twinQueries.resultSize|Dubbla frågor storlek|Byte|Genomsnitt|Medel, min och max resultatet av alla lyckade dubbla frågor storlek.|
|jobs.createTwinUpdateJob.success|Lyckad skapande av dubbla uppdatera jobb|Antal|Totalt|Antal alla lyckade skapandet av dubbla uppdateringsjobb.|
|jobs.createTwinUpdateJob.failure|Misslyckade skapande av dubbla uppdatera jobb|Antal|Totalt|Antal alla misslyckad generering av dubbla uppdateringsjobb.|
|jobs.createDirectMethodJob.success|Lyckad skapande av metoden anrops-jobb|Antal|Totalt|Antal alla har skapats på direkta metoden anrop jobb.|
|jobs.createDirectMethodJob.failure|Misslyckade skapande av metoden anrops-jobb|Antal|Totalt|Antal alla misslyckad generering av direkta metoden anrop jobb.|
|jobs.listJobs.success|Antal samtal till listan jobb|Antal|Totalt|Antal alla lyckade anrop till listan jobb.|
|jobs.listJobs.failure|Det gick inte anrop till listan jobb|Antal|Totalt|Antal alla misslyckade anrop till listan jobb.|
|jobs.cancelJob.success|Lyckad jobbavbrott|Antal|Totalt|Antal lyckade anrop avbryta ett jobb.|
|jobs.cancelJob.failure|Avbryta misslyckade jobb|Antal|Totalt|Antal misslyckade anrop avbryta ett jobb.|
|jobs.queryJobs.success|Jobbfrågor|Antal|Totalt|Antal alla lyckade anrop till frågan jobb.|
|jobs.queryJobs.failure|Misslyckade jobbfrågor|Antal|Totalt|Antal alla misslyckade anrop till frågan jobb.|
|jobs.Completed|Slutförda jobb|Antal|Totalt|Antal alla slutförda jobb.|
|jobs.Failed|Misslyckade jobb|Antal|Totalt|Antal alla misslyckade jobb.|

## <a name="next-steps"></a>Nästa steg
Nu när du har sett en översikt över IoT-hubb mått på länken om du vill veta mer om hur du hanterar Azure IoT-hubb:

* [Åtgärder som övervakning][lnk-monitor]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
