---
title: Spåra anpassade åtgärder med Azure Application Insights .NET SDK
description: Spåra anpassade åtgärder med Azure Application Insights .NET SDK
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 31c1fb366e7b109ea1fa4977d8e2f908e766e0f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276106"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Spåra anpassade åtgärder med Application Insights .NET SDK

Azure Application Insights SDK:er spårar automatiskt inkommande HTTP-begäranden och anrop till beroende tjänster, till exempel HTTP-begäranden och SQL-frågor. Spårning och korrelation av begäranden och beroenden ger dig insyn i hela programmets svarstider och tillförlitlighet för alla mikrotjänster som kombinerar det här programmet. 

Det finns en klass av programmönster som inte kan stödjas allmänt. Korrekt övervakning av sådana mönster kräver manuell kod instrumentering. Den här artikeln innehåller några mönster som kan kräva manuell instrumentering, till exempel anpassad köbearbetning och tidskrävande bakgrundsuppgifter.

Det här dokumentet innehåller vägledning om hur du spårar anpassade åtgärder med SDK för programinsikter. Denna dokumentation är relevant för:

- Application Insights för .NET (kallas även Base SDK) version 2.4+.
- Application Insights för webbprogram (som körs ASP.NET) version 2.4+.
- Application Insights för ASP.NET Core version 2.1+.

## <a name="overview"></a>Översikt
En åtgärd är ett logiskt arbete som körs av ett program. Den har ett namn, starttid, varaktighet, resultat och en kontext för körning som användarnamn, egenskaper och resultat. Om åtgärd A initierades av åtgärd B anges åtgärd B som överordnad för A. En åtgärd kan bara ha en överordnad, men den kan ha många underordnade åtgärder. Mer information om åtgärder och telemetrikorrelation finns i [Azure Application Insights telemetrikorrelation](correlation.md).

I Application Insights .NET SDK beskrivs åtgärden av den abstrakta klassen [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) och dess underordnade [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) and [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Spårning av inkommande åtgärder 
Application Insights web SDK samlar automatiskt in HTTP-begäranden för ASP.NET program som körs i en IIS-pipeline och alla ASP.NET Core-program. Det finns community-stödda lösningar för andra plattformar och ramverk. Men om programmet inte stöds av någon av de standard- eller community-stödda lösningarna kan du instrumentera det manuellt.

Ett annat exempel som kräver anpassad spårning är arbetaren som tar emot artiklar från kön. För vissa köer spåras anropet för att lägga till ett meddelande i den här kön som ett beroende. Den åtgärd på hög nivå som beskriver meddelandebearbetning samlas dock inte in automatiskt.

Låt oss se hur sådana operationer kan spåras.

På en hög nivå är `RequestTelemetry` uppgiften att skapa och ange kända egenskaper. När åtgärden är klar spårar du telemetrin. I följande exempel visas den här uppgiften.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-begäran i Owin självvärdapp
I det här exemplet sprids spårningskontext enligt [HTTP-protokollet för korrelation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Du bör förvänta dig att ta emot rubriker som beskrivs där.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
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

HTTP-protokollet för korrelation `Correlation-Context` deklarerar också huvudet. Men det utelämnas här för enkelhetens skull.

## <a name="queue-instrumentation"></a>Instrumentering av kö
Det finns [W3C Trace Context](https://www.w3.org/TR/trace-context/) och HTTP Protocol for Correlation [för](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) att skicka korrelationsinformation med HTTP-begäran, men varje köprotokoll måste definiera hur samma information skickas längs kömeddelandet. Vissa köprotokoll (till exempel AMQP) tillåter att du skickar ytterligare metadata och vissa andra (till exempel Azure Storage Queue) kräver att kontexten kodas till meddelandenyttolasten.

> [!NOTE]
> * **Spårning mellan komponenter stöds inte för köer ännu** Om din producent och konsument skickar telemetri till olika Application Insights-resurser visar transaktionsdiagnostikupplevelse och programöversikt transaktioner och mappning från till. Vid köer stöds detta inte ännu. 

### <a name="service-bus-queue"></a>Service Bus-kö
Application Insights spårar Service Bus Messaging-anrop med den nya [Microsoft Azure ServiceBus Client för .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) version 3.0.0 och senare.
Om du använder [meddelandehanterarens mönster](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) för att bearbeta meddelanden är du klar: alla Service Bus-samtal som utförs av din tjänst spåras automatiskt och korreleras med andra telemetriartiklar. Se [Service Bus-klientspårningen med Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) om du behandlar meddelanden manuellt.

Om du använder [WindowsAzure.ServiceBus-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) läser du vidare – följande exempel visar hur du spårar (och korrelerar) anrop till Service Bus som Service Bus-kö använder AMQP-protokoll och Application Insights spårar inte automatiskt köåtgärder.
Korrelationsidentifierare skickas i meddelandeegenskaperna.

#### <a name="enqueue"></a>Gå på

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
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
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

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

### <a name="azure-storage-queue"></a>Kö för Azure Storage
I följande exempel visas hur du spårar [Azure Storage-köåtgärderna](../../storage/queues/storage-dotnet-how-to-use-queues.md) och korrelerar telemetri mellan producenten, konsumenten och Azure Storage. 

Lagringskön har ett HTTP-API. Alla anrop till kön spåras av Application Insights Dependency Collector för HTTP-begäranden.
Den är konfigurerad som standard på ASP.NET och ASP.NET Core-program, med andra typer av program, kan du läsa dokumentation för [konsolprogram](../../azure-monitor/app/console.md)

Du kanske också vill korrelera application insights-åtgärds-ID med id:t för lagringsbegäran. Information om hur du anger och hämtar en lagringsbegäran klient och en server begäran ID, se [Övervaka, diagnostisera och felsöka Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Gå på
Eftersom lagringsköer stöder HTTP API spåras alla åtgärder med kön automatiskt av Application Insights. I många fall bör denna instrumentering vara tillräcklig. Men för att korrelera spår på konsumentsidan med producentspår, måste du skicka vissa korrelationskontexter på samma sätt som hur vi gör det i HTTP-protokollet för korrelation. 

Det här exemplet visar `Enqueue` hur du spårar åtgärden. Du kan:

 - **Korrelera återförsök (om några)**: De har `Enqueue` alla en gemensam förälder som är åtgärden. Annars spåras de som underordnade till den inkommande begäran. Om det finns flera logiska begäranden till kön kan det vara svårt att hitta vilket samtal som resulterade i återförsök.
 - **Korrelera lagringsloggar (om och när det behövs)**: De är korrelerade med Application Insights telemetri.

Åtgärden `Enqueue` är underordnad en överordnad åtgärd (till exempel en inkommande HTTP-begäran). HTTP-beroendeanropet är underordnad `Enqueue` åtgärden och barnbarn till den inkommande begäran:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
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

Om du vill minska mängden telemetri som dina programrapporter eller `Enqueue` om du inte `Activity` vill spåra åtgärden av andra skäl använder du API:et direkt:

- Skapa (och starta) `Activity` en ny i stället för att starta application insights-åtgärden. Du behöver *inte* tilldela några egenskaper på den förutom operationsnamnet.
- Serialisera `yourActivity.Id` i meddelandets nyttolast i stället för `operation.Telemetry.Id`. Du kan `Activity.Current.Id`också använda .


#### <a name="dequeue"></a>Mer från Avqueue
`Enqueue`På samma sätt spåras en faktisk HTTP-begäran till lagringskön automatiskt av Application Insights. `Enqueue` Åtgärden sker dock förmodligen i den överordnade kontexten, till exempel en inkommande begärandekontext. Application Insights SDK:er korrelerar automatiskt en sådan åtgärd (och dess HTTP-del) med den överordnade begäran och annan telemetri som rapporteras i samma omfång.

Operationen `Dequeue` är knepig. Application Insights SDK spårar automatiskt HTTP-begäranden. Det vet dock inte korrelationskontexten förrän meddelandet tolkas. Det går inte att korrelera HTTP-begäran för att få meddelandet med resten av telemetrin, särskilt när mer än ett meddelande tas emot.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Process

I följande exempel spåras ett inkommande meddelande på ett sätt som liknar inkommande HTTP-begäran:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
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

På samma sätt kan andra köåtgärder instrumenteras. En tittoperation bör instrumenteras på ett liknande sätt som en dequeue-operation. Det är inte nödvändigt att instrumentera köhanteringsåtgärder. Application Insights spårar åtgärder som HTTP, och i de flesta fall räcker det.

När du instrumenterar borttagning av meddelanden kontrollerar du att du anger åtgärdsidentifieringarna (korrelationsidentifierarna). Du kan också använda `Activity` API:et. Då behöver du inte ange åtgärdsidentifierare för telemetriobjekten eftersom Application Insights SDK gör det åt dig:

- Skapa ett `Activity` nytt när du har ett objekt från kön.
- Används `Activity.SetParentId(message.ParentId)` för att korrelera konsument- och producentloggar.
- Starta `Activity`.
- Spåra dequeue-, process- och `Start/StopOperation` borttagningsåtgärder med hjälp av hjälpelement. Gör det från samma asynkrona kontrollflöde (körningskontext). På detta sätt är de korrelerade ordentligt.
- Stoppa `Activity`.
- Använd `Start/StopOperation`eller `Track` anropa telemetri manuellt.

### <a name="dependency-types"></a>Beroendetyper

Application Insights använder beroendetyp för att cusomize UI-upplevelser. För köer identifieras följande `DependencyTelemetry` typer av som förbättrar [transaktionsdiagnostiken:](/azure/azure-monitor/app/transaction-diagnostics)
- `Azure queue`för Azure-lagringsköer
- `Azure Event Hubs`för Azure-händelsehubbar
- `Azure Service Bus`för Azure Service Bus

### <a name="batch-processing"></a>Batchbearbetning
Med vissa köer kan du avmarkera flera meddelanden med en begäran. Bearbetning av sådana meddelanden är förmodligen oberoende och tillhör de olika logiska åtgärderna. Det går inte att korrelera åtgärden `Dequeue` till ett visst meddelande som bearbetas.

Varje meddelande ska bearbetas i sitt eget asynkrona kontrollflöde. Mer information finns i avsnittet [Spåra utgående beroenden.](#outgoing-dependencies-tracking)

## <a name="long-running-background-tasks"></a>Långvariga bakgrundsuppgifter

Vissa program startar tidskrävande åtgärder som kan orsakas av användarbegäranden. Ur spårnings-/instrumenteringsperspektiv skiljer det sig inte från begäran eller beroendeinstrumentering: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
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

I det `telemetryClient.StartOperation` här `DependencyTelemetry` exemplet skapar och fyller korrelationskontexten. Anta att du har en överordnad åtgärd som har skapats av inkommande begäranden som schemalagt åtgärden. Så länge `BackgroundTask` som börjar i samma asynkrona kontrollflöde som en inkommande begäran, är det korrelerat med den överordnade åtgärden. `BackgroundTask`och alla kapslade telemetriobjekt korreleras automatiskt med begäran som orsakade den, även efter att begäran har avslutats.

När aktiviteten startar från bakgrundstråden som inte`Activity`har någon `BackgroundTask` åtgärd ( ) associerad med den, har ingen överordnad. Det kan dock ha kapslade operationer. Alla telemetriartiklar som rapporteras från aktiviteten `DependencyTelemetry` är `BackgroundTask`korrelerade med de skapade i .

## <a name="outgoing-dependencies-tracking"></a>Spårning av utgående beroenden
Du kan spåra din egen beroende typ eller en åtgärd som inte stöds av Application Insights.

Metoden `Enqueue` i servicebusskön eller lagringskön kan fungera som exempel för sådan anpassad spårning.

Den allmänna metoden för anpassad beroendespårning är att:

- Anropa `TelemetryClient.StartOperation` metoden (tillägg) som `DependencyTelemetry` fyller de egenskaper som behövs för korrelation och vissa andra egenskaper (starttidsstämpel, varaktighet).
- Ange andra anpassade `DependencyTelemetry`egenskaper på , till exempel namnet och alla andra sammanhang som du behöver.
- Ring ett beroendesamtal och vänta på det.
- Stoppa operationen `StopOperation` med när den är klar.
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

Om du inaktiverar åtgärden stoppas åtgärden, så du `StopOperation`kan göra det i stället för att anropa .

*Varning*: i vissa fall kan ohanterat undantag [förhindra](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` att kallas så åtgärder kanske inte spåras.

### <a name="parallel-operations-processing-and-tracking"></a>Bearbetning och spårning av parallella operationer

`StopOperation`stoppar bara åtgärden som startades. Om den aktuella körningsåtgärden inte matchar `StopOperation` den du vill stoppa gör ingenting. Den här situationen kan inträffa om du startar flera åtgärder parallellt i samma körningskontext:

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

Se till att `StartOperation` du alltid anropar och bearbetar åtgärden i samma **asynkrona** metod för att isolera åtgärder som körs parallellt. Om åtgärden är synkron (eller inte async), `Task.Run`figursättningsprocess och spår med:

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

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights-åtgärder jämfört med System.Diagnostics.Activity
`System.Diagnostics.Activity`representerar den distribuerade spårningskontexten och används av ramverk och bibliotek för att skapa och sprida kontext inom och utanför processen och korrelera telemetriobjekt. Aktivitet fungerar `System.Diagnostics.DiagnosticSource` tillsammans med - anmälningsmekanismen mellan ramverket/biblioteket för att meddela om intressanta händelser (inkommande eller utgående förfrågningar, undantag osv.).

Aktiviteter är förstklassiga medborgare i Application Insights och automatiskt beroende och begäran `DiagnosticSource` insamling är starkt beroende av dem tillsammans med händelser. Om du skapar aktivitet i ditt program - det skulle inte resultera i Application Insights telemetri skapas. Application Insights måste ta emot DiagnosticSource-händelser och känna till händelsenamn och nyttolaster för att översätta aktivitet till telemetri.

Varje Application Insights-åtgärd (begäran eller `Activity` beroende) innebär - när `StartOperation` anropas skapas aktivitet under. `StartOperation`är det rekommenderade sättet att spåra begäran eller beroende telemetri manuellt och se till att allt är korrelerad.

## <a name="next-steps"></a>Nästa steg

- Lär dig grunderna i [telemetrikorrelation](correlation.md) i Application Insights.
- Kolla in hur korrelerade data driver [transaktionsdiagnostik erfarenhet](../../azure-monitor/app/transaction-diagnostics.md) och [application map](../../azure-monitor/app/app-map.md).
- Se [datamodellen](../../azure-monitor/app/data-model.md) för programinsiktstyper och datamodell.
- Rapportera anpassade [händelser och mått](../../azure-monitor/app/api-custom-events-metrics.md) till Application Insights.
- Kolla in [standardkonfiguration](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) för kontextegenskaper samling.
- Läs [användarhandboken för System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) för att se hur vi korrelerar telemetri.
