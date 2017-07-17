---
title: "Skicka händelser till miljön för Azure Time Series Insights | Microsoft Docs"
description: "Den här självstudien tar upp hur du push-överför händelser till din Time Series Insights-miljö"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9f2d3b57a42efb7b04566278d3267b3cdbed713a
ms.contentlocale: sv-se
ms.lasthandoff: 07/01/2017

---
# Skicka händelser till en Time Series Insights-miljö via Event Hub
<a id="send-events-to-a-time-series-insights-environment-via-event-hub" class="xliff"></a>

I den här självstudien förklaras hur du skapar och konfigurerar Event Hub och kör ett exempelprogram för att push-överföra händelser. Om du har en befintlig händelsehubb som innehåller händelser i JSON-format kan du hoppa över den här självstudien och visa din miljö i [time series insights](https://insights.timeseries.azure.com).

## Skapa en Event Hub
<a id="configure-an-event-hub" class="xliff"></a>
1. Om du vill skapa en Event Hub följer du instruktionerna från Event Hub[-dokumentationen](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

2. Se till att skapa en konsumentgrupp som enbart används av din Time Series Insights-händelsekälla.

  > [!IMPORTANT]
  > Kontrollera att den här konsumentgruppen inte används av andra tjänster (t.ex Stream Analytics-jobb eller en annan Time Series Insights-miljö). Om en konsumentgrupp används av en annan tjänst påverkas läsåtgärden negativt för den här miljön och de andra tjänsterna. Om du använder ”$Default” som konsumentgrupp kan det leda till eventuell återanvändning av andra läsare.

  ![Välja konsumentgrupp för Event Hub](media/send-events/consumer-group.png)

3. I Event Hub skapar du ”MySendPolicy” som används för att skicka händelser i c#-exemplet.

  ![Välj Policyer för delad åtkomst och klicka på knappen Lägg till](media/send-events/shared-access-policy.png)  

  ![Lägg till ny policy för delad åtkomst](media/send-events/shared-access-policy-2.png)  

## Skapa händelsekälla för Time Series Insights
<a id="create-time-series-insights-event-source" class="xliff"></a>
1. Om du inte har skapat någon händelsekälla följer du instruktionerna [här](time-series-insights-add-event-source.md) för att skapa en händelsekälla.

2. Ange "deviceTimestamp" som egenskapsnamn för tidsstämpeln – den här egenskapen används som den faktiska tidsstämpeln i c#-exemplet. Egenskapsnamnet för tidsstämpeln är skiftlägeskänsligt och värdena ska ha formatet __åååå-MM-ddTHH:mm:ss.FFFFFFFK__ när det skickas som JSON till Event Hub. Om egenskapen inte finns i händelsen används den tid då händelsen stod i Event Hub-kön.

  ![Skapa händelsekälla](media/send-events/event-source-1.png)

## Exempelkod för push-händelser
<a id="sample-code-to-push-events" class="xliff"></a>
1. Gå till Event Hub-principen ”MySendPolicy” och kopiera anslutningssträngen med principnyckeln.

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
## JSON-former som stöds
<a id="supported-json-shapes" class="xliff"></a>
### Exempel 1
<a id="sample-1" class="xliff"></a>

#### Indata
<a id="input" class="xliff"></a>

Ett enda JSON-objekt.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### Resultat – 1 händelse
<a id="output---1-event" class="xliff"></a>

|id|tidsstämpel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### Exempel 2
<a id="sample-2" class="xliff"></a>

#### Indata
<a id="input" class="xliff"></a>
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
#### Resultat – 2 händelser
<a id="output---2-events" class="xliff"></a>

|id|tidsstämpel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|
### Exempel 3
<a id="sample-3" class="xliff"></a>

#### Indata
<a id="input" class="xliff"></a>

Ett JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt.
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
#### Resultat – 2 händelser
<a id="output---2-events" class="xliff"></a>
Observera att egenskapen "plats" kopieras till varje händelse.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### Exempel 4
<a id="sample-4" class="xliff"></a>

#### Indata
<a id="input" class="xliff"></a>

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
#### Resultat – 2 händelser
<a id="output---2-events" class="xliff"></a>

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## Nästa steg
<a id="next-steps" class="xliff"></a>

* Visa din miljö i [Time Series Insights-portalen](https://insights.timeseries.azure.com)

