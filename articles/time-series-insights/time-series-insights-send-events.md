---
title: "Skicka händelser till miljön för Azure Time Series Insights | Microsoft Docs"
description: "Den här självstudiekursen beskriver de steg du följer för att skicka händelser till en Time Series Insights-miljö"
keywords: 
services: tsi
documentationcenter: 
author: venkatgct
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/21/2017
ms.author: venkatja
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: b4ef96a045393f28b3cd750068fe82a5a8411afa
ms.contentlocale: sv-se
ms.lasthandoff: 07/24/2017

---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Skicka händelser till en Time Series Insights-miljö med hjälp av Event Hub

I den här självstudien förklaras hur du skapar och konfigurerar Event Hub och kör ett exempelprogram för att push-överföra händelser. Om du har en befintlig händelsehubb som innehåller händelser i JSON-format kan du hoppa över den här självstudien och visa din miljö i [time series insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub
1. Om du vill skapa en Event Hub följer du instruktionerna från Event Hub[-dokumentationen](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

2. Se till att skapa en konsumentgrupp som enbart används av din Time Series Insights-händelsekälla.

  > [!IMPORTANT]
  > Kontrollera att den här konsumentgruppen inte används av andra tjänster (t.ex Stream Analytics-jobb eller en annan Time Series Insights-miljö). Om en konsumentgrupp används av en annan tjänst påverkas läsåtgärden negativt för den här miljön och de andra tjänsterna. Om du använder ”$Default” som konsumentgrupp kan det leda till eventuell återanvändning av andra läsare.

  ![Välja konsumentgrupp för Event Hub](media/send-events/consumer-group.png)

3. I Event Hub skapar du ”MySendPolicy” som används för att skicka händelser i c#-exemplet.

  ![Välj Policyer för delad åtkomst och klicka på knappen Lägg till](media/send-events/shared-access-policy.png)  

  ![Lägg till ny policy för delad åtkomst](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>Skapa händelsekälla för Time Series Insights
1. Om du inte har skapat en händelsekälla följer du [dessa instruktioner](time-series-insights-add-event-source.md) för att skapa en händelsekälla.

2. Ange "deviceTimestamp" som egenskapsnamn för tidsstämpeln – den här egenskapen används som den faktiska tidsstämpeln i c#-exemplet. Egenskapsnamnet för tidsstämpeln är skiftlägeskänsligt och värdena måste ha formatet __åååå-MM-ddTHH:mm:ss.FFFFFFFK__ när de skickas som JSON till Event Hub. Om egenskapen inte finns i händelsen används den tid då händelsen stod i Event Hub-kön.

  ![Skapa händelsekälla](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Exempelkod för push-händelser
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
#### <a name="output---1-event"></a>Resultat – 1 händelse

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
#### <a name="output---2-events"></a>Resultat – 2 händelser

|id|tidsstämpel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|
### <a name="sample-3"></a>Exempel 3

#### <a name="input"></a>Indata

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
#### <a name="output---2-events"></a>Resultat – 2 händelser
Observera att egenskapen "plats" kopieras till varje händelse.

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
#### <a name="output---2-events"></a>Resultat – 2 händelser

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Nästa steg

* Visa din miljö i [Time Series Insights-portalen](https://insights.timeseries.azure.com)

