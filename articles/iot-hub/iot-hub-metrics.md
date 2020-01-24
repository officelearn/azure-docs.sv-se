---
title: Använd mått för att övervaka Azure-IoT Hub | Microsoft Docs
description: Använda Azure IoT Hub mått för att utvärdera och övervaka den övergripande hälsan hos dina IoT-hubbar.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705742"
---
# <a name="understand-iot-hub-metrics"></a>Förstå IoT Hub mått

IoT Hub mått ger bättre data om status för Azure IoT-resurser i din Azure-prenumeration. Med IoT Hub mått kan du utvärdera den övergripande hälsan för tjänsten IoT Hub och de enheter som är anslutna till den. Statistik som riktas mot användaren är viktig eftersom de hjälper dig att se vad som händer med din IoT-hubb och hjälp med rotor Saks problem utan att behöva kontakta Azure-supporten.

Mått är aktiverat som standard. Du kan visa IoT Hub mått från Azure Portal.

> [!NOTE]
> Du kan använda IoT Hub mått för att visa information om IoT Plug and Play-enheter som är anslutna till din IoT Hub. IoT Plug and Play-enheter ingår i [iot plug and Play offentlig för hands version](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Visa IoT Hub mått

1. Skapa en IoT-hubb. Du hittar instruktioner om hur du skapar en IoT Hub i [Skicka telemetri från en enhet till IoT Hub](quickstart-send-telemetry-dotnet.md) guide.

2. Öppna bladet med din IoT Hub. Därifrån klickar du på **mått**.
   
    ![Skärm bild som visar var mått alternativet finns på IoT Hub resurs sida](./media/iot-hub-metrics/enable-metrics-1.png)

3. Från mått bladet kan du visa måtten för din IoT-hubb och skapa anpassade vyer av dina mått. 
   
    ![Skärm bild som visar mått sidan för IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Du kan välja att skicka Mät data till en Event Hubs-slutpunkt eller ett Azure Storage konto genom att klicka på **diagnostikinställningar**och sedan **lägga till diagnostikinställningar**

   ![Skärm bild som visar var diagnostikinställningar-knappen är](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub mått och hur du använder dem

IoT Hub tillhandahåller flera mått för att ge dig en översikt över hälsan för hubben och det totala antalet anslutna enheter. Du kan kombinera information från flera mått för att måla en större bild av läget för IoT Hub. I följande tabell beskrivs måtten för varje IoT Hub-spår och hur varje mått relaterar till den övergripande statusen för IoT Hub.

|Mått|Metrisk visningsnamn|Enhet|Sammansättningstyp:|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inget|
|d2c.telemetry.ingress.success|Skickade telemetrimeddelanden|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inget|
|C2D. commands. utgående. Complete. lyckades|C2D meddelande leveranser har slutförts|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inget|
|c2d.commands.egress.abandon.success|Övergivna C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inget|
|c2d.commands.egress.reject.success|Avvisade C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inget|
|C2DMessagesExpired|C2D meddelanden har förfallit (förhands granskning)|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Inget|
|devices.totalDevices|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inget|
|devices.connectedDevices.allProtocol|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inget|
|d2c.telemetry.egress.success|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inget|
|d2c.telemetry.egress.dropped|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inget|
|d2c.telemetry.egress.orphaned|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inget|
|d2c.telemetry.egress.invalid|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inget|
|d2c.telemetry.egress.fallback|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inget|
|d2c.endpoints.egress.eventHubs|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inget|
|d2c.endpoints.latency.eventHubs|Routning: meddelandesvarstid för händelsehubb|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inget|
|d2c.endpoints.egress.serviceBusQueues|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inget|
|d2c.endpoints.latency.serviceBusQueues|Routning: meddelandesvarstid för Service Bus-kö|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inget|
|d2c.endpoints.egress.serviceBusTopics|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inget|
|d2c.endpoints.latency.serviceBusTopics|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inget|
|d2c.endpoints.egress.builtIn.events|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser).|Inget|
|d2c.endpoints.latency.builtIn.events|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser).|Inget|
|D2C. endpoints. utgående. Storage|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inget|
|d2c.endpoints.latency.storage|Routning: meddelande fördröjning för lagring|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inget|
|D2C. endpoints. utgående. Storage. byte|Routning: data som levererats till minne|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inget|
|d2c.endpoints.egress.storage.blobs|Routning: blobar som levererats till minne|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inget|
|EventGridDeliveries|Event Grid leveranser (för hands version)|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar typen av händelse (https://aka.ms/ioteventgrid).|ResourceID<br/>Medför<br/>Typ|
|EventGridLatency|Event Grid svars tid (för hands version)|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|ResourceID<br/>Typ|
|d2c.twin.read.success|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inget|
|d2c.twin.read.failure|Misslyckade tvillingläsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inget|
|d2c.twin.read.size|Svarsstorlek för tvillingläsningar från enheter|Byte|Medel|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inget|
|d2c.twin.update.success|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|d2c.twin.update.failure|Misslyckade tvillinguppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|d2c.twin.update.size|Storlek på dubbla uppdateringar från enheter|Byte|Medel|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|c2d.methods.success|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inget|
|C2D. Methods. Failure|Misslyckade direktmetodsanrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inget|
|C2D. Methods. requestSize|Begäransstorlek för direktmetodsanrop|Byte|Medel|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inget|
|C2D. Methods. responseSize|Svarsstorlek för direktmetodsanrop|Byte|Medel|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inget|
|c2d.twin.read.success|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inget|
|c2d.twin.read.failure|Misslyckade tvillingläsningar från serverdel|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inget|
|c2d.twin.read.size|Svarsstorlek för tvillingläsningar från serverdel|Byte|Medel|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inget|
|c2d.twin.update.success|Lyckade tvillinguppdateringar från serverdel|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inget|
|c2d.twin.update.failure|Misslyckade tvillinguppdateringar från serverdel|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inget|
|c2d.twin.update.size|Storlek på dubbla uppdateringar från Server delen|Byte|Medel|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inget|
|twinQueries. lyckades|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Inget|
|twinQueries. Failure|Misslyckade tvillingfrågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inget|
|twinQueries.resultSize|Resultat storlek för dubbla frågor|Byte|Medel|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inget|
|jobs.createTwinUpdateJob.success|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inget|
|jobs.createTwinUpdateJob.failure|Misslyckat skapande av tvillinguppdateringsjobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inget|
|jobs.createDirectMethodJob.success|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inget|
|jobs.createDirectMethodJob.failure|Misslyckat skapande av metodanropsjobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inget|
|jobs.listJobs.success|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inget|
|Jobs. listJobs. Failure|Misslyckade anrop för att lista jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inget|
|Jobs. cancelJob. lyckades|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inget|
|Jobs. cancelJob. Failure|Misslyckade jobbannulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inget|
|Jobs. queryJobs. lyckades|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inget|
|Jobs. queryJobs. Failure|Misslyckade jobbfrågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inget|
|jobb. slutfört|Slutförda arbeten|Antal|Totalt|Antalet slutförda jobb.|Inget|
|jobb. misslyckades|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inget|
|d2c.telemetry.ingress.sendThrottle|Antal begränsningsfel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inget|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Antal|Medel|Antal sammanlagt antal meddelanden som använts idag. Detta är ett ackumulerat värde som återställs till noll vid 00:00 UTC varje dag.|Inget|
|deviceDataUsage|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inget|
|deviceDataUsageV2|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inget|
|totalDeviceCount|Totalt antal enheter (förhands granskning)|Antal|Medel|Antal enheter som har registrerats för din IoT-hubb|Inget|
|connectedDeviceCount|Anslutna enheter (förhandsversion)|Antal|Medel|Antal enheter som är anslutna till din IoT-hubb|Inget|
|konfigurationer|Konfigurationsmått|Antal|Totalt|Mått för konfigurations åtgärder|Inget|

## <a name="next-steps"></a>Nästa steg

Nu när du har sett en översikt över IoT Hub måtten följer du den här länken för att lära dig mer om hur du hanterar Azure IoT Hub:

* [Konfigurera diagnostikloggar](iot-hub-monitor-resource-health.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)

* [Distribuera AI till gräns enheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
