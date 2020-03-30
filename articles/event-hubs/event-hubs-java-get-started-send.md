---
title: Skicka eller ta emot händelser från Azure Event Hubs med Java (äldre)
description: Den här artikeln innehåller en genomgång av hur du skapar ett Java-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det gamla azure-eventhubs-paketet.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 2c9baa4c0e048419ece09b954cee1af21b1f0cc1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77158017"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Använd Java för att skicka händelser till eller ta emot händelser från Azure Event Hubs (azure-eventhubs)

Den här snabbstarten visar hur du skickar händelser till och ta emot händelser från en händelsehubb med **azure-eventhubs Java-paketet.**

> [!WARNING]
> Den här snabbstarten använder de gamla **azure-eventhubs-** och **azure-eventhubs-eph-paketen.** En snabbstart som använder det senaste **azure-messaging-eventhubs-paketet** finns i [Skicka och ta emot händelser med azure-messaging-eventhubs](get-started-java-send-v2.md). Information om hur du flyttar ditt program från att använda det gamla paketet till ett nytt finns i [guiden för att migrera från azure-eventhubs till azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md). 


## <a name="prerequisites"></a>Krav

Om du inte har gjort det tidigare i Azure Event Hubs läser du [översikt över eventhubbar](event-hubs-about.md) innan du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure-prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) eller använda dina msdn-prenumerationsförmåner när du [skapar ett konto](https://azure.microsoft.com).
- En Java-utvecklingsmiljö. Den här snabbstarten använder [Eclipse](https://www.eclipse.org/).
- **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Det första steget är att använda [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan värdet för åtkomstnyckeln för händelsehubben genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder åtkomstnyckeln i koden du skriver senare i den här snabbstarten. Standardnyckelnamnet är: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Skicka händelser 
I det här avsnittet visas hur du skapar ett Java-program för att skicka händelser till en händelsehubb. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Du kan också följa stegen i den här snabbstarten för att skapa egna.

### <a name="add-reference-to-azure-event-hubs-library"></a>Lägga till referens till Azure Event Hubs-bibliotek

Java-klientbiblioteket för eventhubbar är tillgängligt för användning i Maven-projekt från [Mavens centrala databas](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Du kan referera till det här biblioteket med hjälp av följande beroendedeklaration i maven-projektfilen:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

För olika typer av byggmiljöer kan du uttryckligen hämta de senast släppta JAR-filerna från [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

För en enkel händelseutgivare importerar du paketet *com.microsoft.azure.eventhubs* för event hubs-klientklasserna och *com.microsoft.azure.servicebus-paketet* för verktygsklasser, till exempel vanliga undantag som delas med Azure Service Bus-meddelandeklienten. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Lägg till `SimpleSend`en klass med namnet och lägg till följande kod i klassen:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Konstruera anslutningssträng

Använd klassen ConnectionStringBuilder för att skapa ett anslutningssträngvärde som ska gå till klientinstansen Event Hubs. Ersätt platshållarna med de värden som du erhöll när du skapade namnområdet och händelsehubben:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Skriv kod för att skicka händelser

Skapa en unik händelse genom att omvandla en sträng till dess UTF-8 byte-kodning. Skapa sedan en ny Event Hubs-klientinstans från anslutningssträngen och skicka meddelandet:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Skapa och kör programmet och se till att det inte finns några fel.

Grattis! Du har nu skickat meddelanden till en händelsehubb.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Tillägg: Så här dirigeras meddelanden till EventHub-partitioner

Innan meddelanden hämtas av konsumenter måste de först publiceras till partitionerna av utgivarena. När meddelanden publiceras på händelsehubben synkront med metoden sendSync() på objektet com.microsoft.azure.eventhubs.EventHubClient kan meddelandet skickas till en viss partition eller distribueras till alla tillgängliga partitioner på ett avrundat robin-sätt, kan meddelandet skickas till en viss partition eller distribueras till alla tillgängliga partitioner på ett avrundat robin-sätt beroende på om partitionsnyckeln har angetts eller inte.

När en sträng som representerar partitionsnyckeln anges hasheras nyckeln för att avgöra vilken partition som ska skickas till.

När partitionsnyckeln inte har angetts round-robined meddelanden till alla tillgängliga partitioner

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Ta emot händelser
Koden i den här självstudien baseras på [EventProcessorSample-koden på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), som du kan undersöka för att se hela arbetsprogrammet.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Ta emot meddelanden med EventProcessorHost i Java

**EventProcessorHost** är en Java-klass som förenklar mottagning av händelser från eventhubbar genom att hantera beständiga kontrollpunkter och parallella mottagningar från dessa eventhubbar. Med EventProcessorHost kan du dela upp händelser mellan flera mottagare, även när de finns i olika noder. Det här exemplet visas hur man använder EventProcessorHost för en enda mottagare.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Om du vill använda EventProcessorHost måste du ha ett [Azure Storage-konto][Azure Storage-konto]:

1. Logga in i [Azure-portalen](https://portal.azure.com)och välj **Skapa en resurs** till vänster på skärmen.
2. Välj **Lagring**och välj sedan **Lagringskonto**. Skriv ett namn på lagringskontot i fönstret **Skapa lagringskonto.** Fyll i resten av fälten, välj önskad region och välj sedan **Skapa**.
   
    ![Skapa ett lagringskonto i Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Välj det nyskapade lagringskontot och välj sedan **Åtkomstnycklar:**
   
    ![Hämta dina åtkomstnycklar i Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Kopiera key1-värdet till en tillfällig plats. Du använder det senare i den här självstudien.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Skapa ett Java-projekt med EventProcessor-värden

Java-klientbiblioteket för eventhubbar är tillgängligt för användning i Maven-projekt från [Mavens centrala databas](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)och kan refereras med hjälp av följande beroendedeklaration i din Maven-projektfil: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

För olika typer av byggmiljöer kan du uttryckligen hämta de senast släppta JAR-filerna från [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Klassen heter `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Använd följande kod för att skapa en ny klass som kallas `EventProcessorSample`. Ersätt platshållarna med de värden som användes när du skapade händelsehubben och lagringskontot:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Skapa ytterligare en `EventProcessor`klass med namnet med hjälp av följande kod:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Den här guiden använder en enda instans av EventProcessorHost. För att öka dataflödet rekommenderar vi att du kör flera instanser av EventProcessorHost, helst på separata datorer.  Det ger redundans också. I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. Om du vill att flera mottagare bearbetar *alla* händelser, måste du använda konceptet **ConsumerGroup**. När du tar emot händelser från olika datorer, kan det vara praktiskt att ange namn för EventProcessorHost-instanser baserat på de datorer (eller roller) som de har distribuerats i.

### <a name="publishing-messages-to-eventhub"></a>Publicera meddelanden till EventHub

Innan meddelanden hämtas av konsumenter måste de först publiceras till partitionerna av utgivarena. Det är värt att notera att när meddelanden publiceras till händelsehubben synkront med metoden sendSync() på objektet com.microsoft.azure.eventhubs.EventHubClient kan meddelandet skickas till en viss partition eller distribueras till alla tillgängliga partitioner på ett round-robin-sätt beroende på om partitionsnyckeln har angetts eller inte.

När en sträng som representerar partitionsnyckeln anges hasheras nyckeln för att avgöra vilken partition som händelsen ska skickas till.

När partitionsnyckeln inte har angetts round-robined meddelanden till alla tillgängliga partitioner

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementera en anpassad CheckpointManager för EventProcessorHost (EPH)

API:et tillhandahåller en mekanism för att implementera din anpassade kontrollpunktshanterare för scenarier där standardimplementeringen inte är kompatibel med ditt användningsfall.

Standardkontrollpunktshanteraren använder blob-lagring, men om du åsidosätter kontrollpunktshanteraren som används av EPH med din egen implementering kan du använda vilket arkiv du vill stödja implementeringen av kontrollpunktshanteraren.

Skapa en klass som implementerar gränssnittet com.microsoft.azure.eventprocessorhost.ICheckpointManager

Använd din anpassade implementering av kontrollpunktshanteraren (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Inom implementeringen kan du åsidosätta standardkontrollpunktsmekanismen och implementera våra egna kontrollpunkter baserat på ditt eget datalager (som SQL Server, CosmosDB och Azure Cache för Redis). Vi rekommenderar att det arkiv som används för att stödja implementeringen av kontrollpunktshanteraren är tillgängligt för alla EPH-instanser som bearbetar händelser för konsumentgruppen.

Du kan använda alla datalager som är tillgängliga i din miljö.

Klassen com.microsoft.azure.eventprocessorhost.EventProcessorHost ger dig två konstruktorer som gör att du kan åsidosätta kontrollpunktshanteraren för din EventProcessorHost.


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

