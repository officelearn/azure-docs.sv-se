---
title: Hur man skickar händelser till en Azure Time Series Insights-miljö | Microsoft Docs
description: Den här självstudien beskrivs hur du skapar och konfigurerar event hub och kör ett exempelprogram för push-händelser som ska visas i Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 30b83c54d314934f1de170955eec22e7b2a264b8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629760"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Skicka händelser till en Time Series Insights-miljö med hjälp av Event Hub
Den här artikeln förklarar hur du skapar och konfigurerar event hub och kör ett exempelprogram för push-händelser. Om du har en befintlig händelsehubb med händelser i JSON-format, hoppa över den här självstudien och visa din miljö i [Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub
1. Om du vill skapa en Event Hub följer du instruktionerna från Event Hub[-dokumentationen](../event-hubs/event-hubs-create.md).

2. Sök efter **händelsehubb** i sökfältet. Klicka på **Händelsehubbar** i den returnerade listan.

3. Välj din event hub genom att klicka på dess namn.

4. Under **entiteter** i fönstret i mitten konfiguration klickar du på **Händelsehubbar** igen.

5. Välj namnet på händelsehubben för att konfigurera den.

  ![Välja konsumentgrupp för Event Hub](media/send-events/consumer-group.png)

6. Under **entiteter**väljer **konsumentgrupper**.
 
7. Se till att skapa en konsumentgrupp som enbart används av din Time Series Insights-händelsekälla.

   > [!IMPORTANT]
   > Kontrollera att den här konsumentgruppen inte används av andra tjänster (t.ex Stream Analytics-jobb eller en annan Time Series Insights-miljö). Om konsumentgruppen används av andra påverkas tjänster, Läsåtgärd negativt för den här miljön och andra tjänster. Om du använder ”$Default” som konsumentgrupp kan det leda till eventuell återanvändning av andra läsare.

8. Under den **inställningar** väljer **åtkomstprinciper för filresursen**.

9. Skapa på event hub **MySendPolicy** som används för att skicka händelser i c#-exemplet.

  ![Välj Policyer för delad åtkomst och klicka på knappen Lägg till](media/send-events/shared-access-policy.png)  

  ![Lägg till ny policy för delad åtkomst](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Lägg till Time Series Insights-referensdatauppsättningen 
Med hjälp av referensdata i TSD beräkning telemetridata.  Den kontexten lägger till vilket innebär att dina data och gör det enklare att filtrera och aggregering.  TSI-kopplingar referensdata när ingress och retroaktivt kan inte ansluta till dessa data.  Det är därför viktigt att lägga till referensdata innan du lägger till en händelsekälla med data.  Data som typ av plats eller sensorn är användbara dimensioner som du kanske vill ansluta till en enhet/taggen/sensor ID att göra det enklare att sektorn och filter.  

> [!IMPORTANT]
> Det är viktigt att ha en referensdatauppsättning som konfigurerats när du överför historiska data.

Kontrollera att du har referensdata på plats när du massimportera uppladdning historiska data till TSD.  Tänk på, TSI kommer omedelbart börja läsa från en domänansluten händelsekälla om den händelsekällan har data.  Det är bra att vänta med att ansluta till en händelsekälla till TSD förrän du har din referensdata på plats, särskilt om den händelsekällan har data i den. Du kan också vänta med att skicka data till den händelsekällan tills referensdatauppsättningen är på plats.

För att hantera referensdata, finns det webbaserade användargränssnittet i TSI-Utforskaren och det finns en programmatisk C#-API. TSI-Utforskaren har ett visual användarmiljö att ladda upp filer eller klistra in befintliga referens datauppsättningar som JSON- eller CSV-format. Med API: et, kan du skapa en anpassad app när det behövs.

Mer information om hur du hanterar referensdata i Time Series Insights finns i den [data referensartikeln](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Skapa händelsekälla för Time Series Insights
1. Om du inte har skapat en händelsekälla följer du [dessa instruktioner](time-series-insights-how-to-add-an-event-source-eventhub.md) för att skapa en händelsekälla.

2. Ange **deviceTimestamp** som egenskapsnamn för tidsstämpeln – den här egenskapen används som den faktiska tidsstämpeln i C#-exemplet. Egenskapsnamnet för tidsstämpeln är skiftlägeskänsligt och värdena måste ha formatet __åååå-MM-ddTHH:mm:ss.FFFFFFFK__ när de skickas som JSON till Event Hub. Om egenskapen inte finns i händelsen används den tid då händelsen stod i Event Hub-kön.

  ![Skapa händelsekälla](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Exempelkod för push-händelser
1. Gå till event hub-principen med namnet **MySendPolicy**. Kopiera den **anslutningssträngen** med principnyckeln.

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
#### <a name="output---one-event"></a>Resultat – en händelse

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
#### <a name="output---two-events"></a>Resultat – två händelser

|id|tidsstämpel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Exempel 3

#### <a name="input"></a>Indata

En JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt:
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
#### <a name="output---two-events"></a>Resultat – två händelser
Observera att egenskapen ”plats” kopieras till varje händelse.

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
#### <a name="output---two-events"></a>Resultat – två händelser

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|



## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Visa din miljö i Time Series Insights explorer](https://insights.timeseries.azure.com).
