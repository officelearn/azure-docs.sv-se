---
title: Övervaka hälsotillståndet för din Azure IoT Hub | Microsoft-dokument
description: Använd Azure Monitor och Azure Resource Health för att övervaka din IoT Hub och diagnostisera problem snabbt
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271088"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt

Företag som implementerar Azure IoT Hub förväntar sig tillförlitlig prestanda från sina resurser. För att hjälpa dig att hålla ett vakande öga på din verksamhet är IoT Hub helt integrerat med [Azure Monitor](../azure-monitor/index.yml) och Azure [Resource Health](../service-health/resource-health-overview.md). Dessa två tjänster arbetar för att ge dig de data du behöver för att hålla dina IoT-lösningar igång på ett felfritt sätt.

Azure Monitor är en enda källa till övervakning och loggning för alla dina Azure-tjänster. Du kan skicka de diagnostikloggar som Azure Monitor genererar till Azure Monitor-loggar, eventhubbar eller Azure Storage för anpassad bearbetning. Azure Monitors mått och diagnostikinställningar ger dig insyn i dina resursers prestanda. Fortsätt läsa den här artikeln om du vill lära dig hur [du använder Azure Monitor](#use-azure-monitor) med din IoT-hubb. 

> [!IMPORTANT]
> De händelser som avges av IoT Hub-tjänsten med hjälp av Diagnostikloggar för Azure Monitor är inte garanterade att vara tillförlitliga eller beställda. Vissa händelser kan gå förlorade eller levereras i oordning. Diagnostikloggar är inte heller avsedda att vara i realtid, och det kan ta flera minuter för händelser som ska loggas till ditt val av destination.

Azure Resource Health hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina resurser. En instrumentpanel ger aktuell och tidigare hälsostatus för var och en av dina IoT-hubbar. Fortsätt till avsnittet längst ned i den här artikeln om du vill lära dig hur du [använder Azure Resource Health](#use-azure-resource-health) med din IoT-hubb. 

IoT Hub innehåller också egna mått som du kan använda för att förstå tillståndet för dina IoT-resurser. Mer information finns i [Förstå IoT Hub-mått](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Använda Azure Monitor

Azure Monitor tillhandahåller diagnostikinformation för Azure-resurser, vilket innebär att du kan övervaka åtgärder som utförs inom din IoT-hubb.

Azure Monitors diagnostikinställningar ersätter IoT Hub-driftövervakaren. Om du för närvarande använder verksamhetsövervakning bör du migrera arbetsflödena. Mer information finns i [Migrera från verksamhetsövervakning till diagnostikinställningar](iot-hub-migrate-to-diagnostics-settings.md).

Mer information om specifika mått och händelser som Azure Monitor [bevakar](../azure-monitor/platform/metrics-supported.md) finns i Mått som stöds med Azure Monitor och tjänster som [stöds, scheman och kategorier för Azure Diagnostic Logs](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Förstå loggar

Azure Monitor spårar olika åtgärder som inträffar i IoT Hub. Varje kategori har ett schema som definierar hur händelser i den kategorin rapporteras.

#### <a name="connections"></a>Anslutningar

I kategorin anslutningar spårar enhetens anslutning och frånkopplade händelser från en IoT-hubb samt fel. Den här kategorin är användbar för att identifiera obehöriga anslutningsförsök och eller aviseringar när du förlorar anslutningen till enheter.

> [!NOTE]
> För tillförlitlig anslutningsstatus för enheter kontrollera [Device heartbeat](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Kommandon mellan molnet och enheten

Kategorin moln-till-enhet-kommandon spårar fel som uppstår vid IoT-hubben och är relaterade till meddelandepipelinen mellan molnet och enheten. Den här kategorin innehåller fel som uppstår från:

* Skicka meddelanden från molnet till enheten (t.o.d. obehöriga avsändarfel),
* Ta emot meddelanden från molnet till enheten (som leveransantal överskred fel) och
* Ta emot feedback från meddelanden från molnet till enheten (som feedback som gått ut).

Den här kategorin fångar inte fel när meddelandet från molnet till enheten levereras men sedan hanteras felaktigt av enheten.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Åtgärder för enhetsidentitet

Kategorin enhetsidentitetsåtgärder spårar fel som uppstår när du försöker skapa, uppdatera eller ta bort en post i IoT-hubbens identitetsregister. Det är användbart att spåra den här kategorin för etablering av scenarier.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Vägar

Meddelanderoutningskategorin spårar fel som uppstår under utvärdering av meddelandevägar och slutpunktshälsa som uppfattas av IoT Hub. Denna kategori innehåller händelser som:

* En regel utvärderas till "odefinierad",
* IoT Hub markerar en slutpunkt som död, eller
* Eventuella fel som tas emot från en slutpunkt. 

Den här kategorin innehåller inte specifika fel om själva meddelandena (som enhetsbegränsningsfel), som rapporteras under kategorin "enhetstelemetri".

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Enhetstelemetri

Enhetstelemetrikategorin spårar fel som uppstår vid IoT-hubben och är relaterade till telemetripipelinen. Den här kategorin innehåller fel som uppstår när telemetrihändelser skickas (t.ex. begränsning) och mottagning av telemetrihändelser (till exempel obehörig läsare). Den här kategorin kan inte fånga fel som orsakas av kod som körs på själva enheten.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Filöverföringsåtgärder

Filöverföringskategorin spårar fel som uppstår vid IoT-hubben och är relaterade till filöverföringsfunktioner. Denna kategori omfattar:

* Fel som uppstår med SAS URI, till exempel när den upphör att gälla innan en enhet meddelar navet för en slutförd överföring.

* Misslyckade uppladdningar som rapporterats av enheten.

* Fel som uppstår när en fil inte hittas i lagring under ioT Hub meddelande meddelande skapas.

Den här kategorin kan inte fånga fel som uppstår direkt när enheten överför en fil till lagring.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Dubbla operationer mellan molnet och enheten

Kategorin två åtgärder från molnet till enheten spårar tjänstinitierade händelser på enhetstvillingar. Dessa åtgärder kan omfatta hämta dubbla taggar, uppdatera eller ersätta taggar och uppdatera eller ersätta önskade egenskaper.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Dubbla åtgärder mellan enheter och moln

Kategorin två enheter till molnet spårar enhetsinitierade händelser på enhetstvillingar. Dessa åtgärder kan omfatta hämta dubbla, uppdatera rapporterade egenskaper och prenumerera på önskade egenskaper.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Dubbla frågor

Kategorin dubbla frågor rapporterar om frågebegäranden för enhetstvillingar som initieras i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Jobbåtgärder

Kategorin jobbåtgärder rapporterar om jobbbegäranden om att uppdatera enhetstvillingar eller anropa direkta metoder på flera enheter. Dessa begäranden initieras i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Direkta metoder

Kategorin direkta metoder spårar interaktioner mellan begäran och svar som skickas till enskilda enheter. Dessa begäranden initieras i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Distribuerad spårning (förhandsgranskning)

Kategorin distribuerad spårning spårar korrelations-ID:erna för meddelanden som bär spårningskontexthuvudet. Om du vill aktivera dessa loggar fullt ut måste kod på klientsidan uppdateras genom att följa [Analysera och diagnostisera IoT-program från slutpunkt till slutpunkt med distribuerad spårning av IoT Hub (förhandsversion).](iot-hub-distributed-tracing.md)

Observera `correlationId` att det överensstämmer med [W3C Trace](https://github.com/w3c/trace-context) Context-förslaget, där det innehåller en `trace-id` såväl som en `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C -loggar (enhet till moln)

IoT Hub registrerar den här loggen när ett meddelande som innehåller giltiga spårningsegenskaper anländer till IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Här `durationMs` beräknas inte eftersom IoT Hub klocka kanske inte är synkroniserad med enheten klockan, och därmed en varaktighet beräkning kan vara vilseledande. Vi rekommenderar att du skriver logik `properties` med hjälp av tidsstämplarna i avsnittet för att fånga toppar i svarstiden mellan enheter och moln.

| Egenskap | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **meddelandeSize** | Integer | Storleken på meddelande från enhet till moln i byte |
| **deviceId** | Sträng med 7-bitars alfanumeriska ASCII-tecken | Enhetens identitet |
| **anroparenLokalTimeUtc** | TIDSstämpel för UTC | Skapandetiden för meddelandet som rapporterats av enhetens lokala klocka |
| **calleeLocalTimeUtc** | TIDSstämpel för UTC | Tiden för meddelandeankomst till IoT Hub's gateway som rapporterats av IoT Hub-tjänstsidans klocka |

##### <a name="iot-hub-ingress-logs"></a>IoT Hub ingress loggar

IoT Hub registrerar den här loggen när meddelandet som innehåller giltiga spårningsegenskaper skriver till interna eller inbyggda Event Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

I `properties` avsnittet innehåller den här loggen ytterligare information om inkommande meddelanden.

| Egenskap | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **ärRoutingEnabled** | String | Antingen sant eller falskt anger om meddelanderoutning är aktiverad i IoT Hub |
| **parentSpanId** | String | [Span-id](https://w3c.github.io/trace-context/#parent-id) för det överordnade meddelandet, som skulle vara D2C-meddelandespårningen i det här fallet |

##### <a name="iot-hub-egress-logs"></a>IoT Hub utgående loggar

IoT Hub registrerar den här loggen när [routning](iot-hub-devguide-messages-d2c.md) är aktiverat och meddelandet skrivs till en [slutpunkt](iot-hub-devguide-endpoints.md). Om routning inte är aktiverat registrerar inte IoT Hub den här loggen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

I `properties` avsnittet innehåller den här loggen ytterligare information om inkommande meddelanden.

| Egenskap | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **slutpunktNamn** | String | Namnet på routningsslutpunkten |
| **slutpunktTyp** | String | Typ av routningslutpunkt |
| **parentSpanId** | String | Det [överordnade meddelandets span-id,](https://w3c.github.io/trace-context/#parent-id) som skulle vara ioT Hub-inkommande meddelandespårning i det här fallet |

#### <a name="configurations"></a>Konfigurationer

IoT Hub-konfigurationsloggar spårar händelser och fel för funktionsuppsättningen Automatisk enhetshantering.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Enhetsströmmar (förhandsgranskning)

Kategorin enhetsströmmar spårar interaktioner med begäran-svar som skickas till enskilda enheter.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Läsa loggar från Azure Event Hubs

När du har konfigurerat händelseloggning via diagnostikinställningar kan du skapa program som läser upp loggarna så att du kan vidta åtgärder baserat på informationen i dem. Den här exempelkoden hämtar loggar från en händelsehubb:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
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
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Använd Azure Resource Health

Använd Azure Resource Health för att övervaka om din IoT-hubb är igång. Du kan också ta reda på om ett regionalt avbrott påverkar hälsan för din IoT-hubb. För att förstå specifik information om hälsotillståndet för din Azure IoT Hub rekommenderar vi att du [använder Azure Monitor](#use-azure-monitor).

Azure IoT Hub anger hälsotillstånd på regional nivå. Om ett regionalt avbrott påverkar IoT-hubben visas hälsostatusen som **Okänd**. Mer information finns [i Resurstyper och hälsokontroller i Azure-resurshälsa](../service-health/resource-health-checks-resource-types.md).

Så här kontrollerar du hälsotillståndet för dina IoT-hubbar:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Navigera till **hälsa för tjänstens** > **hälsoresurs**.

3. Välj prenumeration i listrutorna och välj sedan **IoT Hub** som resurstyp.

Mer information om hur du tolkar hälsodata finns i [Översikt över Azure-resurshälsa](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Förstå IoT Hub-mått](iot-hub-metrics.md)
* [IoT fjärrövervakning och meddelanden med Azure Logic Apps som ansluter din IoT-hubb och postlåda](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
