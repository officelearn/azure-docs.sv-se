---
title: Använd mått för att övervaka Azure-IoT Hub | Microsoft Docs
description: Använda Azure IoT Hub mått för att utvärdera och övervaka den övergripande hälsan hos dina IoT-hubbar.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: f0bcf12a43a4732b371dd2d64c0b174a0087bea9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098947"
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
|D2C<br>. telemetri<br>ingångs.<br>allProtocol|Skicka försök för telemetri|Count|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inga dimensioner|
|D2C<br>. telemetri<br>. ingress<br>.success|Meddelande om telemetri|Count|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inga dimensioner|
|C2D<br>. kommandon<br>. utgående<br>. Complete<br>.success|Slutförda kommandon|Count|Totalt|Antalet moln-till-enhet-kommandon som har slutförts av enheten|Inga dimensioner|
|C2D<br>. kommandon<br>. utgående<br>.abandon<br>.success|Övergivna kommandon|Count|Totalt|Antalet moln-till-enhet-kommandon som avbrutits av enheten|Inga dimensioner|
|C2D<br>. kommandon<br>. utgående<br>. avvisa<br>.success|Nekade kommandon|Count|Totalt|Antal moln-till-enhet-kommandon som avvisats av enheten|Inga dimensioner|
|enheter<br>.totalDevices|Totalt antal enheter (inaktuella)|Count|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|enheter<br>.connectedDevices<br>.allProtocol|Anslutna enheter (inaktuella) |Count|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|D2C<br>. telemetri<br>. utgående<br>.success|Routning: telemetri meddelanden levereras|Count|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|D2C<br>. telemetri<br>. utgående<br>. borttagen|Routning: telemetri ignoreras |Count|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|D2C<br>. telemetri<br>. utgående<br>. överblivna|Routning: telemetri-meddelanden har överblivna |Count|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inga dimensioner|
|D2C<br>. telemetri<br>. utgående<br>. ogiltig|Routning: telemetri-meddelanden är inkompatibla|Count|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inga dimensioner|
|D2C<br>. telemetri<br>. utgående<br>. fallback|Routning: meddelanden levererade till reserv|Count|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inga dimensioner|
|D2C<br>. slut punkter<br>. utgående<br>.eventHubs|Routning: meddelanden levererade till Händelsehubben|Count|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|D2C<br>. slut punkter<br>. latens<br>.eventHubs|Routning: meddelande fördröjning för Event Hub|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inga dimensioner|
|D2C<br>. slut punkter<br>. utgående<br>.serviceBusQueues|Routning: meddelanden levererade till Service Bus kö|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inga dimensioner|
|D2C<br>. slut punkter<br>. latens<br>.serviceBusQueues|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inga dimensioner|
|D2C<br>. slut punkter<br>. utgående<br>.serviceBusTopics|Routning: meddelanden levererade till Service Bus ämnet|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inga dimensioner|
|D2C<br>. slut punkter<br>. latens<br>.serviceBusTopics|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inga dimensioner|
|D2C<br>. slut punkter<br>. utgående<br>.builtIn<br>. events|Routning: meddelanden som levereras till meddelanden/händelser|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser). Det här måttet börjar bara fungera när routning är https://aka.ms/iotrouting) aktiverat (för IoT Hub.|Inga dimensioner|
|D2C<br>. slut punkter<br>. latens<br>. Builtin. events|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser). Det här måttet börjar bara fungera när routning är https://aka.ms/iotrouting) aktiverat (för IoT Hub.|Inga dimensioner|
|D2C<br>. slut punkter<br>. utgående<br>. Storage|Routning: meddelanden som levereras till lagring|Count|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inga dimensioner|
|D2C<br>. slut punkter<br>. latens<br>. Storage|Routning: meddelande fördröjning för lagring|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inga dimensioner|
|D2C<br>. slut punkter<br>. utgående<br>. Storage<br>. byte|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inga dimensioner|
|D2C<br>. slut punkter<br>. utgående<br>. Storage<br>.blobs|Routning: blobbar levererade till lagring|Count|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inga dimensioner|
|EventGridDeliveries|Event Grid leveranser (för hands version)|Count|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen (https://aka.ms/ioteventgrid). Använd EventType-dimensionen för att se var förfrågningen kommer från.|Resultat, EventType|
|EventGridLatency|Event Grid svars tid (för hands version)|Millisekunder|Average|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|Händelsetyp|
|D2C<br>.twin<br>. Läs<br>.success|Lyckades dubbla läsningar från enheter|Count|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inga dimensioner|
|D2C<br>.twin<br>. Läs<br>. Failure|Misslyckade dubbla läsningar från enheter|Count|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C<br>.twin<br>. Läs<br>.size|Svars storlek för dubbla läsningar från enheter|Byte|Average|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C<br>.twin<br>.update<br>.success|Lyckade dubbla uppdateringar från enheter|Count|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C<br>.twin<br>.update<br>. Failure|Misslyckade dubbla uppdateringar från enheter|Count|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C<br>.twin<br>.update<br>.size|Storlek på dubbla uppdateringar från enheter|Byte|Average|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|C2D<br>. metoder<br>.success|Direkta metod anrop|Count|Totalt|Antalet lyckade direkta metod anrop.|Inga dimensioner|
|C2D<br>. metoder<br>. Failure|Misslyckade direkta metod anrop|Count|Totalt|Antalet misslyckade direkta metod anrop.|Inga dimensioner|
|C2D<br>. metoder<br>.requestSize|Begär ande storlek för direkta metod anrop|Byte|Average|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inga dimensioner|
|C2D<br>. metoder<br>.responseSize|Svars storlek för direkta metod anrop|Byte|Average|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inga dimensioner|
|C2D<br>.twin<br>. Läs<br>.success|Lyckades dubbla läspaket från Server delen|Count|Totalt|Antalet dubbla läsningar som initierats av alla lyckade. Det här antalet inkluderar inte dubbla läsningar som initieras från dubbla frågor.|Inga dimensioner|
|C2D<br>.twin<br>. Läs<br>. Failure|Det gick inte att dubbla läsningar från Server delen|Count|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D<br>.twin<br>. Läs<br>.size|Svars storlek för dubbla läsningar från Server delen|Byte|Average|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inga dimensioner|
|C2D<br>.twin<br>.update<br>.success|Lyckades dubbla uppdateringar från Server delen|Count|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inga dimensioner|
|C2D<br>.twin<br>.update<br>. Failure|Misslyckade dubbla uppdateringar från Server delen|Count|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D<br>.twin<br>.update<br>.size|Storlek på dubbla uppdateringar från Server delen|Byte|Average|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inga dimensioner|
|twinQueries<br>.success|Lyckades dubbla frågor|Count|Totalt|Antalet lyckade dubbla frågor.|Inga dimensioner|
|twinQueries<br>. Failure|Misslyckade dubbla frågor|Count|Totalt|Antalet misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries<br>.resultSize|Resultat storlek för dubbla frågor|Byte|Average|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inga dimensioner|
|jobb<br>.createTwinUpdateJob<br>.success|Skapandet av dubbla uppdaterings jobb lyckades|Count|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|jobb<br>.createTwinUpdateJob<br>. Failure|Det gick inte att skapa dubbla uppdaterings jobb|Count|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|jobb<br>.createDirectMethodJob<br>.success|Lyckade skapande av metod anrops jobb|Count|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inga dimensioner|
|jobb<br>.createDirectMethodJob<br>. Failure|Det gick inte att skapa metod anrops jobb|Count|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inga dimensioner|
|jobb<br>.listJobs<br>.success|Lyckade anrop till List jobb|Count|Totalt|Antalet lyckade anrop till List jobb.|Inga dimensioner|
|jobb<br>.listJobs<br>. Failure|Misslyckade anrop till List jobb|Count|Totalt|Antalet misslyckade anrop till List jobb.|Inga dimensioner|
|jobb<br>.cancelJob<br>.success|Slutförda jobb avbokningar|Count|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobb<br>.cancelJob<br>. Failure|Misslyckade jobb-annulleringar|Count|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobb<br>.queryJobs<br>.success|Slutförda jobb frågor|Count|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inga dimensioner|
|jobb<br>.queryJobs<br>. Failure|Misslyckade jobb frågor|Count|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inga dimensioner|
|jobb<br>. slutförd|Slutförda jobb|Count|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|jobb<br>. misslyckades|Misslyckade jobb|Count|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|D2C<br>. telemetri<br>. ingress<br>.sendThrottle|Antal begränsnings fel|Count|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inga dimensioner|
|dailyMessage<br>QuotaUsed|Totalt antal meddelanden som används|Count|Average|Antal sammanlagt antal meddelanden som använts idag. Detta är ett ackumulerat värde som återställs till noll vid 00:00 UTC varje dag.|Inga dimensioner|
|deviceDataUsage|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|totalDeviceCount|Totalt antal enheter (förhands granskning)|Count|Average|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|Länk<br>DeviceCount|Anslutna enheter (förhands granskning)|Count|Average|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|konfigurationer|Konfigurations mått|Count|Totalt|Mått för konfigurations åtgärder|Inga dimensioner|

## <a name="next-steps"></a>Nästa steg

Nu när du har sett en översikt över IoT Hub måtten följer du den här länken för att lära dig mer om hur du hanterar Azure IoT Hub:

* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)

* [Distribuera AI till gräns enheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
