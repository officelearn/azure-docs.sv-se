---
title: Använd mått för att övervaka Azure IoT Hub | Microsoft-dokument
description: Så här använder du Azure IoT Hub-mått för att bedöma och övervaka den övergripande hälsan för dina IoT-hubbar.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284439"
---
# <a name="understand-iot-hub-metrics"></a>Förstå IoT Hub-mått

IoT Hub-mått ger dig bättre data om tillståndet för Azure IoT-resurser i din Azure-prenumeration. Med IoT Hub-mått kan du bedöma den allmänna hälsan för IoT Hub-tjänsten och de enheter som är anslutna till den. Användarinriktad statistik är viktig eftersom de hjälper dig att se vad som händer med din IoT-hubb och hjälpa problem med root-cause utan att behöva kontakta Azure-supporten.

Mått är aktiverade som standard. Du kan visa IoT Hub-mått från Azure-portalen.

> [!NOTE]
> Du kan använda IoT Hub-mått för att visa information om IoT Plug and Play-enheter som är anslutna till din IoT Hub. IoT Plug and Play-enheter är en del av [ioT Plug and Play offentliga förhandsvisningen](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Så här visar du IoT Hub-mått

1. Skapa en IoT-hubb. Du kan hitta instruktioner om hur du skapar en IoT-hubb i [guiden Skicka telemetri från en enhet till IoT](quickstart-send-telemetry-dotnet.md) Hub-guiden.

2. Öppna bladet på din IoT-hubb. Därifrån klickar du på **Mått**.
   
    ![Skärmbild som visar var alternativet mått finns på resurssidan för IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. Från måttbladet kan du visa måtten för din IoT-hubb och skapa anpassade vyer av dina mått. 
   
    ![Skärmbild som visar statistiksidan för IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Du kan välja att skicka dina måttdata till en slutpunkt för händelsehubbar eller ett Azure Storage-konto genom att klicka på **Diagnostikinställningar**och sedan **lägga till diagnostikinställning**

   ![Skärmbild som visar var knappen för diagnostikinställningar finns](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub-mått och hur du använder dem

IoT Hub innehåller flera mått som ger dig en översikt över hälsotillståndet för ditt nav och det totala antalet anslutna enheter. Du kan kombinera information från flera mått för att måla en större bild av tillståndet för IoT-hubben. I följande tabell beskrivs måtten varje IoT-hubb spårar och hur varje mått relaterar till den övergripande statusen för IoT-hubben.

|Mått|Namn på mätdisplay|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Skicka försök till telemetri|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som har försökt skickas till din IoT-hubb|Inget|
|d2c.telemetry.ingress.success|Skickade telemetrimeddelanden|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som skickats till din IoT-hubb|Inget|
|c2d.commands.egress.complete.success|C2D-meddelandeleveranser har slutförts|Antal|Totalt|Antal meddelandeleveranser från molnet till enheten som har slutförts av enheten|Inget|
|c2d.commands.egress.abandon.success|C2D-meddelanden övergivna|Antal|Totalt|Antal meddelanden från molnet till enheten som övergivits av enheten|Inget|
|c2d.commands.egress.reject.success|C2D-meddelanden avvisade|Antal|Totalt|Antal meddelanden från molnet till enheten som avvisats av enheten|Inget|
|C2DMessagesExpirerad|C2D-meddelanden har upphört att gälla (förhandsgranskning)|Antal|Totalt|Antal meddelanden från molnet till enheten som har upphört att gälla|Inget|
|devices.totalDevices|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som är registrerade i din IoT-hubb|Inget|
|devices.connectedDevices.allProtocol|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inget|
|d2c.telemetry.egress.success|Routning: telemetrimeddelanden levererade|Antal|Totalt|Antalet gånger meddelanden har levererats till alla slutpunkter med hjälp av IoT Hub-routning. Om ett meddelande dirigeras till flera slutpunkter ökar det här värdet med en för varje lyckad leverans. Om ett meddelande levereras till samma slutpunkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inget|
|d2c.telemetry.egress.dropped|Routning: telemetrimeddelanden har tagits bort |Antal|Totalt|Antalet gånger meddelanden har tappats av IoT Hub routning på grund av döda slutpunkter. Det här värdet räknar inte meddelanden som levereras till återställningsvägen eftersom ignorerade meddelanden inte levereras där.|Inget|
|d2c.telemetry.egress.orphaned d2c.telemetry.egress.orphaned d2c.telemetry.egress.orphaned d2|Routning: överblivna telemetrimeddelanden |Antal|Totalt|Antalet gånger meddelanden övergavs av IoT Hub-routning eftersom de inte matchade några routningsregler (inklusive reservregeln). |Inget|
|d2c.telemetry.egress.invalid|Routning: telemetrimeddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub-routning misslyckades med att leverera meddelanden på grund av en inkompatibilitet med slutpunkten. Det här värdet inkluderar inte återförsök.|Inget|
|d2c.telemetri.egress.reserv|Routning: meddelanden som levereras till reserv|Antal|Totalt|Antalet gånger IoT Hub-routning levererade meddelanden till slutpunkten som är associerad med återgångsrutten.|Inget|
|d2c.endpoints.egress.eventHubs|Routning: meddelanden som levereras till eventhubben|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till slutpunkter för Event Hub.|Inget|
|d2c.endpoints.latens.eventHubs|Routning: meddelandesvarstid för händelsehubben|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och meddelandeinträngning till en event hub-slutpunkt.|Inget|
|d2c.endpoints.egress.serviceBusQueues|Routning: meddelanden som levereras till servicebusskö|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till servicebussköslutpunkter.|Inget|
|d2c.endpoints.latency.serviceBusQueues|Routning: meddelandesvarstid för servicebusskö|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelandet inträder i en tjänstbussköslutpunkt.|Inget|
|d2c.endpoints.egress.serviceBusTopics|Routning: meddelanden som levereras till Service Bus-ämne|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till servicebussämneslutpunkter.|Inget|
|d2c.endpoints.latens.serviceBusTopics|Routning: meddelandefördröjning för servicebussavsnitt|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelande ingående till en Service Bus-ämneslutpunkt.|Inget|
|d2c.endpoints.egress.builtIn.events|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till den inbyggda slutpunkten (meddelanden/händelser).|Inget|
|d2c.endpoints.latens.builtIn.events|Routning: meddelandefördröjning för meddelanden/händelser|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelande ingående till den inbyggda slutpunkten (meddelanden/händelser).|Inget|
|d2c.endpoints.egress.storage|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub-routningen har levererat meddelanden till lagringsslutpunkter.|Inget|
|d2c.endpoints.latens.lagring|Routning: meddelandefördröjning för lagring|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeinträngning till IoT Hub och telemetrimeddelande inträngning i en lagringsslutpunkt.|Inget|
|d2c.endpoints.egress.storage.bytes|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub-routning som levereras till lagringsslutpunkter.|Inget|
|d2c.endpoints.egress.storage.blobbar|Routning: blobbar som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub-routning levererade blobbar till lagringsslutpunkter.|Inget|
|EventGridDeliveries|Leveranser av eventrutnät(förhandsgranskning)|Antal|Totalt|Antalet IoT Hub-händelser som publicerats i Event Grid. Använd dimensionen Resultat för antalet lyckade och misslyckade begäranden. EventType-dimensionen visar typenhttps://aka.ms/ioteventgrid)av händelse ( .|ResourceId,<br/>Resultatet<br/>Eventtype|
|EventGridLatency|Svarstid för händelserutnät (förhandsgranskning)|Millisekunder|Medel|Den genomsnittliga svarstiden (millisekunder) från när Iot Hub-händelsen genererades till när händelsen publicerades i Event Grid. Det här talet är ett genomsnitt mellan alla händelsetyper. Använd dimensionen EventType om du vill se svarstid för en viss typ av händelse.|ResourceId,<br/>Eventtype|
|d2c.twin.read.success|Lyckade tvillingläsningar från enheter|Antal|Totalt|Antalet lyckade enhetsinitierade tvillingläsningar.|Inget|
|d2c.twin.read.failure|Misslyckade tvillingavläsningar från enheter|Antal|Totalt|Antalet misslyckade enhetsinitierade tvillingläsningar.|Inget|
|d2c.twin.read.size|Svarsstorleken för dubbla läsningar från enheter|Byte|Medel|Medelvärdet, min och max för alla framgångsrika enhetsinitierade tvillingläsningar.|Inget|
|d2c.twin.update.success|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade enhetsinitierade tvillinguppdateringar.|Inget|
|d2c.twin.update.failure|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade enhetsinitierade tvillinguppdateringar.|Inget|
|d2c.twin.update.size|Storlek på dubbla uppdateringar från enheter|Byte|Medel|Medelvärdet, min och maxstorleken för alla lyckade enhetsinitierade tvillinguppdateringar.|Inget|
|c2d.methods.success|Lyckade direkta metodaropationer|Antal|Totalt|Antalet alla lyckade direktmetodanrop.|Inget|
|c2d.methods.failure|Misslyckade direkta metodaropcations|Antal|Totalt|Antalet misslyckade direktmetodanrop.|Inget|
|c2d.methods.requestSize|Begär storlek på direkta metodaropcations|Byte|Medel|Medelvärdet, min och max för alla lyckade direktmetodbegäranden.|Inget|
|c2d.methods.responseSize|Svarsstorlek för direkta metodutkallelser|Byte|Medel|Medelvärdet, min och max för alla framgångsrika direktmetodsvar.|Inget|
|c2d.twin.read.success|Lyckad tvilling läser från slutet|Antal|Totalt|Antalet framgångsrika back-end-initierade tvillingläsningar.|Inget|
|c2d.twin.read.failure|Misslyckade tvillingläsningar från baksidan|Antal|Totalt|Antalet misslyckade backend-initierade tvillingläsningar.|Inget|
|c2d.twin.read.size|Svarsstorleken på tvillingavläsningar från den bakre änden|Byte|Medel|Medelvärdet, min och max för alla framgångsrika back-end-initierade tvillingläsningar.|Inget|
|c2d.twin.update.success|Lyckade dubbla uppdateringar från slutet|Antal|Totalt|Antalet lyckade backend-initierade tvillinguppdateringar.|Inget|
|c2d.twin.update.failure|Misslyckade dubbla uppdateringar från backend|Antal|Totalt|Antalet misslyckade backend-initierade tvillinguppdateringar.|Inget|
|c2d.twin.update.size|Storleken på dubbla uppdateringar från baksidan|Byte|Medel|Medelvärdet, min och maxstorleken för alla lyckade back-end-initierade tvillinguppdateringar.|Inget|
|twinQueries.success twinQueries.success twinQueries.success twinQue|Lyckade dubbla frågor|Antal|Totalt|Antalet lyckade tvillingfrågor.|Inget|
|twinQueries.failure|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade tvillingfrågor.|Inget|
|twinQueries.resultSize|Dubbla frågor resultatstorlek|Byte|Medel|Medelvärdet, min och max för resultatstorleken för alla lyckade tvillingfrågor.|Inget|
|jobs.createTwinUpdateJob.success|Framgångsrika skapande av dubbla uppdateringsjobb|Antal|Totalt|Antalet framgångsrika skapande av dubbla uppdateringsjobb.|Inget|
|jobs.createTwinUpdateJob.failure|Misslyckade skapande av dubbla uppdateringsjobb|Antal|Totalt|Antalet misslyckade skapande av dubbla uppdateringsjobb.|Inget|
|jobs.createDirectMethodJob.success|Framgångsrika skapande av metod anrop jobb|Antal|Totalt|Antalet framgångsrika skapande av direkta metoder åkarop jobb.|Inget|
|jobs.createDirectMethodJob.failure|Misslyckade skapande av metod-anropsjobb|Antal|Totalt|Antalet misslyckade skapande av direkta metod-anropsjobb.|Inget|
|jobs.listJobs.success|Lyckade anrop till listjobb|Antal|Totalt|Antalet lyckade anrop till listjobb.|Inget|
|jobs.listJobs.failure|Misslyckade anrop till listjobb|Antal|Totalt|Antalet misslyckade anrop till listjobb.|Inget|
|jobs.cancelJob.success|Lyckade jobbavbokningar|Antal|Totalt|Antalet lyckade samtal för att avbryta ett jobb.|Inget|
|jobs.cancelJob.failure|Misslyckade avbokningar av jobb|Antal|Totalt|Antalet misslyckade samtal för att avbryta ett jobb.|Inget|
|jobs.queryJobs.success|Lyckade jobbfrågor|Antal|Totalt|Antalet lyckade anrop till frågejobb.|Inget|
|jobs.queryJobs.failure|Misslyckade jobbfrågor|Antal|Totalt|Antalet misslyckade anrop till frågejobb.|Inget|
|jobb.slutförda|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inget|
|jobs.failed|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inget|
|d2c.telemetry.ingress.sendThrottle|Antal begränsningsfel|Antal|Totalt|Antal begränsningsfel på grund av enhetsdataflödesbegränsningar|Inget|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Antal|Medel|Antal totalt antal meddelanden som används idag. Detta är ett ackumulerat värde som återställs till noll vid 00:00 UTC varje dag.|Inget|
|deviceDataUsage|Total användning av enhetsdata|Byte|Totalt|Byte som överförts till och från alla enheter som är anslutna till IotHub|Inget|
|deviceDataUsageV2|Total enhetsdataanvändning (förhandsgranskning)|Byte|Totalt|Byte som överförts till och från alla enheter som är anslutna till IotHub|Inget|
|totaltDeviceCount|Totalt antal enheter (förhandsgranskning)|Antal|Medel|Antal enheter som är registrerade i din IoT-hubb|Inget|
|connectedDeviceCount|Anslutna enheter (förhandsgranskning)|Antal|Medel|Antal enheter som är anslutna till din IoT-hubb|Inget|
|Konfigurationer|Konfigurationsmått|Antal|Totalt|Mått för konfigurationsåtgärder|Inget|

## <a name="next-steps"></a>Nästa steg

Nu när du har sett en översikt över IoT Hub-mått följer du den här länken om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Konfigurera diagnostikloggar](iot-hub-monitor-resource-health.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
