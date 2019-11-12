---
title: Övervaka hälso tillståndet för Azure-IoT Hub | Microsoft Docs
description: Använd Azure Monitor och Azure Resource Health för att övervaka dina IoT Hub och diagnostisera problem snabbt
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929695"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Övervaka hälso tillståndet för Azure IoT Hub och diagnostisera problem snabbt

Företag som implementerar Azure IoT Hub förväntar sig pålitliga prestanda från sina resurser. IoT Hub är helt integrerat med [Azure Monitor](../azure-monitor/index.yml) och [Azure Resource Health](../service-health/resource-health-overview.md)för att hjälpa dig att upprätthålla en nära Watch-åtgärd. De här två tjänsterna arbetar för att förse dig med de data du behöver för att hålla dina IoT-lösningar igång i felfritt tillstånd.

Azure Monitor är en enda källa för övervakning och loggning för alla dina Azure-tjänster. Du kan skicka diagnostikloggar som Azure Monitor genererar till Azure Monitor loggar, Event Hubs eller Azure Storage för anpassad bearbetning. Azure Monitorens mått och diagnostikinställningar ger dig insyn i prestandan för dina resurser. Fortsätt att läsa den här artikeln för att lära dig hur du [använder Azure Monitor](#use-azure-monitor) med IoT Hub. 

> [!IMPORTANT]
> De händelser som genereras av tjänsten IoT Hub som använder Azure Monitor diagnostikloggar är inte garanterat tillförlitliga eller beställda. Vissa händelser kan gå förlorade eller levereras i rätt ordning. Diagnostikloggar är inte avsedda att vara i real tid och det kan ta flera minuter innan händelserna loggas till ditt val av mål.

Azure Resource Health hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina resurser. En instrument panel tillhandahåller aktuell och tidigare hälso status för varje IoT-hubb. Fortsätt till avsnittet längst ned i den här artikeln om du vill lära dig hur du [använder Azure Resource Health](#use-azure-resource-health) med din IoT Hub. 

IoT Hub tillhandahåller också egna mått som du kan använda för att förstå status för dina IoT-resurser. Mer information finns i [förstå IoT Hub mått](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Använda Azure Monitor

Azure Monitor innehåller diagnostikinformation för Azure-resurser, vilket innebär att du kan övervaka åtgärder som sker i din IoT-hubb.

Azure Monitorens diagnostikinställningar ersätter IoT Hubs åtgärds övervakaren. Om du för närvarande använder åtgärds övervakning bör du migrera dina arbets flöden. Mer information finns i [Migrera från Operations Monitoring to Diagnostic Settings](iot-hub-migrate-to-diagnostics-settings.md).

Om du vill veta mer om vilka mått och händelser som Azure Monitor bevaka, se [mått som stöds med Azure Monitor](../azure-monitor/platform/metrics-supported.md) och [tjänster som stöds, scheman och kategorier för Azure Diagnostic-loggar](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Förstå loggarna

Azure Monitor spårar olika åtgärder som inträffar i IoT Hub. Varje kategori har ett schema som definierar hur händelser i den kategorin rapporteras.

#### <a name="connections"></a>Anslutningar

Kategorin anslutningar spårar enhets anslutning och från kopplings händelser från en IoT-hubb och fel. Den här kategorin är användbar för att identifiera otillåtna anslutnings försök och aviseringar när du förlorar anslutningen till enheter.

> [!NOTE]
> För tillförlitlig anslutnings status för enheter kontrol lera [enhetens pulsslag](iot-hub-devguide-identity-registry.md#device-heartbeat).

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

#### <a name="cloud-to-device-commands"></a>Kommandon från moln till enhet

Kommando kategorin för moln-till-enhet spårar fel som uppstår på IoT-hubben och är relaterade till pipeline för meddelande från moln till enhet. Den här kategorin innehåller fel som inträffar från:

* Skicka meddelanden från moln till enhet (t. ex. otillåtna avsändar fel)
* Ta emot meddelanden från moln till enhet (t. ex. antal leveranser som har överskridits) och
* Får feedback från moln till enhet (t. ex. återkopplings fel).

Den här kategorin fångar inte fel när meddelanden från molnet till enheten levereras korrekt, men hanteras sedan felaktigt av enheten.

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

#### <a name="device-identity-operations"></a>Enhets identitets åtgärder

Kategorin enhets identitets åtgärder spårar fel som inträffar när du försöker skapa, uppdatera eller ta bort en post i din IoT Hubs identitets register. Att spåra den här kategorin är användbart för etablerings scenarier.

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

Kategorin meddelande cirkulation spårar fel som inträffar under utvärderingen av meddelande vägar och slut punkts hälsa som uppfattas av IoT Hub. Den här kategorin omfattar händelser som:

* En regel utvärderas till "odefinierad",
* IoT Hub markerar en slut punkt som död eller
* Eventuella fel som tagits emot från en slut punkt. 

Den här kategorin innehåller inte vissa fel meddelanden om själva meddelandena (t. ex. fel i enhets begränsning), som rapporteras i kategorin "enhets telemetri".

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

#### <a name="device-telemetry"></a>Telemetri för enhet

Kategorin hets telemetri spårar fel som inträffar på IoT-hubben och är relaterade till pipelinen för telemetri. Den här kategorin innehåller fel som uppstår när du skickar telemetri-händelser (till exempel begränsning) och tar emot telemetri-händelser (till exempel obehörig läsare). Den här kategorin kan inte fånga fel som orsakas av kod som körs på själva enheten.

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

Kategorin fil uppladdning spårar fel som uppstår på IoT-hubben och är relaterade till fil uppladdnings funktionen. Den här kategorin omfattar:

* Fel som uppstår med SAS-URI: n, till exempel när den upphör att gälla innan en enhet meddelar navet om en slutförd uppladdning.

* Misslyckade uppladdningar som rapporter ATS av enheten.

* Fel som inträffar när det inte går att hitta en fil i lagringen under IoT Hub meddelande om att meddelanden skapas.

Den här kategorin kan inte fånga fel som sker direkt när enheten laddar upp en fil till lagringen.

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

#### <a name="cloud-to-device-twin-operations"></a>Dubbla åtgärder från moln till enhet

Den dubbla drift kategorin från moln till enhet spårar händelser som initierats av tjänsten på enheten. Dessa åtgärder kan omfatta etiketter för att hämta, uppdatera eller ersätta och uppdatera eller ersätta önskade egenskaper.

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

#### <a name="device-to-cloud-twin-operations"></a>Dubbla åtgärder från enhet till moln

Kategorin enhets-till-moln-dubbla åtgärder spårar enheternas initierade händelser på enheten. De här åtgärderna kan vara att hämta rapporter med dubbla, uppdatera rapporterade egenskaper och prenumerera på önskade egenskaper.

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

Kategorin dubbla frågor rapporterar om förfrågningar om enhets grupper som initieras i molnet.

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

Jobbets åtgärds kategori rapporterar om jobb begär Anden för att uppdatera enheten är dubbla eller anropar direkta metoder på flera enheter. Dessa förfrågningar initieras i molnet.

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

Kategorin direkta metoder spårar interaktioner för begäran och svar som skickas till enskilda enheter. Dessa förfrågningar initieras i molnet.

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

#### <a name="distributed-tracing-preview"></a>Distribuerad spårning (för hands version)

Den distribuerade spårnings kategorin spårar korrelations-ID: n för meddelanden som har spårnings kontext rubriken. Om du vill aktivera dessa loggar fullständigt måste kod på klient sidan uppdateras genom att följa [analys och diagnostisera IoT-program från slut punkt till slut punkt med IoT Hub Distributed tracing (för hands version)](iot-hub-distributed-tracing.md).

Observera att `correlationId` överensstämmer med [spårnings kontext](https://github.com/w3c/trace-context) förslaget för W3C, där det innehåller en `trace-id` och en `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C-loggar (från enhet till moln)

IoT Hub registrerar loggen när ett meddelande som innehåller giltiga spårnings egenskaper kommer till IoT Hub.

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

Här beräknas `durationMs` inte eftersom IoT Hub klockan inte är synkroniserad med enhets klockan, och därför kan en varaktighets beräkning vara missvisande. Vi rekommenderar att du skriver logik med hjälp av tidsstämplar i avsnittet `properties` för att samla in toppar i svar från enhet till moln.

| Egenskap | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Storleken på meddelanden från enhet till moln i byte |
| **deviceId** | Sträng med ASCII 7-bitars alfanumeriska tecken | Enhetens identitet |
| **callerLocalTimeUtc** | UTC-tidsstämpel | Skapande tid för meddelandet som rapporteras av lokal enhets klocka |
| **calleeLocalTimeUtc** | UTC-tidsstämpel | Tiden för meddelande ankomsten vid IoT Hubens gateway som rapporteras av IoT Hub klockan på tjänst Sidan |

##### <a name="iot-hub-ingress-logs"></a>IoT Hub ingress-loggar

IoT Hub registrerar loggen när ett meddelande som innehåller giltiga spårnings egenskaper skriver till intern eller inbyggd Händelsehubben.

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

I avsnittet `properties` innehåller loggen ytterligare information om meddelande ingångar.

| Egenskap | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Sträng | Antingen sant eller falskt anger om meddelanderoutning är aktiverat i IoT Hub |
| **parentSpanId** | Sträng | Det överordnade meddelandets [span-ID](https://w3c.github.io/trace-context/#parent-id) , som skulle vara D2C meddelande spårning i det här fallet |

##### <a name="iot-hub-egress-logs"></a>IoT Hub utgående loggar

IoT Hub registrerar loggen när [routning](iot-hub-devguide-messages-d2c.md) är aktiverat och meddelandet skrivs till en [slut punkt](iot-hub-devguide-endpoints.md). Om routning inte är aktiverat registrerar IoT Hub inte den här loggen.

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

I avsnittet `properties` innehåller loggen ytterligare information om meddelande ingångar.

| Egenskap | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | Sträng | Namnet på dirigerings slut punkten |
| **endpointType** | Sträng | Typ av Dirigerings slut punkt |
| **parentSpanId** | Sträng | [Intervall-ID: t](https://w3c.github.io/trace-context/#parent-id) för det överordnade meddelandet, som skulle vara IoT Hub ingresss meddelande spårning i det här fallet |

#### <a name="configurations"></a>Konfigurationer

IoT Hub konfigurations loggar spårar händelser och fel för funktionen automatisk enhets hantering.

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

### <a name="device-streams-preview"></a>Enhets strömmar (förhands granskning)

Kategorin enhets strömmar spårar interaktioner för begär ande svar som skickas till enskilda enheter.

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

### <a name="read-logs-from-azure-event-hubs"></a>Läs loggar från Azure Event Hubs

När du har konfigurerat händelse loggning via diagnostikinställningar kan du skapa program som läser loggarna så att du kan vidta åtgärder baserat på informationen i dem. Den här exempel koden hämtar loggar från en Event Hub:

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

Använd Azure Resource Health för att övervaka om din IoT Hub är igång. Du kan också lära dig om ett regionalt avbrott påverkar hälsan för din IoT Hub. Om du vill veta mer om hälso tillståndet för Azure-IoT Hub rekommenderar vi att du [använder Azure Monitor](#use-azure-monitor).

Azure IoT Hub indikerar hälsa på regional nivå. Om ett regionalt avbrott påverkar din IoT Hub visas hälso statusen som **okänd**. Mer information finns i [resurs typer och hälso kontroller i Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

Följ dessa steg om du vill kontrol lera hälso tillståndet för dina IoT-hubbar:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Navigera till **Service Health** > **resurs hälsa**.

3. Välj din prenumeration i list rutan och välj **IoT Hub** som resurs typ.

Mer information om hur du tolkar hälso data finns i [Översikt över Azure Resource Health](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Förstå IoT Hub mått](iot-hub-metrics.md)
* [Övervakning och aviseringar för IoT-fjärrhantering med Azure Logic Apps du ansluter din IoT Hub och post lådan](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
