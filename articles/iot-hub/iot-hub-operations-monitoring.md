---
title: "Åtgärder i Azure IoT-hubb övervakning | Microsoft Docs"
description: "Hur du använder Azure IoT Hub operations övervakning för att övervaka status för åtgärder för din IoT-hubb i realtid."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: nberdy
ms.openlocfilehash: db03cfdd486a3172b258379928fac12cc0af730a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="iot-hub-operations-monitoring"></a>IoT-hubb operations övervakning

IoT-hubb operations övervakning kan du övervaka status för åtgärder för din IoT-hubb i realtid. IoT-hubb spårar händelser över flera kategorier av åtgärder. Du kan välja att skicka händelser från en eller flera kategorier för en slutpunkt av din IoT-hubb för bearbetning. Du kan övervaka data för fel eller ställa in mer komplexa bearbetning baserat på datamönster.

>[!NOTE]
>IoT-hubb operations övervakning är föråldrad och kommer att tas bort från IoT-hubb i framtiden. Övervaka åtgärder och hälsotillståndet för IoT-hubb finns [övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt][lnk-monitor]. Läs mer om utfasning tidslinjen [övervaka dina Azure IoT-lösningar med Azure-Monitor och Azure Resource Health][lnk-blog-announcement].

IoT-hubb övervakar sex kategorier av händelser:

* Enhetens identitet åtgärder
* Enhetstelemetrin
* Meddelanden moln till enhet
* Anslutningar
* Filöverföringar
* Meddelanderedigering

> [!IMPORTANT]
> IoT-hubb operations övervakning garanterar inte tillförlitligt och sorterad leverans av händelser. Beroende på IoT-hubb underliggande infrastruktur, kan vissa händelser tappas bort eller levereras i ordning. Använda operations övervakning för att generera varningar baserat på fel signalerar till exempel misslyckade anslutningsförsök eller hög frekvens frånkoppling för specifika enheter. Du bör inte lita på operations händelseövervakning om du vill skapa en konsekvent store för enhetens tillstånd, t.ex. en butik spårning ansluten eller frånkopplad tillståndet för en enhet. 

## <a name="how-to-enable-operations-monitoring"></a>Så här aktiverar du operations övervakning

1. Skapa en IoT-hubb. Du hittar anvisningar om hur du skapar en IoT-hubb i den [Kom igång] [ lnk-get-started] guide.

1. Öppna bladet för din IoT-hubb. Därifrån klickar du på **Operations övervakning**.

    ![Åtkomståtgärder övervakning konfigurationen för portalen][1]

1. Välj övervakning kategorier som du vill övervaka och klicka sedan på **spara**. Händelserna som är tillgängliga för att läsa från Event Hub-kompatibel slutpunkten som anges i **övervakningsinställningarna**. IoT-hubb-slutpunkt anropas `messages/operationsmonitoringevents`.

    ![Konfigurera operations övervakning på din IoT-hubb][2]

> [!NOTE]
> Att välja **utförlig** övervakning för den **anslutningar** kategori orsakar IoT-hubb för att generera diagnostikmeddelanden för ytterligare. För alla kategorier, den **utförlig** inställningsändringar mängd information IoT-hubb ingår i varje felmeddelande.

## <a name="event-categories-and-how-to-use-them"></a>Kategorier och hur du använder dem.

Varje operations övervakning kategorin spårar en annan typ av interaktion med IoT-hubb och varje övervakning kategori har ett schema som definierar hur händelser i den kategorin är strukturerad.

### <a name="device-identity-operations"></a>Enhetens identitet åtgärder

Enhetskategori identitet operations spårar fel som uppstår vid försök att skapa, uppdatera eller ta bort en post i registret för din IoT-hubb identitet. Spårning av den här kategorin är användbart för att etablera scenarier.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Enhetstelemetrin

Telemetri enhetskategori spårar fel som uppstår vid IoT-hubb och är relaterade till telemetri pipeline. Den här kategorin innehåller fel som uppstår när du skickar telemetriska händelser (till exempel begränsning) och ta emot telemetriska händelser (till exempel obehöriga läsare). Den här kategorin kan inte fånga fel som orsakats av kod som körs på själva enheten.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Moln till enhet kommandon

Moln till enhet kommandon kategorin spårar fel som uppstår vid IoT-hubb och är relaterade till moln-till-enhetsmeddelande pipeline. Den här kategorin innehåller fel som uppstår när meddelanden moln till enhet (till exempel obehöriga avsändaren), ta emot meddelanden moln till enhet (till exempel leverans för många) och ta emot meddelandet moln till enhet feedback (t ex feedback har upphört att gälla). Den här kategorin fånga inte fel från en enhet som hanterar ett moln till enhet meddelande felaktigt om moln till enhet meddelandet levererades har.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Anslutningar

Anslutningar kategorin spårar fel som uppstår när enheter ansluta eller koppla från en IoT-hubb. Spårning av den här kategorin är användbar för att identifiera obehöriga anslutningsförsök och för att spåra när anslutningen bryts för enheter i områden i dålig anslutning.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Filöverföringar

Filen överför kategorin spårar fel som uppstår vid IoT-hubb och är relaterade till funktioner för överföring av filer. Den här kategorin omfattar:

* Fel som inträffar med SAS-URI, till exempel när det upphör att gälla innan en enhet meddelar hubb för en överförda.
* Det gick inte överföringar som rapporteras av enheten.
* Fel som uppstår när en fil inte hittas i lagringen under skapande av IoT-hubb notification meddelandet.

Den här kategorin kan inte fånga fel som uppstår direkt medan enheten är Överför en fil till lagring.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Meddelanderedigering

Meddelandet routning kategorin spårar fel som inträffar när meddelandet väg utvärdering och slutpunkten hälsa som uppfattas av IoT-hubb. Den här kategorin innefattar händelser, t.ex. när en regel som utvärderar till ”undefined”, när IoT-hubb markerar en slutpunkt som förlorade och andra fel togs emot från en slutpunkt. Den här kategorin omfattar inte felen om själva meddelandena (till exempel enhet begränsning fel) som har rapporterats under kategorin ”enhetstelemetrin”.

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="view-events"></a>Visa händelser

Du kan använda den *iothub explorer* verktyg för att snabbt testa att din IoT-hubb genererar övervakning händelser. Om du vill installera verktyget, se anvisningarna i den [iothub explorer] [ lnk-iothub-explorer] GitHub-lagringsplatsen.

1. Kontrollera att den **anslutningar** övervakning kategori är inställd på **utförlig** i portalen.

1. Kör följande kommando för att läsa från övervakning slutpunkten i en kommandotolk:

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. Kör följande kommando för att simulera en enhet som skickar meddelanden från enhet till moln i en annan kommandotolk:

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. Första Kommandotolken visar händelser som övervakning som den simulerade enheten ansluter till din IoT-hubb.

## <a name="connect-to-the-monitoring-endpoint"></a>Ansluta till övervakning slutpunkt

Övervakning slutpunkten på din IoT-hubb är Event Hub-kompatibel slutpunkt. Du kan använda någon mekanism som fungerar med Händelsehubbar att läsa övervakning meddelanden från den här slutpunkten. I följande exempel skapar en grundläggande läsare som inte lämpar sig för en distribution med hög genomströmning. Mer information om hur du bearbetar meddelanden från Event Hubs finns i självstudiekursen [Komma igång med Event Hubs][lnk-eventhubs-tutorial].

För att ansluta till slutpunkten för övervakning, behöver du en anslutningssträng och namnet på slutpunkten. Följande steg visar hur du hittar de nödvändiga värdena i portalen:

1. Gå till resursbladet för din IoT-hubb i portalen.

1. Välj **Operations övervakning**, och anteckna den **Event Hub-kompatibelt namn** och **Event Hub-kompatibel endpoint** värden:

    ![Event Hub-kompatibel endpoint värden][img-endpoints]

1. Välj **principer för delad åtkomst**, Välj **tjänsten**. Anteckna den **primärnyckel** värde:

    ![Primär nyckel för tjänsten delad åtkomst-princip][img-service-key]

Följande C# kodexempel hämtas från Visual Studio **klassiska Windows-skrivbordet** C#-konsolapp. Projektet har den **WindowsAzure.ServiceBus** NuGet-paketet installeras.

* Ersätt platshållaren för anslutningssträngen med en anslutningssträng som använder den **Event Hub-kompatibel endpoint** och tjänsten **primärnyckel** värden som du antecknade tidigare som visas i följande exempel:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Ersätt platshållaren namn övervakning slutpunkten för med den **Event Hub-kompatibelt namn** värdet som du antecknade tidigare.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
