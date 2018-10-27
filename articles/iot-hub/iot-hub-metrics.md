---
title: Använda mått för att övervaka Azure IoT Hub | Microsoft Docs
description: Hur du använder Azure IoT Hub mått för att utvärdera och övervaka den övergripande hälsan för din IoT-hubbar.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: f5da3759e97eaa2f89a393d55ae95eb6623fbc5d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158346"
---
# <a name="understand-iot-hub-metrics"></a>Förstå IoT Hub-mått

IoT Hub mått ger dig bättre information om tillstånd för Azure IoT-resurser i Azure-prenumerationen. IoT Hub mått kan du utvärdera den övergripande hälsan för IoT Hub-tjänsten och de enheter som är anslutna till den. Användarinriktade statistik är viktigt eftersom de hjälper dig se vad som händer med din IoT hub hjälp grundorsaken problem med och utan att behöva kontakta Azure-supporten.

Mått är aktiverade som standard. Du kan visa mått för IoT Hub från Azure-portalen.

## <a name="how-to-view-iot-hub-metrics"></a>Visa mått för IoT Hub

1. Skapa en IoT-hubb. Du hittar anvisningar om hur du skapar en IoT hub i den [skickar telemetri från en enhet till IoT Hub](quickstart-send-telemetry-dotnet.md) guide.

2. Öppna bladet för din IoT hub. Därifrån klickar du på **mått**.
   
    ![Måttbladet för IoT-hubb](./media/iot-hub-metrics/enable-metrics-1.png)

3. Du kan visa värdena för din IoT hub och skapa anpassade vyer mått från måttbladet. Du kan välja att skicka dina måttdata till en slutpunkt för Event Hubs eller ett Azure Storage-konto genom att klicka på **diagnostikinställningar**.
   
    ![Diagnostikinställningar](./media/iot-hub-metrics/enable-metrics-2.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub-mått och hur du använder dem.

IoT Hub innehåller flera mått för att ge dig en översikt över hälsotillståndet för din hubb och det totala antalet anslutna enheter. Du kan kombinera information från flera mått för att skapa en större översiktsbild av tillståndet för IoT-hubben. I följande tabell beskrivs de mått som spårar varje IoT-hubb och hur varje mått relaterar till det övergripande tillståndet för IoT-hubben.

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c<br>.telemetry<br>.Ingress.<br>allProtocol|Telemetri message send försök|Antal|Totalt|Antal meddelanden för enhet till moln-telemetri försökte skickas till din IoT-hubb|Inga dimensioner|
|d2c<br>.telemetry<br>.Ingress<br>.Success|Telemetrimeddelanden som skickas|Antal|Totalt|Antal telemetrimeddelanden från enheten till molnet har skickats till din IoT hub|Inga dimensioner|
|c2d<br>.commands<br>.egress<br>.komplett<br>.Success|Kommandon har slutförts|Antal|Totalt|Antal moln till enhet kommandon har slutförts av enheten|Inga dimensioner|
|c2d<br>.commands<br>.egress<br>.Abandon<br>.Success|Kommandon övergivna|Antal|Totalt|Antal moln till enhet kommandon som avbrutits av enheten|Inga dimensioner|
|c2d<br>.commands<br>.egress<br>.Reject<br>.Success|Kommandon som avvisats|Antal|Totalt|Antal moln till enhet kommandon som avvisats av enheten|Inga dimensioner|
|enheter<br>.totalDevices|Totalt antal enheter (inaktuell)|Antal|Totalt|Antalet enheter som registrerats till din IoT hub|Inga dimensioner|
|enheter<br>.connectedDevices<br>.allProtocol|Anslutna enheter (inaktuell) |Antal|Totalt|Antalet enheter som är anslutna till din IoT hub|Inga dimensioner|
|d2c<br>.telemetry<br>.egress<br>.Success|Routning: telemetrimeddelanden som levereras|Antal|Totalt|Antal gånger som meddelanden levereras till alla slutpunkter med hjälp av IoT Hub routning. Om ett meddelande skickas till flera slutpunkter, ökar detta värde med ett för varje lyckad leverans. Om ett meddelande skickas till samma slutpunkt flera gånger, ökar detta värde med ett för varje lyckad leverans.|Inga dimensioner|
|d2c<br>.telemetry<br>.egress<br>.dropped|Routning: telemetrimeddelanden som tas bort |Antal|Totalt|Antal gånger som meddelanden har tagits bort av IoT Hub routning på grund av döda slutpunkter. Det här värdet räknas inte meddelanden som levereras till återställningsplats väg som utelämnade meddelanden inte levereras det.|Inga dimensioner|
|d2c<br>.telemetry<br>.egress<br>.orphaned|Routning: telemetrimeddelanden som överblivna |Antal|Totalt|Antal gånger som meddelanden frånkopplade genom IoT Hub routning eftersom de inte matchar några regler för routning (inklusive återställningsplats regeln). |Inga dimensioner|
|d2c<br>.telemetry<br>.egress<br>.Invalid|Routning: telemetrimeddelanden inkompatibla|Antal|Totalt|Antal gånger som IoT Hub routning inte kunde skicka meddelanden på grund av inkompatibilitet med slutpunkten. Det här värdet inkluderar inte återförsök.|Inga dimensioner|
|d2c<br>.telemetry<br>.egress<br>.fallback|Routning: skicka meddelanden till återställningsplats|Antal|Totalt|Antal gånger som IoT Hub routning levererade meddelanden till slutpunkten som är associerade med återställningsplats vägen.|Inga dimensioner|
|d2c<br>.endpoints<br>.egress<br>.eventHubs|Routning: skicka meddelanden till Event Hub|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|d2c<br>.endpoints<br>.latency<br>.eventHubs|Routning: meddelande svarstiden för Event Hub|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och meddelandet inkommande till en Event Hub-slutpunkt.|Inga dimensioner|
|d2c<br>.endpoints<br>.egress<br>.serviceBusQueues|Routning: skicka meddelanden till Service Bus-kö|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Service Bus-kö-slutpunkter.|Inga dimensioner|
|d2c<br>.endpoints<br>.latency<br>.serviceBusQueues|Routning: meddelande svarstid för Service Bus-kö|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande i en slutpunkt för Service Bus-kö.|Inga dimensioner|
|d2c<br>.endpoints<br>.egress<br>.serviceBusTopics|Routning: skicka meddelanden till Service Bus-ämne|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till Service Bus-ämnet slutpunkter.|Inga dimensioner|
|d2c<br>.endpoints<br>.latency<br>.serviceBusTopics|Routning: meddelande svarstid för Service Bus-ämne|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande i en slutpunkt för Service Bus-ämne.|Inga dimensioner|
|d2c<br>.endpoints<br>.egress<br>.builtIn<br>.events|Routning: skicka meddelanden till meddelanden/händelser|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till den inbyggda slutpunkten (meddelanden/händelser).|Inga dimensioner|
|d2c<br>.endpoints<br>.latency<br>. builtIn.events|Routning: meddelande svarstiden för meddelanden/händelser|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande ingående i den inbyggda slutpunkten (meddelanden/händelser).|Inga dimensioner|
|d2c<br>.endpoints<br>.egress<br>.Storage|Routning: skicka meddelanden till storage|Antal|Totalt|Antal gånger som IoT Hub routning har levererade meddelanden till storage-slutpunkter.|Inga dimensioner|
|d2c<br>.endpoints<br>.latency<br>.Storage|Routning: meddelande svarstid för lagring|Millisekunder|Medel|Genomsnittlig svarstid (millisekunder) mellan inkommande meddelande till IoT Hub och telemetri meddelande inkommande till en slutpunkt för lagring.|Inga dimensioner|
|d2c<br>.endpoints<br>.egress<br>.Storage<br>.bytes|Routning: data som levereras till storage|Byte|Totalt|Hur mycket data (byte) IoT Hub routning levereras till storage-slutpunkter.|Inga dimensioner|
|d2c<br>.endpoints<br>.egress<br>.Storage<br>.BLOBs|Routning: blobar som levereras till storage|Antal|Totalt|Antal gånger som IoT Hub routning levereras BLOB storage-slutpunkter.|Inga dimensioner|
|d2c<br>.Twin<br>.Read<br>.Success|Lyckad twin läser från enheter|Antal|Totalt|Antal för alla lyckade enhet-initierad tvilling läser.|Inga dimensioner|
|d2c<br>.Twin<br>.Read<br>.failure|Det gick inte twin läsningar från enheter|Antal|Totalt|Det gick inte att enheten-initierad twin läsningar uppräkning av alla.|Inga dimensioner|
|d2c<br>.Twin<br>.Read<br>.size|Svarsstorlek för tvilling läser från enheter|Byte|Medel|Genomsnitt, min och max för alla lyckade enhet-initierad twin läsningar.|Inga dimensioner|
|d2c<br>.Twin<br>.Update<br>.Success|Lyckad twin uppdateringar från enheter|Antal|Totalt|Antal uppdateringar för alla lyckade enhet-initierad enhetstvilling.|Inga dimensioner|
|d2c<br>.Twin<br>.Update<br>.failure|Det gick inte uppdateringar för enhetstvilling från enheter|Antal|Totalt|Det gick inte att uppdateringar för enheten-initierad enhetstvilling uppräkning av alla.|Inga dimensioner|
|d2c<br>.Twin<br>.Update<br>.size|Storleken på uppdateringar för enhetstvilling från enheter|Byte|Medel|Genomsnitt, min och maxstorleken på alla lyckade enhet-initierad twin uppdateringar.|Inga dimensioner|
|c2d<br>.Methods<br>.Success|Lyckad direkt metod anrop|Antal|Totalt|Antal anrop för alla lyckade direkt metod.|Inga dimensioner|
|c2d<br>.Methods<br>.failure|Det gick inte direkt metod anrop|Antal|Totalt|Uppräkning av alla misslyckade anropen för direkt metod.|Inga dimensioner|
|c2d<br>.Methods<br>.requestSize|Storlek på direkt metod anrop|Byte|Medel|Genomsnitt, min och max för alla lyckade begäranden för direkt metod.|Inga dimensioner|
|c2d<br>.Methods<br>.responseSize|Svarsstorlek för direkt metod anrop|Byte|Medel|Genomsnitt, min och max för alla direkt metod lyckades.|Inga dimensioner|
|c2d<br>.Twin<br>.Read<br>.Success|Lyckad twin läser från backend-server|Antal|Totalt|Antal för alla lyckade tillbaka-end-initierad tvilling läser.|Inga dimensioner|
|c2d<br>.Twin<br>.Read<br>.failure|Misslyckade twin läser från backend-server|Antal|Totalt|Det gick inte att tillbaka-end-initierad twin läsningar uppräkning av alla.|Inga dimensioner|
|c2d<br>.Twin<br>.Read<br>.size|Svarsstorlek för tvilling läser från backend-server|Byte|Medel|Genomsnitt, min och max för alla lyckade tillbaka-end-initierad twin läsningar.|Inga dimensioner|
|c2d<br>.Twin<br>.Update<br>.Success|Lyckad twin uppdateringar från backend-server|Antal|Totalt|Antal uppdateringar för alla lyckade tillbaka-end-initierad enhetstvilling.|Inga dimensioner|
|c2d<br>.Twin<br>.Update<br>.failure|Misslyckade twin uppdateringar från backend-server|Antal|Totalt|Uppräkning av alla misslyckades tillbaka-end-initierad twin uppdateringar.|Inga dimensioner|
|c2d<br>.Twin<br>.Update<br>.size|Storleken på twin uppdateringar från backend-server|Byte|Medel|Genomsnitt, min och maxstorleken på alla lyckade tillbaka-end-initierad twin uppdateringar.|Inga dimensioner|
|TwinQueries<br>.Success|Lyckad twin frågor|Antal|Totalt|Totalt antal alla lyckade twin frågor.|Inga dimensioner|
|TwinQueries<br>.failure|Misslyckade twin frågor|Antal|Totalt|Totalt antal alla misslyckade twin frågor.|Inga dimensioner|
|TwinQueries<br>.resultSize|Twin frågor storlek|Byte|Medel|Genomsnitt, min och max för resultatet storleken på alla lyckade twin frågor.|Inga dimensioner|
|jobb<br>.createTwinUpdateJob<br>.Success|Lyckad som skapats för tvilling uppdatera jobb|Antal|Totalt|Totalt antal alla har skapats på twin uppdateringsjobb.|Inga dimensioner|
|jobb<br>.createTwinUpdateJob<br>.failure|Misslyckade som skapats för tvilling uppdatera jobb|Antal|Totalt|Uppräkning av alla misslyckad generering av twin uppdatera jobb.|Inga dimensioner|
|jobb<br>.createDirectMethodJob<br>.Success|Lyckad skapande av metoden anrops-jobb|Antal|Totalt|Totalt antal alla har skapats på direkt metod anrop jobb.|Inga dimensioner|
|jobb<br>.createDirectMethodJob<br>.failure|Misslyckade skapande av metoden anrops-jobb|Antal|Totalt|Totalt antal alla misslyckad generering av direkt metod anrop jobb.|Inga dimensioner|
|jobb<br>.listJobs<br>.Success|Utförda anrop lista jobb|Antal|Totalt|Antal lyckade anrop lista jobb.|Inga dimensioner|
|jobb<br>.listJobs<br>.failure|De misslyckade anropen lista jobb|Antal|Totalt|Antal misslyckade anrop lista jobb.|Inga dimensioner|
|jobb<br>.cancelJob<br>.Success|Avbryta lyckade jobb|Antal|Totalt|Antal lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobb<br>.cancelJob<br>.failure|Avbryta misslyckade jobb|Antal|Totalt|Antal misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobb<br>.queryJobs<br>.Success|Lyckad jobbfrågor|Antal|Totalt|Antal lyckade anrop till fråga jobb.|Inga dimensioner|
|jobb<br>.queryJobs<br>.failure|Misslyckade jobbfrågor|Antal|Totalt|Antal misslyckade anrop till fråga jobb.|Inga dimensioner|
|jobb<br>.Completed|Slutförda jobb|Antal|Totalt|Totalt antal alla slutförda jobb.|Inga dimensioner|
|jobb<br>.Failed|Misslyckade jobb|Antal|Totalt|Totalt antal alla misslyckade jobb.|Inga dimensioner|
|d2c<br>.telemetry<br>.Ingress<br>.sendThrottle|Antalet begränsningsfel|Antal|Totalt|Begränsar antalet begränsningsfel på grund av enheten dataflöde|Inga dimensioner|
|dailyMessage<br>QuotaUsed|Sammanlagt antal meddelanden som används|Antal|Medel|Antal Totalt antal meddelanden som används idag. Detta är en ackumulerad värde som nollställs på 00:00 UTC varje dag.|Inga dimensioner|
|deviceDataUsage|Enhetens totala dataanvändning (inaktuell)|Byte|Totalt|Byte som överfördes till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|deviceDataUsageV2|Dataanvändning för totalt antal enheter (förhandsversion)|Byte|Totalt|Byte som överfördes till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|totalDeviceCount|Totalt antal enheter (förhandsversion)|Antal|Medel|Antalet enheter som registrerats till din IoT hub|Inga dimensioner|
|Ansluten<br>DeviceCount|Anslutna enheter (förhandsversion)|Antal|Medel|Antalet enheter som är anslutna till din IoT hub|Inga dimensioner|
|Konfigurationer|Konfiguration av mått|Antal|Totalt|Mått för konfigurationsåtgärder|Inga dimensioner|

## <a name="next-steps"></a>Nästa steg

Nu när du har fått en översikt över IoT Hub-mått, följ den här länken om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
