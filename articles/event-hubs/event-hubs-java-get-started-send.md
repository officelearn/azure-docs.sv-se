---
title: Skicka och ta emot händelser med hjälp av Java – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång av hur man skapar en Java-program som skickar händelser till Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 0487cac6a0cf7d37befdf0d7cfab33ad6a62cf7f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679652"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-java"></a>Skicka händelser till eller ta emot händelser från Azure Event Hubs med Java

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien visar hur du skapar Java-program att skicka händelser till eller ta emot händelser från en event hub. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- En Java-utvecklingsmiljön. Den här självstudien används [Eclipse](https://www.eclipse.org/).
- **Skapa ett Event Hubs-namnområde och en event hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan värdet för åtkomstnyckeln för event hub genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder åtkomstnyckeln i koden du skriver senare i den här självstudien. Standard nyckelnamnet är: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett Java-program för att skicka händelser till en händelsehubb. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Lägg till referens till Azure Event Hubs-biblioteket

Java-klientbibliotek för Event Hubs är tillgängliga för användning i Maven-projekt från den [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Du kan referera till det här biblioteket med följande beroendedeklaration i Maven-projektfilen:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

För olika typer av versionsmiljöer kan du uttryckligen hämta de senast utgivna JAR-filerna från den [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

För en enkel händelse utgivare och importera den *com.microsoft.azure.eventhubs* paketet för Event Hubs-klientklasser och *com.microsoft.azure.servicebus* Paketera för verktygsklasser som vanliga undantag som delas med Azure Service Bus meddelandeklient. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Lägg till en klass med namnet `SimpleSend`, och Lägg till följande kod i klassen:

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

### <a name="construct-connection-string"></a>Skapa anslutningssträng

Använda klassen ConnectionStringBuilder för att konstruera ett Anslutningssträngens värde att skicka till Event Hubs klientinstans. Ersätt platshållarna med de värden du erhöll när du skapade namnområde och en händelsehubb:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("speventhubns") 
                .setEventHubName("speventhub")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("2+WMsyyy1XmUtEnRsfOmTTyGasfJgsVjGAOIN20J1Y8=");
```

### <a name="write-code-to-send-events"></a>Skriva kod för att skicka händelser

Skapa en enda händelse genom att omvandla en sträng till UTF-8 byte kodningen. Sedan skapar en ny instans för Event Hubs-klienten från anslutningssträngen och skicka meddelandet:   

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

Skapa och kör programmet och kontrollera att det inte finns några fel.

Grattis! Du har nu skickat meddelanden till en händelsehubb.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Tillägg: Hur meddelanden skickas till EventHub-partitioner

Innan meddelanden hämtas av användare, har de publiceras till partitionerna först av utgivarna. När meddelanden har publicerats till event hub synkront med metoden sendSync() com.microsoft.azure.eventhubs.EventHubClient-objektet visas kan meddelandet skickas till en specifik partition eller distribueras till alla tillgängliga partitioner på ett sätt för resursallokering beroende på om partitionsnyckel anges eller inte.

När du anger en sträng som representerar Partitionsnyckeln ska nyckeln kodas för att avgöra vilken partition som ska skicka händelsen till.

När Partitionsnyckeln inte har angetts, sedan kommer meddelanden round robined till alla tillgängliga partitioner

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
Koden i den här självstudien är baserad på den [EventProcessorSample kod på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), som du kan kontrollera för att se fullständiga fungerande program.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Ta emot meddelanden med EventProcessorHost i Java

**EventProcessorHost** är en javaklass som förenklar mottagandet av händelser från Event Hubs genom att hantera permanenta kontrollpunkter och parallella mottaganden från Event Hubs. Med EventProcessorHost kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder EventProcessorHost för en enda mottagare.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Om du vill använda EventProcessorHost, måste du ha ett [Azure Storage-konto] [Azure Storage-konto]:

1. Logga in på [Azure-portalen](https://portal.azure.com), och klicka på **+ skapa en resurs** på vänster sida av skärmen.
2. Klicka på **Lagring** och sedan på **Lagringskonto**. I den **skapa lagringskonto** fönstret anger du ett namn för lagringskontot. Slutför resten av fälten, Välj önskad region och klicka sedan på **skapa**.
   
    ![Skapa lagringskonto](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Klicka på det nyligen skapade lagringskontot och klicka sedan på **åtkomstnycklar**:
   
    ![Hämta åtkomstnycklar](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Kopiera key1-värdet till en tillfällig plats. Du använder det senare i den här självstudien.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Skapa ett Java-projekt med EventProcessor-värden

Java-klientbibliotek för Event Hubs är tillgängliga för användning i Maven-projekt från den [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22), och kan refereras med följande beroendedeklaration i Maven-projektfilen: 

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

För olika typer av versionsmiljöer kan du uttryckligen hämta de senast utgivna JAR-filerna från den [Maven Central Repository] [https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22].  

1. För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Klassen kallas `ErrorNotificationHandler`.     
   
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
2. Använd följande kod för att skapa en ny klass som kallas `EventProcessorSample`. Ersätt platshållarna med värden som används när du har skapat kontot för event hub och lagring:
   
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
3. Skapa en mer klass med namnet `EventProcessor`, med följande kod:
   
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

Den här guiden använder en enda instans av EventProcessorHost. För att öka dataflödet rekommenderar vi att du kör flera instanser av EventProcessorHost, helst på separata datorer.  Det ger även redundans. I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. Om du vill att flera mottagare bearbetar *alla* händelser, måste du använda konceptet **ConsumerGroup**. När du tar emot händelser från olika datorer, kan det vara praktiskt att ange namn för EventProcessorHost-instanser baserat på de datorer (eller roller) som de har distribuerats i.

### <a name="publishing-messages-to-eventhub"></a>Publicera meddelanden till EventHub

Innan meddelanden hämtas av användare, har de publiceras till partitionerna först av utgivarna. Det är värt att när meddelanden har publicerats till event hub synkront med metoden sendSync() com.microsoft.azure.eventhubs.EventHubClient-objektet visas meddelandet kan skickas till en specifik partition eller distribueras till alla tillgängliga partitioner på en resursallokering sätt beroende på om anges partitionsnyckel eller inte.

När du anger en sträng som representerar Partitionsnyckeln hashas nyckeln för att avgöra vilken partition som ska skicka händelsen till.

När Partitionsnyckeln inte har angetts, sedan är meddelanden resursallokering robined till alla tillgängliga partitioner

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

API: et är en mekanism för att implementera din anpassade kontrollpunktshanterare för scenarier där standardimplementering inte är kompatibla med ditt användningsområde.

Kontrollpunktshanterare standard använder blob-lagring men om du åsidosätter kontrollpunktshanterare som används av EPH med en egen implementering, du kan använda alla store som du vill säkerhetskopiera implementeringen kontrollpunkt manager.

Skapa en klass som implementerar gränssnittet com.microsoft.azure.eventprocessorhost.ICheckpointManager

Använd din anpassade implementering av kontrollpunktshanterare (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Inom din implementering kan du åsidosätta kontrollpunkter standardmekanismen och implementera vår egen kontrollpunkter som bygger på ditt eget datalager (till exempel SQL Server, CosmosDB och Azure Cache för Redis). Vi rekommenderar att store används för att säkerhetskopiera implementeringen kontrollpunkt manager är tillgänglig för alla EPH-instanser som bearbetar händelser för konsumentgruppen.

Du kan använda alla datalager som är tillgänglig i din miljö.

Klassen com.microsoft.azure.eventprocessorhost.EventProcessorHost ger dig två konstruktorer så att du kan åsidosätta kontrollpunktshanterare för din EventProcessorHost.


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktionerna och terminologin i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

