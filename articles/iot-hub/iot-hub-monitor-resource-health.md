---
title: Övervaka hälsotillståndet hos Azure IoT Hub | Microsoft Docs
description: Använd Azure-Monitor och Azure Resource Health att övervaka din IoT-hubb och diagnostisera problem snabbt
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 39171f7d7a7b27ec54f67b592e184e90134a1a52
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850397"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt

Företag som implementerar Azure IoT Hub förvänta sig tillförlitlig prestanda från sina resurser. För att hjälpa dig att bibehålla en nära klockan på din verksamhet IoT-hubben är helt integrerat med [Azure-Monitor] [ lnk-AM] och [Azure Resource Health] [ lnk-ARH]. Dessa två tjänster fungerar tillsammans för att förse dig med de data som behövs för att hålla din IoT-lösningar upp och körs i ett felfritt tillstånd. 

Azure övervakaren är en enda källa för övervakning och loggning för alla dina Azure-tjänster. Du kan skicka diagnostiska loggar som genereras av Övervakare för Azure Log Analytics, Händelsehubbar eller Azure Storage för anpassade bearbetning. Azure övervakaren mätvärden och diagnostikfunktionerna inställningar ger inblick i prestandan för dina resurser. Fortsätta att läsa den här artikeln för att lära dig hur du [Använd Azure-Monitor](#use-azure-monitor) med IoT-hubben. 

> [!IMPORTANT]
> De händelser som sänds av tjänsten IoT-hubb med hjälp av Azure-Monitor diagnostikloggar är inte garanterat vara tillförlitliga eller sorterad. Vissa händelser kan tappas bort eller levereras i ordning. Diagnostikloggar också är inte avsett att vara realtid och det kan ta flera minuter för händelser som loggas önskat mål.

Azure Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. En anpassad instrumentpanel innehåller aktuella och tidigare hälsostatus för din IoT-hubbar. Fortsätta att läsa den här artikeln för att lära dig hur du [Använd Azure Resource Health](#use-azure-resource-health) med IoT-hubben. 

Förutom att integrera med de här båda tjänsterna tillhandahåller IoT-hubb också sin egen mått som du kan använda för att förstå statusen för din IoT-resurser. Läs mer i [förstå IoT-hubb mått][lnk-metrics].

## <a name="use-azure-monitor"></a>Använda Azure Monitor

Azure-Monitor innehåller resursnivå diagnostikinformation, vilket innebär att du kan övervaka åtgärder som äger rum i din IoT-hubb. 

Övervaka Azure diagnostics inställningar ersätter åtgärderna IoT-hubb övervaka. Om du använder operations övervakning, bör du migrera dina arbetsflöden. Mer information finns i [migrera från operations övervakningsinställningarna Diagnostics][lnk-migrate].

Mer information om specifika mått och händelser som Azure-Monitor söker finns [stöds mått med Azure-Monitor] [ lnk-AM-metrics] och [tjänster, scheman och kategorier som stöds för Azure Diagnostikloggar][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Förstå loggarna

Azure övervakaren spårar olika åtgärder som sker i IoT-hubb. Varje kategori har ett schema som definierar hur händelser i den kategorin rapporteras. 

#### <a name="connections"></a>Anslutningar

Anslutningar kategorin spårar enheter ansluta och koppla bort händelser från en IoT-hubb samt fel. Spårning av den här kategorin är användbar för att identifiera obehöriga anslutningsförsök och för att spåra när anslutningen bryts för enheter i områden i dålig anslutning.

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

#### <a name="cloud-to-device-commands"></a>Moln till enhet kommandon

Moln till enhet kommandon kategorin spårar fel som uppstår vid IoT-hubb och är relaterade till moln-till-enhetsmeddelande pipeline. Den här kategorin innehåller fel som uppstår när meddelanden moln till enhet (till exempel obehöriga avsändaren), ta emot meddelanden moln till enhet (till exempel leverans för många) och ta emot meddelandet moln till enhet feedback (t ex feedback har upphört att gälla). Den här kategorin fånga inte fel från en enhet som hanterar ett moln till enhet meddelande felaktigt om moln till enhet meddelandet levererades har.

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

#### <a name="device-identity-operations"></a>Enhetens identitet åtgärder

Enhetskategori identitet operations spårar fel som uppstår vid försök att skapa, uppdatera eller ta bort en post i registret för din IoT-hubb identitet. Spårning av den här kategorin är användbart för att etablera scenarier.

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

Meddelandet routning kategorin spårar fel som inträffar när meddelandet väg utvärdering och slutpunkten hälsa som uppfattas av IoT-hubb. Den här kategorin innefattar händelser, t.ex. när en regel som utvärderar till ”undefined”, när IoT-hubb markerar en slutpunkt som förlorade och andra fel togs emot från en slutpunkt. Den här kategorin omfattar inte felen om själva meddelandena (till exempel enhet begränsning fel) som har rapporterats under kategorin ”enhetstelemetrin”.

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

#### <a name="device-telemetry"></a>Enhetstelemetrin

Telemetri enhetskategori spårar fel som uppstår vid IoT-hubb och är relaterade till telemetri pipeline. Den här kategorin innehåller fel som uppstår när du skickar telemetriska händelser (till exempel begränsning) och ta emot telemetriska händelser (till exempel obehöriga läsare). Den här kategorin kan inte fånga fel som orsakats av kod som körs på själva enheten.

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

Filen överför kategorin spårar fel som uppstår vid IoT-hubb och är relaterade till funktioner för överföring av filer. Den här kategorin omfattar:

* Fel som inträffar med SAS-URI, till exempel när det upphör att gälla innan en enhet meddelar hubb för en överförda.
* Det gick inte överföringar som rapporteras av enheten.
* Fel som uppstår när en fil inte hittas i lagringen under skapande av IoT-hubb notification meddelandet.

Den här kategorin kan inte fånga fel som uppstår direkt medan enheten är Överför en fil till lagring.

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

#### <a name="cloud-to-device-twin-operations"></a>Moln till enhet dubbla åtgärder

Moln till enhet dubbla operations kategorin spårar service-initierad händelser på enheten twins. Dessa åtgärder kan inkludera get dubbla, uppdatera rapporterade egenskaper och prenumerera på Egenskaper

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

#### <a name="device-to-cloud-twin-operations"></a>Enhet till moln dubbla åtgärder

Enhet till moln dubbla operations kategorin spårar enhet initieras händelser på enheten twins. Dessa åtgärder kan inkludera get dubbla, uppdatera eller ersätta taggar, och uppdatera eller ersätta egenskaper. 

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

#### <a name="twin-queries"></a>Dubbla frågor

Kategorin dubbla frågor rapporter på begäran för enheten twins som startas i molnet. 

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

Kategorin jobb operations rapporter om jobbförfrågningar att uppdatera enheten twins eller anropa direkt metoder på flera enheter. Dessa begäranden initieras i molnet. 

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

#### <a name="direct-methods"></a>Direkt-metoder

Kategorin direkt metoder spårar begäran respons interaktioner som skickades till enskilda enheter. Dessa begäranden initieras i molnet. 

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

Du kan skapa program som läser ut loggarna så att du kan vidta åtgärder baserat på informationen i dem när du har skapat händelseloggning via inställningarna för webbprogramdiagnostik. Den här exempelkoden hämtar loggarna från en händelsehubb:

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

## <a name="use-azure-resource-health"></a>Använd Azure Resource Health

Använd Azure Resource Health för att övervaka om din IoT-hubb som är igång. Du kan också lära dig om ett regionalt strömavbrott påverka hälsotillståndet för din IoT-hubb. För att förstå specifik information om hälsotillståndet för dina Azure IoT Hub, rekommenderar vi att du [Använd Azure-Monitor](#use-azure-monitor). 

Azure IoT-hubb anger hälsa på regional nivå. Om det regionala avbrott som påverkar din IoT-hubb hälsostatus visas som **okänd**. Mer information om specifika hälsokontroller som utför Azure Resource Health finns [resurstyper och hälsa kontrollerar i Azure resource health][lnk-ARH-checks].

Följ dessa steg för att kontrollera hälsotillståndet för din IoT-hubbar:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till **Hälsotjänst** > **Resource health**.
1. I listrutorna, väljer din prenumeration och **IoT-hubb**.

Mer information om hur du tolkar hälsotillståndsdata finns [Azure resource health översikt][lnk-ARH]

## <a name="next-steps"></a>Nästa steg

- [Förstå IoT-hubb mått][lnk-metrics]
- [IoT fjärrövervaknings och meddelanden med Azure Logikappar ansluta din IoT-hubb och postlåda][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
