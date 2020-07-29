---
title: Använd mått för att övervaka Azure-IoT Hub | Microsoft Docs
description: Använda Azure IoT Hub mått för att utvärdera och övervaka den övergripande hälsan hos dina IoT-hubbar.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 808320f89c4dbeca835fc5a710ea1566199f6884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791851"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>Förstå IoT Hub mått

IoT Hub mått ger dig information om status för Azure IoT-resurser i din Azure-prenumeration. IoT Hub Mät värden hjälper dig att utvärdera den övergripande hälsan hos IoT Hubs tjänsten och de enheter som är anslutna till den. Dessa statistik för användare hjälper dig att se vad som händer med din IoT-hubb och hjälper till med rotor Saks analys av problem utan att behöva kontakta Azure-supporten.

Mått är aktiverat som standard. Du kan visa IoT Hub mått från Azure Portal.

> [!NOTE]
> Du kan använda IoT Hub mått för att visa information om IoT Plug and Play-enheter som är anslutna till din IoT Hub. IoT Plug and Play-enheter ingår i [iot plug and Play offentlig för hands version](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Visa IoT Hub mått

1. Skapa en IoT-hubb. Du hittar instruktioner om hur du skapar en IoT Hub i [Skicka telemetri från en enhet till IoT Hub](quickstart-send-telemetry-dotnet.md) guide.

2. Öppna bladet med din IoT Hub. Därifrån klickar du på **mått**.
   
    ![Skärm bild som visar var mått alternativet finns på IoT Hub resurs sida](./media/iot-hub-metrics/enable-metrics-1.png)

3. Från mått bladet kan du visa måtten för din IoT-hubb och skapa anpassade vyer av dina mått. 
   
    ![Skärm bild som visar mått sidan för IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Du kan välja att skicka dina mått data till en Event Hubs-slutpunkt eller ett Azure Storage konto genom att klicka på diagnostikinställningar och sedan **lägga till diagnostiska** **Inställningar**.

   ![Skärm bild som visar var diagnostikinställningar-knappen är](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub mått och hur du använder dem

IoT Hub tillhandahåller flera mått för att ge dig en översikt över hälsan för hubben och det totala antalet anslutna enheter. Du kan kombinera information från flera mått för att måla en större bild av läget för IoT Hub. I följande tabell beskrivs måtten för varje IoT Hub-spår och hur varje mått relaterar till den övergripande statusen för IoT Hub.

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|
|RoutingDeliveries | Routnings leverans försök (förhands granskning) | Antal | Totalt |Detta är mått för routnings leverans. Använd dimensionerna för att identifiera leverans status för en angiven slut punkt eller för en speciell Dirigerings källa.| ResourceID<br>Medför<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>EndpointName<br>*Mer information om dimensioner finns [**här**](#dimensions)*. |
|RoutingDeliveryLatency| Svars tid för routning (för hands version) | Millisekunder | Medel |Detta är måttet för leverans fördröjning i routning. Använd dimensionerna för att identifiera svars tiden för en speciell slut punkt eller för en speciell Dirigerings källa.| ResourceID<br>RoutingSource,<br>EndpointType,<br>EndpointName<br>*Mer information om dimensioner finns [**här**](#dimensions)*.|
|RoutingDataSizeInBytesDelivered| Flödes leverans data storlek i byte (för hands version)| Byte | Totalt |Det totala antalet byte som dirigerats av IoT Hub till den anpassade slut punkten och den inbyggda slut punkten. Använd dimensionerna för att identifiera data storleken dirigerad till en angiven slut punkt eller för en speciell vägkälla.| ResourceID<br>RoutingSource,<br>EndpointType<br>EndpointName<br>*Mer information om dimensioner finns [**här**](#dimensions)*.|
|D2C. telemetri. ingress.<br>allProtocol|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Ingen|
|D2C. telemetri. ingress.<br>lyckades|Meddelande om telemetri|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Ingen|
|C2D. commands. utgående.<br>slutfört. lyckades|C2D meddelande leveranser har slutförts|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Ingen|
|C2D. commands. utgående.<br>överge. lyckades|Övergivna C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Ingen|
|C2D. commands. utgående.<br>avvisa. lyckades|Avvisade C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Ingen|
|C2DMessagesExpired|C2D meddelanden har förfallit (förhands granskning)|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Ingen|
|enheter. totalDevices|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Ingen|
|Devices. connectedDevices.<br>allProtocol|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Ingen|
|D2C. telemetri. utgående.<br>lyckades|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Ingen|
|D2C. telemetri. utgående.<br>släpper|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Ingen|
|D2C. telemetri. utgående.<br>överblivna|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade någon cirkulations fråga när reserv vägen är inaktive rad.|Ingen|
|D2C. telemetri. utgående.<br>tillåtet|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Ett meddelande är inkompatibelt med en slut punkt när IoT Hub försöker leverera meddelandet till en slut punkt och det Miss lyckas med ett icke-tillfälligt fel. Ogiltiga meddelanden har inte gjorts om. Det här värdet omfattar inte återförsök.|Ingen|
|D2C. telemetri. utgående.<br>grund|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Ingen|
|D2C. endpoints. utgående.<br>eventHubs|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till anpassade slut punkter av typen Event Hub. Detta inkluderar inte meddelanden vägar till den inbyggda slut punkten (händelser).|Ingen|
|D2C. endpoints. latens.<br>eventHubs|Routning: meddelande fördröjning för Event Hub|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i anpassade slut punkter av typen Event Hub. Detta inkluderar inte meddelanden vägar till den inbyggda slut punkten (händelser).|Ingen|
|D2C. endpoints. utgående.<br>serviceBusQueues|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Ingen|
|D2C. endpoints. latens.<br>serviceBusQueues|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i en Service Bus Queue-slutpunkt.|Ingen|
|D2C. endpoints. utgående.<br>serviceBusTopics|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Ingen|
|D2C. endpoints. latens.<br>serviceBusTopics|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i en Service Bus avsnitts slut punkt.|Ingen|
|D2C. endpoints. utgående.<br>Builtin. events|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser) och återställnings väg.|Ingen|
|D2C. endpoints. latens.<br>Builtin. events|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i den inbyggda slut punkten (meddelanden/händelser) och återställnings väg.|Ingen|
|D2C. endpoints. utgående.<br>storage|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Ingen|
|D2C. endpoints. latens.<br>storage|Routning: meddelande fördröjning för lagring|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i en lagrings slut punkt.|Ingen|
|D2C. endpoints. utgående.<br>lagring. byte|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Ingen|
|D2C. endpoints. utgående.<br>Storage. blob|Routning: blobbar levererade till lagring|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Ingen|
|EventGridDeliveries|Event Grid leveranser (för hands version)|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen ( https://aka.ms/ioteventgrid) .|ResourceID<br/>Medför<br/>Typ|
|EventGridLatency|Event Grid svars tid (för hands version)|Millisekunder|Medel|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|ResourceID<br/>Typ|
|D2C. delad. lyckades|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Ingen|
|D2C. delad.|Misslyckade dubbla läsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Ingen|
|D2C., delad. storlek|Svars storlek för dubbla läsningar från enheter|Byte|Medel|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Ingen|
|D2C. delad. Update. lyckades|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Ingen|
|D2C. delad. Update. Failure|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Ingen|
|D2C. dubbla. Update. size|Storlek på dubbla uppdateringar från enheter|Byte|Medel|Den totala storleken på alla lyckade, dubbla uppdateringar som initierats av enheten.|Ingen|
|C2D. Methods. Success|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Ingen|
|C2D. Methods. Failure|Misslyckade direkta metod anrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Ingen|
|C2D. Methods. requestSize|Begär ande storlek för direkta metod anrop|Byte|Medel|Antalet lyckade direkta metod begär Anden.|Ingen|
|C2D. Methods. responseSize|Svars storlek för direkta metod anrop|Byte|Medel|Antalet slutförda direkta metod svar.|Ingen|
|C2D. delad. lyckades|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Ingen|
|C2D. delad.|Det gick inte att dubbla läsningar från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Ingen|
|C2D., delad. storlek|Svars storlek för dubbla läsningar från Server delen|Byte|Medel|Antalet dubbla läsningar som initierats av alla lyckade.|Ingen|
|C2D. delad. Update. lyckades|Lyckades dubbla uppdateringar från Server delen|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Ingen|
|C2D. delad. Update. Failure|Misslyckade dubbla uppdateringar från Server delen|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Ingen|
|C2D. dubbla. Update. size|Storlek på dubbla uppdateringar från Server delen|Byte|Medel|Den totala storleken på alla lyckade, dubbla uppdateringar som initieras.|Ingen|
|twinQueries. lyckades|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Ingen|
|twinQueries. Failure|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Ingen|
|twinQueries.resultSize|Resultat storlek för dubbla frågor|Byte|Medel|Summan av resultat storleken för alla lyckade dubbla frågor.|Ingen|
|Jobs. createTwinUpdateJob.<br>lyckades|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Ingen|
|Jobs. createTwinUpdateJob.<br>haverera|Det gick inte att skapa dubbla uppdaterings jobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Ingen|
|Jobs. createDirectMethodJob.<br>lyckades|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Ingen|
|Jobs. createDirectMethodJob.<br>haverera|Det gick inte att skapa metod anrops jobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Ingen|
|Jobs. listJobs. lyckades|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Ingen|
|Jobs. listJobs. Failure|Misslyckade anrop till List jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Ingen|
|Jobs. cancelJob. lyckades|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Ingen|
|Jobs. cancelJob. Failure|Misslyckade jobb-annulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Ingen|
|Jobs. queryJobs. lyckades|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Ingen|
|Jobs. queryJobs. Failure|Misslyckade jobb frågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Ingen|
|jobb. slutfört|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Ingen|
|jobb. misslyckades|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Ingen|
|D2C. telemetri. ingress.<br>sendThrottle|Antal begränsnings fel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Ingen|
|dailyMessageQuotaUsed|Totalt antal meddelanden som används|Antal|Medel|Antal sammanlagt antal meddelanden som använts idag. Detta är ett ackumulerat värde som återställs till noll vid 00:00 UTC varje dag.|Ingen|
|deviceDataUsage|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Ingen|
|deviceDataUsageV2|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Ingen|
|totalDeviceCount|Totalt antal enheter (förhands granskning)|Antal|Medel|Antal enheter som har registrerats för din IoT-hubb|Ingen|
|connectedDeviceCount|Anslutna enheter (förhands granskning)|Antal|Medel|Antal enheter som är anslutna till din IoT-hubb|Ingen|
|konfigurationer|Konfigurations mått|Antal|Totalt|Antalet totala CRUD-åtgärder som utförts för enhets konfiguration och IoT Edge distribution på en uppsättning mål enheter. Detta omfattar även antalet åtgärder som ändrar enheten till dubbla eller modulerna på grund av dessa konfigurationer.|Ingen|

### <a name="dimensions"></a>Dimensioner
Dimensionerna hjälper till att identifiera mer information om måtten. Några av måtten för routning tillhandahåller information per slut punkt. I tabellen nedan visas möjliga värden för dessa dimensioner.

|Dimension|Värden|
|---|---|
|ResourceID|din IoT Hub-resurs|
|Resultat|lyckades<br>haverera|
|RoutingSource|Enhets meddelanden<br>Dubbla ändrings händelser<br>Livs cykel händelser för enhet|
|EndpointType|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>definiera<br>blobStorage|
|FailureReasonCategory|tillåtet<br>släpper<br>överblivna<br>null|
|EndpointName|slut punkts namn|

## <a name="next-steps"></a>Nästa steg

Nu när du har sett en översikt över IoT Hub måtten följer du den här länken för att lära dig mer om hur du hanterar Azure IoT Hub:

* [Konfigurera diagnostikloggar](iot-hub-monitor-resource-health.md)

* [Lär dig hur du felsöker meddelanderoutning](troubleshoot-message-routing.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
