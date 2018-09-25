---
title: Övervaka hälsotillståndet för Azure IoT Hub | Microsoft Docs
description: Använda Azure Monitor och Azure Resource Health för att övervaka din IoT Hub och diagnostisera problem snabbt
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: kgremban
ms.openlocfilehash: d3c32c2258f7542a02549fbc531aa9e8293d0235
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996306"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt

Företag som implementerar Azure IoT Hub förväntar sig pålitlig prestanda från sina resurser. För att hjälpa dig att upprätthålla en Stäng bevakning på din verksamhet, IoT Hub är helt integrerat med [Azure Monitor] [ lnk-AM] och [Azure Resource Health] [ lnk-ARH]. De här två tjänsterna fungerar tillsammans att förse dig med de data du behöver för att hålla din IoT-lösningar som körs i ett felfritt tillstånd. 

Azure Monitor är en enda källa för övervakning och loggning för alla dina Azure-tjänster. Du kan skicka diagnostikloggar som Azure Monitor genererar till Log Analytics, Event Hubs eller Azure Storage för anpassad bearbetning. Azure Monitor-mått och diagnostik för inställningarna ger dig insyn i prestanda för dina resurser. Fortsätt att läsa den här artikeln om du vill lära dig hur du [Använd Azure Monitor](#use-azure-monitor) med IoT-hubben. 

> [!IMPORTANT]
> De händelser som genereras av IoT Hub-tjänsten med hjälp av Azure Monitor-diagnostikloggar är inte garanterat tillförlitliga och ordnade. Vissa händelser kan tappas bort eller levereras i ordning. Diagnostikloggar också är inte avsedda att vara i realtid och det kan ta flera minuter innan händelser som loggas ditt val av mål.

Azure Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. En anpassad instrumentpanel ger status för aktuella och tidigare tillstånd för din IoT-hubbar. Fortsätt att läsa den här artikeln om du vill lära dig hur du [Använd Azure Resource Health](#use-azure-resource-health) med IoT-hubben. 

Förutom att integrera med de här två tjänsterna, dessutom IoT Hub eget mått som du kan använda för att förstå tillståndet för dina IoT-resurser. Mer information finns i [förstå IoT Hub mått][lnk-metrics].

## <a name="use-azure-monitor"></a>Använda Azure Monitor

Azure Monitor innehåller resursnivå diagnostikinformation, vilket innebär att du kan övervaka åtgärder som äger rum i din IoT-hubb. 

Azure Monitor diagnostik inställningar ersätter övervaka för IoT Hub-åtgärder. Om du använder åtgärdsövervakning, bör du migrera dina arbetsflöden. Mer information finns i [migrera från åtgärder som övervakar Diagnostics inställningar][lnk-migrate].

Mer information om specifika mått och händelser som Azure Monitor bevakar finns [stöds mått med Azure Monitor] [ lnk-AM-metrics] och [tjänster, scheman och kategorier som stöds för Azure Diagnostikloggar][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Förstå loggar

Azure Monitor övervakar olika åtgärder som inträffar i IoT Hub. Varje kategori har ett schema som definierar hur händelser i den kategorin rapporteras. 

#### <a name="connections"></a>Anslutningar

Anslutningar kategorin spårar enheten ansluta och koppla bort händelser från en IoT-hubb, samt fel. Det är användbart för att identifiera obehöriga anslutningsförsök och för att spåra när anslutningen kopplas från för enheter i områden med dålig anslutning att spåra den här kategorin.

> [!NOTE]
> Tillförlitlig anslutningsstatus för enheter Kontrollera [enheten pulsslag][lnk-devguide-heartbeat].

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Moln till enhet-kommandon

Moln till enhet kommandon kategorin spårar fel som inträffar på IoT-hubben och som är relaterade till moln till enhet meddelandepipeline. Den här kategorin innehåller fel som uppstår när du skickar meddelanden från molnet till enheten (till exempel obehöriga avsändare), ta emot meddelanden från molnet till enheten (till exempel leveransantalet har överskridits) och mottagande moln till enhet-meddelande (t ex feedback har gått ut). Den här kategorin fånga inte fel från en enhet som hanterar ett moln-till-enhet-meddelande felaktigt om moln till enhet meddelandet levererades har.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>Identitet åtgärder

Enhetskategorin identitet operations spårar fel som uppstår vid försök att skapa, uppdatera eller ta bort en post i din IoT-hubbens identitetsregister. Spårning av den här kategorin är användbart för att etablera scenarier.

```json
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
```

#### <a name="routes"></a>Vägar

Meddelandet routning kategorin spårar fel som inträffar när meddelandet vägen utvärdering och slutpunktshälsa anser vara av IoT Hub. Den här kategorin innehåller händelser, som när en regel som utvärderas till ”odefinierad”, när IoT Hub markerar en slutpunkt som dead och eventuella andra fel togs emot från en slutpunkt. Den här kategorin omfattar inte specifika fel om själva meddelandena (till exempel enhet begränsningsfel) som har rapporterats under kategorin ”enhetstelemetri”.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Enhetstelemetri

Telemetri enhetskategorin spårar fel som inträffar på IoT-hubben och som är relaterade till pipelinen telemetri. Den här kategorin innehåller fel som uppstår när du skickar telemetrihändelser (t.ex begränsningar) och ta emot händelser (till exempel obehöriga läsare). Den här kategorin identifierar inte fel som orsakats av kod som körs på själva enheten.

```json
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
```

#### <a name="file-upload-operations"></a>Filöverföringsåtgärder

Filen uppladdning kategorin spårar fel som inträffar på IoT-hubben och som är relaterade till filuppladdning. Den här kategorin omfattar:

* Fel som inträffar med SAS-URI, t.ex när den upphör att gälla innan en enhet meddelar hubb för en överförda.
* Det gick inte överföringar som rapporteras av enheten.
* Fel som uppstår när en fil inte hittas i storage när IoT Hub-meddelande meddelande skapas.

Den här kategorin identifierar inte fel som sker under tiden enheten laddar upp en fil till lagring.

```json
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
```

#### <a name="cloud-to-device-twin-operations"></a>Moln till enhet twin åtgärder

Moln till enhet twin åtgärdskategori spårar tjänstinitierade händelser på enhetstvillingar. De här åtgärderna kan inkludera get twin, uppdatera eller ersätta taggar, och uppdatera eller ersätta önskade egenskaper. 

```json
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
```

#### <a name="device-to-cloud-twin-operations"></a>Enhet-till-moln-twin-åtgärder

Enhet-till-moln twin åtgärdskategori spårar enhet-initierad händelser på enhetstvillingar. De här åtgärderna kan inkludera get twin, uppdatera rapporterade egenskaper och prenumerera på önskade egenskaper.

```json
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
```

#### <a name="twin-queries"></a>Twin frågor

Kategorin twin frågor rapporterar om frågebegäranden efter enhetstvillingar som startas i molnet. 

```json
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
```

#### <a name="jobs-operations"></a>Jobbåtgärder

Jobb åtgärdskategori rapporterar om jobbförfrågningar uppdatera enhetstvillingar eller anropa direktmetoder på flera enheter. Dessa begäranden initieras i molnet. 

```json
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
```

#### <a name="direct-methods"></a>Direkta metoder

Direkta metoder kategorin spårar begäranden och svar-interaktioner som skickas till enskilda enheter. Dessa begäranden initieras i molnet. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Läs loggar från Azure Event Hubs

Du kan skapa program som läser in loggarna så att du kan vidta åtgärder baserat på informationen i dem när du har konfigurerat händelseloggning via diagnostikinställningar. Den här exempelkoden hämtar loggar från en händelsehubb:

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
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Använda Azure Resource Health

Använd Azure Resource Health för att övervaka om IoT-hubben är igång. Du kan också lära dig om ett regionalt strömavbrott påverkar hälsotillståndet för din IoT-hubb. För att förstå specifik information om hälsotillståndet för Azure IoT Hub, rekommenderar vi att du [Använd Azure Monitor](#use-azure-monitor). 

Azure IoT Hub anger hälsotillstånd på regional nivå. Om det inte ett regionalt strömavbrott påverkar din IoT-hubb, hälsostatus visas som **okänd**. Läs mer om de specifika hälsokontroller av slutpunkter som utför Azure Resource Health i [resurstyper och hälsokontroller i Azure resource health][lnk-ARH-checks].

Följ dessa steg för att kontrollera hälsotillståndet för din IoT-hubbar:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till **Tjänstehälsa** > **resurshälsa**.
1. I listrutorna väljer du din prenumeration och **IoT Hub**.

Läs mer om hur du tolkar hälsodata i [översikt över hälsotillståndet för Azure-resurs][lnk-ARH]

## <a name="next-steps"></a>Nästa steg

- [Förstå IoT Hub-mått][lnk-metrics]
- [IoT fjärrövervakning och aviseringar med Azure Logic Apps ansluter dina IoT-hubb och postlåda][lnk-monitoring-notifications]


[lnk-AM]: ../azure-monitor/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-devguide-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
