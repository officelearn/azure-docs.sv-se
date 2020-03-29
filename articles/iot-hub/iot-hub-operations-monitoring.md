---
title: Övervakning av Azure IoT Hub-åtgärder (inaktuell) | Microsoft-dokument
description: Så här använder du Övervakning av Azure IoT Hub-åtgärder för att övervaka status för åtgärder på din IoT-hubb i realtid.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.openlocfilehash: 84f28a1cb411e7df156fc08fa683efe7f83eda64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60345744"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Övervakning av IoT Hub-operationer (föråldrad)

Med övervakning av IoT Hub-åtgärder kan du övervaka statusen för åtgärder på din IoT-hubb i realtid. IoT Hub spårar händelser över flera kategorier av åtgärder. Du kan välja att skicka händelser från en eller flera kategorier till en slutpunkt i din IoT-hubb för bearbetning. Du kan övervaka data för fel eller ställa in mer komplex bearbetning baserat på datamönster.

>[!NOTE]
>Övervakningen av IoT **Hub-operationer är föråldrad och har tagits bort från IoT Hub den 10 mars 2019**. Information om hur du övervakar IoT Hubs åtgärder och hälsotillstånd finns snabbt [i Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem](iot-hub-monitor-resource-health.md). Mer information om tidslinjen för utfasning finns i [Övervaka dina Azure IoT-lösningar med Azure Monitor och Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

IoT Hub övervakar sex kategorier av händelser:

* Åtgärder för enhetsidentitet
* Enhetstelemetri
* Meddelanden från molnet till enheten
* Anslutningar
* Filuppladdningar
* Meddelanderoutning

> [!IMPORTANT]
> Övervakning av IoT Hub-operationer garanterar inte tillförlitlig eller ordnad leverans av händelser. Beroende på IoT Hub-underliggande infrastruktur kan vissa händelser gå förlorade eller levereras i oordning. Använd åtgärder övervakning för att generera aviseringar baserat på felsignaler såsom misslyckade anslutningsförsök, eller högfrekventa frånkopplingar för specifika enheter. Du bör inte förlita dig på åtgärder som övervakar händelser för att skapa ett konsekvent arkiv för enhetstillstånd, t.ex. 

## <a name="how-to-enable-operations-monitoring"></a>Så här aktiverar du övervakning av operationer

1. Skapa en IoT-hubb. Du hittar instruktioner om hur du skapar en IoT-hubb i [guiden Kom igång.](quickstart-send-telemetry-dotnet.md)

2. Öppna bladet på din IoT-hubb. Därifrån klickar du på **Operationsövervakning**.

    ![Konfiguration av åtkomståtgärder övervakning i portalen](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Välj de övervakningskategorier som du vill övervaka och klicka sedan på **Spara**. Händelserna är tillgängliga för läsning från den eventnavet-kompatibla slutpunkt som anges i **övervakningsinställningarna**. Slutpunkten för IoT `messages/operationsmonitoringevents`Hub kallas .

    ![Konfigurera verksamhetsövervakning på din IoT-hubb](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Om du väljer **Utförlig** övervakning för kategorin **Anslutningar** kan IoT Hub generera ytterligare diagnostikmeddelanden. För alla andra kategorier ändrar inställningen **Utförlig** mängd mängden information som IoT Hub innehåller i varje felmeddelande.

## <a name="event-categories-and-how-to-use-them"></a>Händelsekategorier och hur du använder dem

Varje operationsövervakningskategori spårar en annan typ av interaktion med IoT Hub, och varje övervakningskategori har ett schema som definierar hur händelser i den kategorin är strukturerade.

### <a name="device-identity-operations"></a>Åtgärder för enhetsidentitet

Kategorin enhetsidentitetsåtgärder spårar fel som uppstår när du försöker skapa, uppdatera eller ta bort en post i IoT-hubbens identitetsregister. Det är användbart att spåra den här kategorin för etablering av scenarier.

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

### <a name="device-telemetry"></a>Enhetstelemetri

Enhetstelemetrikategorin spårar fel som uppstår vid IoT-hubben och är relaterade till telemetripipelinen. Den här kategorin innehåller fel som uppstår när telemetrihändelser skickas (t.ex. begränsning) och mottagning av telemetrihändelser (till exempel obehörig läsare). Den här kategorin kan inte fånga fel som orsakas av kod som körs på själva enheten.

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

### <a name="cloud-to-device-commands"></a>Kommandon mellan molnet och enheten

Kategorin moln-till-enhet-kommandon spårar fel som uppstår vid IoT-hubben och är relaterade till meddelandepipelinen mellan molnet och enheten. Den här kategorin innehåller fel som uppstår när meddelanden från molnet till enheten skickas från molnet till enheten (till exempel obehörig avsändare), ta emot meddelanden från molnet till enheten (t.ex. överskridet leveransantal) och ta emot feedback från molnet till enheten (till exempel feedback som har upphört att gälla). Den här kategorin fångar inte fel från en enhet som felaktigt hanterar ett meddelande från molnet till enheten om meddelandet från molnet till enheten levererades.

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

Kategorin Anslutningar spårar fel som uppstår när enheter ansluter eller kopplar från en IoT-hubb. Det är användbart att spåra den här kategorin för att identifiera obehöriga anslutningsförsök och för att spåra när en anslutning går förlorad för enheter i områden med dålig anslutning.

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

### <a name="file-uploads"></a>Filuppladdningar

Filöverföringskategorin spårar fel som uppstår vid IoT-hubben och är relaterade till filöverföringsfunktioner. Denna kategori omfattar:

* Fel som uppstår med SAS URI, till exempel när den upphör att gälla innan en enhet meddelar navet för en slutförd överföring.

* Misslyckade uppladdningar som rapporterats av enheten.

* Fel som uppstår när en fil inte hittas i lagring under ioT Hub meddelande meddelande skapas.

Den här kategorin kan inte fånga fel som uppstår direkt när enheten överför en fil till lagring.

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

### <a name="message-routing"></a>Meddelanderoutning

Meddelanderoutningskategorin spårar fel som uppstår under utvärdering av meddelandevägar och slutpunktshälsa som uppfattas av IoT Hub. Den här kategorin innehåller händelser som när en regel utvärderas till "odefinierad", när IoT Hub markerar en slutpunkt som död och andra fel som tas emot från en slutpunkt. Den här kategorin innehåller inte specifika fel om själva meddelandena (till exempel enhetsbegränsningsfel), som rapporteras under kategorin "enhetstelemetri".

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

## <a name="connect-to-the-monitoring-endpoint"></a>Anslut till övervakningsslutpunkten

Övervakningsslutpunkten på din IoT-hubb är en slutpunkt som är kompatibel med händelsehubben. Du kan använda alla mekanismer som fungerar med händelsehubbar för att läsa övervakningsmeddelanden från den här slutpunkten. I följande exempel skapas en grundläggande läsare som inte är lämplig för en distribution med högt dataflöde. Mer information om hur du bearbetar meddelanden från Event Hubs finns i självstudiekursen [Komma igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

Om du vill ansluta till övervakningsslutpunkten behöver du en anslutningssträng och slutpunktsnamnet. Följande steg visar hur du hittar de nödvändiga värdena i portalen:

1. Navigera till resursbladet för IoT Hub i portalen.

2. Välj **Operationsövervakning**och anteckna de **händelsehubbkompatibla** **slutpunktsvärdena:**

    ![Slutpunktsvärden för händelsehubb](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Välj **Principer för delad åtkomst**och välj sedan **tjänst**. Anteckna värdet **för primärnyckeln:**

    ![Primärnyckel för tjänstens princip för delad åtkomst](./media/iot-hub-operations-monitoring/service-key.png)

Följande C#-kodexempel hämtas från en Visual Studio **Windows Classic Desktop** C#-konsolapp. Projektet har **WindowsAzure.ServiceBus** NuGet paketet installerat.

* Ersätt platshållaren för anslutningssträngen med en anslutningssträng som använder den **händelsehubbkompatibla slutpunkt** och tjänsten **Primärnyckelvärden** som du tidigare har noterat enligt följande exempel:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Ersätt platshållaren för övervakningsslutpunktsnamn med det **namnvärde som är kompatibelt med Händelsehubben** som du noterade tidigare.

```csharp
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

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)