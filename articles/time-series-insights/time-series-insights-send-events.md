---
title: Hur du skickar händelser till en Azure tid serien Insights miljö | Microsoft Docs
description: Den här självstudiekursen beskrivs hur du skapa och konfigurera händelsehubb och köra exempelprogrammet push-händelser som ska visas i Azure tid serien insikter.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 04/09/2018
ms.openlocfilehash: c29b90e703a66cbbc25227f9a4307c74d82b03b5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Skicka händelser till en Time Series Insights-miljö med hjälp av Event Hub
Den här artikeln beskriver hur du skapar och konfigurerar händelsehubb och kör ett exempelprogram push-händelser. Om du har en befintlig händelsehubb med händelser i JSON-format, hoppa över den här kursen och visa din miljö i [tid serien insikter](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub
1. Om du vill skapa en Event Hub följer du instruktionerna från Event Hub[-dokumentationen](../event-hubs/event-hubs-create.md).

2. Sök efter **händelsehubb** i sökfältet. Klicka på **Händelsehubbar** i listan returneras.

3. Välj din händelsehubb genom att klicka på dess namn.

4. Under **entiteter** i mitten configuration-fönstret klickar du på **Händelsehubbar** igen.

5. Välj namnet på händelsehubben för att konfigurera den.

  ![Välja konsumentgrupp för Event Hub](media/send-events/consumer-group.png)

6. Under **entiteter**väljer **konsumentgrupper**.
 
7. Se till att skapa en konsumentgrupp som enbart används av din Time Series Insights-händelsekälla.

   > [!IMPORTANT]
   > Kontrollera att den här konsumentgruppen inte används av andra tjänster (t.ex Stream Analytics-jobb eller en annan Time Series Insights-miljö). Om konsumentgruppen används av andra påverkas negativt tjänster, Läsåtgärd för den här miljön och andra tjänster. Om du använder ”$Default” som konsumentgrupp kan det leda till eventuell återanvändning av andra läsare.

8. Under den **inställningar** rubrik, Välj **resursen åtkomstprinciper**.

9. Skapa på händelsehubb, **MySendPolicy** som används för att skicka händelser i csharp-exemplet.

  ![Välj Policyer för delad åtkomst och klicka på knappen Lägg till](media/send-events/shared-access-policy.png)  

  ![Lägg till ny policy för delad åtkomst](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Lägg till tid serien insikter referens datauppsättning 
Använda referensdata i TSD contextualizes telemetridata.  Den kontexten lägger till betydelse till dina data och gör det enklare att filtrera och aggregering.  TSD kopplingar referensdata ingång för närvarande och retroaktivt kan inte ansluta till dessa data.  Det är därför viktigt att lägga till referensdata innan du lägger till en händelsekälla med data.  Data som plats-eller sensor är användbar dimensioner som kanske du vill ansluta till en enhet/taggen/sensor-ID för att göra det enklare att segment och filter.  

> [!IMPORTANT]
> Det är viktigt att ha en referens datauppsättning som konfigurerats när du överför historiska data.

Se till att du har referensdata på plats när du massimportera överför historiska data till TSD.  Kom ihåg, TSD kommer omedelbart börja läsa från en domänansluten händelsekälla om den händelsekällan innehåller data.  Det är praktiskt att vänta med att ansluta till en händelsekälla till TSD tills du har din referensdata på plats, särskilt om den händelsekällan har data i den. Alternativt kan du vänta på att vidarebefordra data till den händelsekällan tills datamängden som referens är på plats.

Om du vill hantera referensdata finns webbaserade användargränssnittet i TSD Explorer och det finns en programmässiga C#-API. TSD Explorer har en visual användarmiljö Överför filer eller klistra in befintliga referens datauppsättningar som JSON- eller CSV-format. Med API, kan du skapa en anpassad app när det behövs.

Mer information om hur du hanterar referensdata i tid serien insikter finns i [data referensartikeln](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Skapa händelsekälla för Time Series Insights
1. Om du inte har skapat en händelsekälla följer du [dessa instruktioner](time-series-insights-how-to-add-an-event-source-eventhub.md) för att skapa en händelsekälla.

2. Ange **deviceTimestamp** som egenskapsnamn tidsstämpel – den här egenskapen används som den faktiska tidsstämpeln i C#-exempel. Egenskapsnamnet för tidsstämpeln är skiftlägeskänsligt och värdena måste ha formatet __åååå-MM-ddTHH:mm:ss.FFFFFFFK__ när de skickas som JSON till Event Hub. Om egenskapen inte finns i händelsen används den tid då händelsen stod i Event Hub-kön.

  ![Skapa händelsekälla](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Exempelkod för push-händelser
1. Gå till event hub-princip med namnet **MySendPolicy**. Kopiera den **anslutningssträngen** med nyckeln för principen.

  ![Kopiera anslutningssträngen MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Kör följande kod som skickar 600 händelser till var och en av de tre enheterna. Uppdatera `eventHubConnectionString` med anslutningssträngen.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>JSON-former som stöds
### <a name="sample-1"></a>Exempel 1

#### <a name="input"></a>Indata

Ett enda JSON-objekt.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---one-event"></a>Utdata - en händelse

|id|tidsstämpel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Exempel 2

#### <a name="input"></a>Indata
En JSON-matris med två JSON-objekt. Varje JSON-objekt konverteras till en händelse.
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---two-events"></a>Utdata - två händelser

|id|tidsstämpel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Exempel 3

#### <a name="input"></a>Indata

Ett JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt:
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---two-events"></a>Utdata - två händelser
Observera att egenskapen ”plats” kopieras över till var och en av händelsen.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Exempel 4

#### <a name="input"></a>Indata

Ett JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt. Denna indata visar att de globala egenskaperna kan representeras av komplexa JSON-objekt.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---two-events"></a>Utdata - två händelser

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

### <a name="json-shaping-strategies"></a>JSON forma strategier
Använd följande exempel visar en händelse vi en startadress pekar och diskutera problem med den och strategier för hur du minimerar dessa problem.

#### <a name="payload-1"></a>Nyttolasten 1:
```json
[{
            "messageId": "LINE_DATA",
            "deviceId": "FXXX",
            "timestamp": 1522355650620,
            "series": [{
                        "chId": 3,
                        "value": -3750.0
                  }, {
                        "chId": 13,
                        "value": 0.58015072345733643
                  }, {
                        "chId": 11,
                        "value": 800.0
                  }, {
                        "chId": 21,
                        "value": 0.0
                  }, {
                        "chId": 14,
                        "value": -999.0
                  }, {
                        "chId": 37,
                        "value": 2.445906400680542
                  }, {
                        "chId": 39,
                        "value": 0.0
                  }, {
                        "chId": 40,
                        "value": 1.0
                  }, {
                        "chId": 1,
                        "value": 1.0172575712203979
                  }
            ],
            "EventProcessedUtcTime": "2018-03-29T20:36:21.3245900Z",
            "PartitionId": 2,
            "EventEnqueuedUtcTime": "2018-03-29T20:34:11.0830000Z",
            "IoTHub": {
                  "MessageId": "<17xxx2xx-36x0-4875-9x1x-x428x41x1x68>",
                  "CorrelationId": "<x253x5xx-7xxx-4xx3-91x4-xxx3bx2xx0x3>",
                  "ConnectionDeviceId": "AAAA-ZZ-001",
                  "ConnectionDeviceGenerationId": "<123456789012345678>",
                  "EnqueuedTime": "2018-03-29T20:34:10.7990000Z",
                  "StreamId": null
            }
      }
]
 ```

Om du trycker denna matris av händelser som en nyttolast till TSD, lagras den som en händelse per varje måttvärde. Då kan du skapa ett överskott av händelser som inte kanske är optimal. Observera att du kan använda referensdata i TSD för att lägga till meningsfulla namn som egenskaper.  Du kan till exempel skapa referens datauppsättning med nyckelegenskapen = chId:  

chId mått enhet 24 motorn olja trycket PSI 25 BERÄKNA Pump hastighet bbl per minut

Mer information om hur du hanterar referensdata i tid serien insikter finns i [data referensartikeln](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-add-reference-data-set).

Ett annat problem med den första nyttolasten är tidsstämpeln är i millisekunder. TSD accepterar bara ISO-formaterat tidsstämplar. En lösning är att lämna tidsstämpel standardbeteendet i TSD, vilket är att använda köas tidsstämpel.

Som ett alternativ till nyttolasten ovan ska vi titta på ett annat exempel.  

#### <a name="payload-2"></a>Nyttolasten 2:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "STATE Engine State": 1,
      "unit": "NONE"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "MPC_AAAA-ZZ-001",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Well Head Px 1": -494162.8515625,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "unit": "bbl/min"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Fuel Pressure": 0,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Oil Pressure": 0.58015072345733643,
      "unit": "psi"
}
```

Som nyttolast 1 lagrar TSD varje varje uppmätt värde som en unik händelse.  Den viktiga skillnaden är att TSD läser den *tidsstämpel* som korrekt här som ISO.  

Om du behöver minska antalet händelser som skickas kan du skicka informationen som följer.  

#### <a name="payload-3"></a>Nyttolasten 3:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```
En slutlig är nedan.

#### <a name="payload-4"></a>Nyttolasten 4:
```json
{
              "line": "Line01",
              "station": "Station 11",
              "gatewayid": "AAAA-ZZ-001",
              "deviceid": "F12XX",
              "timestamp": "2018-03-29T20:34:15.0000000Z",
              "CALC Pump Rate": {
                           "value": 0,
                           "unit": "bbl/min"
              },
              "Engine Oil Pressure": {
                           "value": 0.58015072345733643,
                           "unit": "psi"
              },
              "Engine Fuel Pressure": {
                           "value": 0,
                           "unit": "psi"
              }
}
```

Det här exemplet visar utdata efter förenkla JSON:

```json
{
      "line": "Line01",
      "station": "Station 11",,
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate.value": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure.value": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure.value": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```

Har du friheten att definiera olika egenskaper för varje kanaler i sin egen json-objekt samtidigt som händelseantal låg. Den här Flat metoden tar upp mer utrymme, vilket är viktigt att tänka på. TSD kapacitet baseras på både händelser och storlek, beroende på vilket som inträffar först.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Visa din miljö i tid serien insikter explorer](https://insights.timeseries.azure.com).
