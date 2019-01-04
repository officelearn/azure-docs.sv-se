---
title: Spåra anpassade åtgärder med Azure Application Insights .NET SDK | Microsoft Docs
description: Spåra anpassade åtgärder med Azure Application Insights .NET SDK
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 8e082f15cff616b9dc63fbf4ad51e94d078a04f3
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811298"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Spåra anpassade åtgärder med Application Insights SDK för .NET

Azure Application Insights SDK: er spåra automatiskt inkommande HTTP-begäranden och anrop till beroende tjänster, till exempel HTTP-begäranden och SQL-frågor. Spårnings- och korrelation av begäranden och beroenden ger insyn i hela programmets svarstider och tillförlitlighet i alla mikrotjänster som kombinerar det här programmet. 

Det finns en klass av mönster som inte stöds med det allmänna skyddet. Korrekt övervakning av dessa mönster som kräver manuell kod instrumentation. Den här artikeln beskriver några mönster som kan kräva manuella instrumentation, till exempel anpassade kö för bearbetning och köra långvariga bakgrundsaktiviteter.

Det här dokumentet innehåller vägledning om hur du spåra anpassade åtgärder med Application Insights SDK. Den här dokumentationen gäller för:

- Application Insights för .NET (även kallat Base SDK) version 2.4 +.
- Application Insights för web programkörning (ASP.NET) version 2.4 +.
- Application Insights för ASP.NET Core version 2.1 +.

## <a name="overview"></a>Översikt
En åtgärd är en logisk mängd arbete körs av ett program. Den har ett namn, starttid, varaktighet, resultatet samt en kontext för körning som användarnamn, egenskaper och resultatet. Om åtgärden A initierades av åtgärden B, har sedan åtgärden B angetts som en överordnad för A. En åtgärd kan bara ha en överordnad, men den kan ha många underordnade åtgärder. Mer information om åtgärder och telemetrikorrelation finns [Azure Application Insights telemetrikorrelation](correlation.md).

I Application Insights .NET SDK beskrivs åtgärden av den abstrakta klassen [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) och dess underordnade [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) och [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Inkommande operations spårning 
Application Insights web SDK: N samlar automatiskt in HTTP-begäranden för ASP.NET-program som körs i en IIS-pipeline och alla ASP.NET Core-program. Det finns stöd för community-lösningar för andra plattformar och ramverk. Men om programmet inte stöds av någon av standard- eller stöd för community-lösningar, kan du instrumentera det manuellt.

Ett annat exempel som kräver anpassade spårning är arbetare som tar emot objekt från kön. För vissa köer spåras anropet för att lägga till ett meddelande i den här kön som ett beroende. Men samlas hur på hög nivå som beskriver meddelandebehandling inte in automatiskt.

Nu ska vi se hur sådana åtgärder kan spåras.

På en hög nivå uppgiften är att skapa `RequestTelemetry` och ange kända egenskaper. När åtgärden har slutförts kan spåra du telemetri. Exemplet nedan visar den här uppgiften.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-begäran i Owin egenhanterad app
I det här exemplet trace kontext sprids enligt den [HTTP-protokollet för korrelation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Du bör förvänta dig att ta emot rubriker som det beskrivs.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

HTTP-protokollet för korrelation deklarerar också den `Correlation-Context` rubrik. Men utelämnas det här för enkelhetens skull.

## <a name="queue-instrumentation"></a>Kön instrumentation
När den har [HTTP-protokollet för korrelation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) om du vill skicka korrelationsinformation med HTTP-begäran, varje kö-protokollet har att definiera hur samma information skickas vidare kömeddelandet. Vissa kö-protokoll (till exempel AMQP) kan skicka ytterligare metadata och vissa andra (sådana Azure Storage-kön) kräver kontexten som ska kodas i nyttolasten för meddelandet.

### <a name="service-bus-queue"></a>Service Bus-kö
Application Insights spårar Service Bus-meddelanden anrop med den nya [Microsoft Azure ServiceBus-klienten för .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) version 3.0.0 och senare.
Om du använder [hanteraren meddelandemönstret](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) för att bearbeta meddelanden du är klar: alla Service Bus-anrop som görs av din tjänst spåras automatiskt och med andra telemetri-objekt. Referera till den [Service Bus klientspårning med Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) om du manuellt bearbeta meddelanden.

Om du använder [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) paketet, läsa vidare - följande exempel visar hur du kan spåra (och korrelera)-anrop till Service Bus som använder AMQP-protokollet för Service Bus-kö och Application Insights inte automatiskt spåra köåtgärder.
Korrelations-ID: n skickas i meddelandeegenskaperna.

#### <a name="enqueue"></a>Placera

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage-kö
I följande exempel visas hur du spårar den [Azure Storage-kö](../../storage/queues/storage-dotnet-how-to-use-queues.md) åtgärder och korrelera telemetri mellan producenten, konsumenten och Azure Storage. 

Storage-kö har ett HTTP-API. Alla anrop till kön spåras med Application Insights beroende insamlaren för HTTP-begäranden.
Kontrollera att du har `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` i `applicationInsights.config`. Om du inte har det, lägga till den programmässigt enligt beskrivningen i [filtrera och Förbearbeta i Azure Application Insights SDK](../../azure-monitor/app/api-filtering-sampling.md).

Om du konfigurerar Application Insights manuellt, kontrollera att du skapar och initiera `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` på samma sätt till:
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Du kan även att korrelera åtgärds-ID för Application Insights med Storage begärande-ID. Information om hur du ställer och få en begäran om Storage-klient och server begäran-ID finns i [övervaka, diagnostisera och felsöka Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Placera
Eftersom Storage-köer stöder HTTP-API, spåras automatiskt alla åtgärder med kön med Application Insights. I många fall kan borde den här instrumentation räcka. Men för att korrelera spårningar på konsument-sida med producent spårningar, måste du ange vissa Korrelations-kontexten på samma sätt till hur vi kan i HTTP-protokollet för korrelation. 

Det här exemplet visar hur du spårar den `Enqueue` igen. Du kan:

 - **Korrelera återförsök (i förekommande fall)**: Alla har en gemensam överordnad som har den `Enqueue` igen. Annars är de spåras som underordnade till den inkommande begäranden. Om det finns flera logiska begäranden i kön, kan det vara svårt att hitta vilka anropet gav återförsök.
 - **Korrelera Storage-loggar (om och vid behov)**: De är korrelerad med Application Insights telemetry.

Den `Enqueue` åtgärden är underordnad till en överordnad åtgärd (till exempel en inkommande HTTP-begäran). HTTP-anrop för beroende är underordnad den `Enqueue` åtgärden och aggregerar för inkommande begäran:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Att minska mängden telemetri programmets rapporter eller om du inte vill spåra den `Enqueue` åtgärden av andra orsaker, Använd den `Activity` API direkt:

- Skapa (och starta) en ny `Activity` i stället för att starta Application Insights-åtgärden. Du gör *inte* måste du tilldela några egenskaper på den utom åtgärdens namn.
- Serialisera `yourActivity.Id` till meddelandenyttolast i stället för `operation.Telemetry.Id`. Du kan också använda `Activity.Current.Id`.


#### <a name="dequeue"></a>Ta bort från kön
På samma sätt till `Enqueue`, en faktiska HTTP-begäran till lagringskön spåras automatiskt av Application Insights. Men den `Enqueue` åtgärden sker antas vara i överordnade samband, till exempel en inkommande begäran-kontext. Application Insights SDK: er korrelera automatiskt en sådan åtgärd (och dess HTTP-delen) med den överordnade begäran och annan telemetri som rapporterats i samma definitionsområde.

Den `Dequeue` det är svårt att åtgärden. Application Insights SDK spårar automatiskt HTTP-begäranden. Men vet den inte kontexten korrelation tills meddelandet tolkas. Det går inte att korrelera HTTP-begäran att hämta meddelanden med resten av telemetrin.

I många fall kan det vara praktiskt att korrelera HTTP-begäran till kön med andra spårningar samt. I följande exempel visar hur du gör det:

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

I följande exempel spåras ett inkommande meddelande på ett sätt på samma sätt till inkommande HTTP-begäran:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

På samma sätt kan andra köåtgärder instrumenterats. En peek-åtgärd bör instrumenterats på ett liknande sätt som en ta-åtgärd. Instrumentering av kön hanteringsåtgärder är inte nödvändigt. Application Insights spårar åtgärder som till exempel HTTP och i de flesta fall är det tillräckligt.

När du instrumenterar borttagning av meddelanden, se till att ange igen (korrelation) identifierare. Du kan också använda den `Activity` API. Behöver du inte att ange identifierare för åtgärden för telemetri objekt eftersom Application Insights SDK gör det åt dig:

- Skapa en ny `Activity` när du har installerat ett objekt från kön.
- Använd `Activity.SetParentId(message.ParentId)` att korrelera loggar för konsument- och producenten.
- Starta den `Activity`.
- Spåra borttagningar, bearbeta och ta bort med hjälp av `Start/StopOperation` hjälpprogram. Göra det från det samma asynkrona kontrollflödet (körningskontext). På så sätt är de korrelerade korrekt.
- Stoppa den `Activity`.
- Använd `Start/StopOperation`, eller anropa `Track` telemetri manuellt.

### <a name="batch-processing"></a>Batchbearbetning
Med vissa köer kan du kan ta bort från kön flera meddelanden med en begäran. Bearbetning av sådana meddelanden är antas vara oberoende och hör till olika logiska åtgärder. I det här fallet det går inte att kombinera den `Dequeue` åtgärden att viss meddelandebehandling.

Varje meddelande som ska bearbetas i sin egen asynkrona Kontrollflöde. Mer information finns i den [utgående beroenden spåra](#outgoing-dependencies-tracking) avsnittet.

## <a name="long-running-background-tasks"></a>Långvariga bakgrundsaktiviteter
Vissa program startar långvariga åtgärder som kan orsakas av användarbegäranden. Ur spårning/instrumentation är det inte skiljer sig från förfrågan eller beroende instrumentation: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

I det här exemplet `telemetryClient.StartOperation` skapar `RequestTelemetry` och fyller Korrelations-kontext. Vi antar att du har en överordnad åtgärd som har skapats av inkommande begäranden som schemalagts igen. Så länge `BackgroundTask` startar asynkrona samma Kontrollflöde som en inkommande begäran, det korreleras med den överordnade åtgärden. `BackgroundTask` och alla kapslade telemetri objekt automatiskt kopplas ihop med den begäran som gör att det, även när begäran avslutas.

När uppgiften startar från den bakgrundstråd som inte har någon åtgärd (`Activity`) som är associerade med den `BackgroundTask` inte har en överordnad. Det kan dock ha kapslade åtgärder. Alla artiklar för telemetri som rapporterats från aktiviteten är korrelerad till den `RequestTelemetry` skapas i `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Utgående beroenden spårning
Du kan spåra dina egna beroende-typ eller en åtgärd som inte stöds av Application Insights.

Den `Enqueue` -metod i Service Bus-kö eller Storage-kö kan fungera som exempel på sådana anpassade spårning.

Allmänna riktlinjer för anpassade beroendespårning är att:

- Anropa den `TelemetryClient.StartOperation` (tillägg)-metod som fyller på `DependencyTelemetry` egenskaper som krävs för korrelation och vissa andra egenskaper (starttid tidsstämpel, varaktighet).
- Ange andra anpassade egenskaper på den `DependencyTelemetry`, till exempel namnet och andra uppgifter som du behöver.
- Se ett beroende anropa och vänta tills den.
- Stoppa igen med `StopOperation` när den är klar.
- Hantera undantag.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Tar bort åtgärden orsakar att stoppas, så att du kan göra det i stället för att anropa `StopOperation`.

*Varning*: i vissa fall kan unhanded undantag [förhindra](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` anropas så att åtgärder inte kan spåras.

### <a name="parallel-operations-processing-and-tracking"></a>Parallella åtgärder som bearbetar och spårning

`StopOperation` endast avbryts som startades. Om den aktuella åtgärden inte matchar det du vill stoppa, `StopOperation` gör ingenting. Den här situationen kan inträffa om du startar flera åtgärder parallellt i samma körningskontexten skapades:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Se till att du alltid anropa `StartOperation` och bearbeta igen i samma **async** metod för att isolera åtgärder som körs parallellt. Om åtgärden sker synkront (eller inte asynkrona), omsluta processen och spåra med `Task.Run`:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [telemetrikorrelation](correlation.md) i Application Insights.
- Se den [datamodellen](../../azure-monitor/app/data-model.md) för Application Insights och modellen.
- Rapportera anpassade [händelser och mått](../../azure-monitor/app/api-custom-events-metrics.md) till Application Insights.
- Kolla in standard [configuration](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) för kontexten egenskapssamling.
- Kontrollera den [System.Diagnostics.Activity användarhandboken](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) att se hur vi korrelera telemetri.
