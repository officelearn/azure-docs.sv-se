---
title: Använda mått för att övervaka Azure IoT Hub | Microsoft Docs
description: Hur du använder Azure IoT Hub mått för att utvärdera och övervaka den övergripande hälsan för din IoT-hubbar.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: b41458f0201c46b99c09d0bfffd219743a36ad50
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186763"
---
# <a name="understand-iot-hub-metrics"></a>Förstå IoT Hub-mått
IoT Hub mått ger dig bättre information om tillstånd för Azure IoT-resurser i Azure-prenumerationen. IoT Hub mått kan du utvärdera den övergripande hälsan för IoT Hub-tjänsten och de enheter som är anslutna till den. Användarinriktade statistik är viktigt eftersom de hjälper dig se vad som händer med din IoT hub hjälp grundorsaken problem med och utan att behöva kontakta Azure-supporten.

Mått är aktiverade som standard. Du kan visa mått för IoT Hub från Azure-portalen.

## <a name="how-to-view-iot-hub-metrics"></a>Visa mått för IoT Hub
1. Skapa en IoT-hubb. Du hittar anvisningar om hur du skapar en IoT hub i den [börjar] [ lnk-get-started] guide.
2. Öppna bladet för din IoT hub. Därifrån klickar du på **mått**.
   
    ![][1]
3. Du kan visa värdena för din IoT hub och skapa anpassade vyer mått från måttbladet. Du kan välja att skicka dina måttdata till en slutpunkt för Event Hubs eller ett Azure Storage-konto genom att klicka på **diagnostikinställningar**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub-mått och hur du använder dem.
IoT Hub innehåller flera mått för att ge dig en översikt över hälsotillståndet för din hubb och det totala antalet anslutna enheter. Du kan kombinera information från flera mått för att skapa en större översiktsbild av tillståndet för IoT-hubben. I följande tabell beskrivs de mått som spårar varje IoT-hubb och hur varje mått relaterar till det övergripande tillståndet för IoT-hubben.

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp|Beskrivning|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetri message send försök|Antal|Totalt|Antal meddelanden för enhet till moln-telemetri försökte skickas till din IoT-hubb|
|d2c.telemetry.Ingress.Success|Telemetrimeddelanden som skickas|Antal|Totalt|Antal telemetrimeddelanden från enheten till molnet har skickats till din IoT hub|
|c2d.commands.egress.Complete.Success|Kommandon har slutförts|Antal|Totalt|Antal moln till enhet kommandon har slutförts av enheten|
|c2d.commands.egress.abandon.success|Kommandon övergivna|Antal|Totalt|Antal moln till enhet kommandon som avbrutits av enheten|
|c2d.commands.egress.Reject.Success|Kommandon som avvisats|Antal|Totalt|Antal moln till enhet kommandon som avvisats av enheten|
|devices.totalDevices|Totalt antal enheter|Antal|Totalt|Antalet enheter som registrerats till din IoT hub|
|devices.connectedDevices.allProtocol|Anslutna enheter|Antal|Totalt|Antalet enheter som är anslutna till din IoT hub|
|d2c.telemetry.egress.Success|Telemetrimeddelanden levereras|Antal|Totalt|Antal gånger som meddelanden har skrivits till slutpunkter (totalt)|
|d2c.telemetry.egress.dropped|Utelämnade meddelanden|Antal|Totalt|Antal meddelanden som tas bort eftersom de matchade inte några vägar och återställningsplats vägen har inaktiverats|
|d2c.telemetry.egress.orphaned|Överblivna meddelanden|Antal|Totalt|Antal meddelanden som inte matchar några vägar inklusive återställningsplats vägen|
|d2c.telemetry.egress.invalid|Ogiltig meddelanden|Antal|Totalt|Antal meddelanden som inte levereras på grund av inkompatibilitet med slutpunkten|
|d2c.telemetry.egress.fallback|Meddelanden som matchar återställningsplats villkor|Antal|Totalt|Antal meddelanden som skrivs till slutpunkten för återställningsplats|
|d2c.endpoints.egress.eventHubs|Skicka meddelanden till Event Hub-slutpunkter|Antal|Totalt|Antal gånger som meddelanden har skrivits till Event Hub-slutpunkter|
|d2c.endpoints.latency.eventHubs|Meddelandet svarstid för Event Hub-slutpunkter|Millisekunder|Medel|Genomsnittlig svarstid mellan inkommande meddelande till IoT hub och meddelandet inkommande till en Event Hub-slutpunkt, i millisekunder|
|d2c.endpoints.egress.serviceBusQueues|Skicka meddelanden till Service Bus-kö slutpunkter|Antal|Totalt|Antal gånger som meddelanden har skrivits till Service Bus-kö slutpunkter|
|d2c.endpoints.latency.serviceBusQueues|Meddelandet svarstid för Service Bus-kö slutpunkter|Millisekunder|Medel|Genomsnittlig svarstid mellan inkommande meddelande till IoT hub och inkommande meddelande i en Service Bus-kö-slutpunkt, i millisekunder|
|d2c.endpoints.egress.serviceBusTopics|Skicka meddelanden till Service Bus-ämne slutpunkter|Antal|Totalt|Antal gånger som meddelanden har skrivits till Service Bus-ämne slutpunkter|
|d2c.endpoints.latency.serviceBusTopics|Meddelandet svarstid för Service Bus-ämne slutpunkter|Millisekunder|Medel|Genomsnittlig svarstid mellan inkommande meddelande till IoT hub och inkommande meddelande i en Service Bus-ämne slutpunkt, i millisekunder|
|d2c.endpoints.egress.builtIn.events|Meddelanden som levereras till den inbyggda slutpunkten (meddelanden/händelser)|Antal|Totalt|Antal gånger som meddelanden har skrivits till den inbyggda slutpunkten (meddelanden/händelser)|
|d2c.endpoints.latency.builtIn.events|Meddelandet svarstiden för den inbyggda slutpunkten (meddelanden/händelser)|Millisekunder|Medel|Genomsnittlig svarstid mellan inkommande meddelande till IoT hub och meddelandet ingående i den inbyggda slutpunkten (meddelanden/händelser), i millisekunder |
|d2c.Twin.Read.Success|Lyckad twin läser från enheter|Antal|Totalt|Antal för alla lyckade enhet-initierad tvilling läser.|
|d2c.Twin.Read.failure|Det gick inte twin läsningar från enheter|Antal|Totalt|Det gick inte att enheten-initierad twin läsningar uppräkning av alla.|
|d2c.Twin.Read.size|Svarsstorlek för tvilling läser från enheter|Byte|Medel|Genomsnitt, min och max för alla lyckade enhet-initierad twin läsningar.|
|d2c.Twin.Update.Success|Lyckad twin uppdateringar från enheter|Antal|Totalt|Antal uppdateringar för alla lyckade enhet-initierad enhetstvilling.|
|d2c.Twin.Update.failure|Det gick inte uppdateringar för enhetstvilling från enheter|Antal|Totalt|Det gick inte att uppdateringar för enheten-initierad enhetstvilling uppräkning av alla.|
|d2c.Twin.Update.size|Storleken på uppdateringar för enhetstvilling från enheter|Byte|Medel|Genomsnitt, min och maxstorleken på alla lyckade enhet-initierad twin uppdateringar.|
|c2d.methods.Success|Lyckad direkt metod anrop|Antal|Totalt|Antal anrop för alla lyckade direkt metod.|
|c2d.methods.failure|Det gick inte direkt metod anrop|Antal|Totalt|Uppräkning av alla misslyckade anropen för direkt metod.|
|c2d.methods.requestSize|Storlek på direkt metod anrop|Byte|Medel|Genomsnitt, min och max för alla lyckade begäranden för direkt metod.|
|c2d.methods.responseSize|Svarsstorlek för direkt metod anrop|Byte|Medel|Genomsnitt, min och max för alla direkt metod lyckades.|
|c2d.Twin.Read.Success|Lyckad twin läser från backend-server|Antal|Totalt|Antal för alla lyckade tillbaka-end-initierad tvilling läser.|
|c2d.Twin.Read.failure|Misslyckade twin läser från backend-server|Antal|Totalt|Det gick inte att tillbaka-end-initierad twin läsningar uppräkning av alla.|
|c2d.Twin.Read.size|Svarsstorlek för tvilling läser från backend-server|Byte|Medel|Genomsnitt, min och max för alla lyckade tillbaka-end-initierad twin läsningar.|
|c2d.Twin.Update.Success|Lyckad twin uppdateringar från backend-server|Antal|Totalt|Antal uppdateringar för alla lyckade tillbaka-end-initierad enhetstvilling.|
|c2d.Twin.Update.failure|Misslyckade twin uppdateringar från backend-server|Antal|Totalt|Uppräkning av alla misslyckades tillbaka-end-initierad twin uppdateringar.|
|c2d.Twin.Update.size|Storleken på twin uppdateringar från backend-server|Byte|Medel|Genomsnitt, min och maxstorleken på alla lyckade tillbaka-end-initierad twin uppdateringar.|
|twinQueries.success|Lyckad twin frågor|Antal|Totalt|Totalt antal alla lyckade twin frågor.|
|twinQueries.failure|Misslyckade twin frågor|Antal|Totalt|Totalt antal alla misslyckade twin frågor.|
|twinQueries.resultSize|Twin frågor storlek|Byte|Medel|Genomsnitt, min och max för resultatet storleken på alla lyckade twin frågor.|
|jobs.createTwinUpdateJob.success|Lyckad som skapats för tvilling uppdatera jobb|Antal|Totalt|Totalt antal alla har skapats på twin uppdateringsjobb.|
|jobs.createTwinUpdateJob.failure|Misslyckade som skapats för tvilling uppdatera jobb|Antal|Totalt|Uppräkning av alla misslyckad generering av twin uppdatera jobb.|
|jobs.createDirectMethodJob.success|Lyckad skapande av metoden anrops-jobb|Antal|Totalt|Totalt antal alla har skapats på direkt metod anrop jobb.|
|jobs.createDirectMethodJob.failure|Misslyckade skapande av metoden anrops-jobb|Antal|Totalt|Totalt antal alla misslyckad generering av direkt metod anrop jobb.|
|jobs.listJobs.success|Utförda anrop lista jobb|Antal|Totalt|Antal lyckade anrop lista jobb.|
|jobs.listJobs.failure|De misslyckade anropen lista jobb|Antal|Totalt|Antal misslyckade anrop lista jobb.|
|jobs.cancelJob.success|Avbryta lyckade jobb|Antal|Totalt|Antal lyckade anrop för att avbryta ett jobb.|
|jobs.cancelJob.failure|Avbryta misslyckade jobb|Antal|Totalt|Antal misslyckade anrop för att avbryta ett jobb.|
|jobs.queryJobs.success|Lyckad jobbfrågor|Antal|Totalt|Antal lyckade anrop till fråga jobb.|
|jobs.queryJobs.failure|Misslyckade jobbfrågor|Antal|Totalt|Antal misslyckade anrop till fråga jobb.|
|jobs.Completed|Slutförda jobb|Antal|Totalt|Totalt antal alla slutförda jobb.|
|jobs.Failed|Misslyckade jobb|Antal|Totalt|Totalt antal alla misslyckade jobb.|

## <a name="next-steps"></a>Nästa steg
Nu när du har fått en översikt över IoT Hub-mått, följ den här länken om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Åtgärdsövervakning][lnk-monitor]

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub][lnk-devguide]
* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
