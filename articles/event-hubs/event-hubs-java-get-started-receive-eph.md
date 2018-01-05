---
title: "Ta emot händelser från Azure Event Hubs använder Java | Microsoft Docs"
description: "Börja ta emot från Event Hubs använder Java"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 38e3be53-251c-488f-a856-9a500f41b6ca
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 94b56fdd1ad350d861b27644c6bedcc59e1cefb0
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Ta emot händelser från Azure Event Hubs använder Java


## <a name="introduction"></a>Introduktion
Händelsehubbar är en mycket skalbar införandet system som kan mata in miljontals händelser per sekund, aktivera ett program för att bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. När uppgifterna väl samlats i Event Hubs, kan du omvandla och lagra data med hjälp av en leverantör av realtidsanalyser eller lagringskluster.

Mer information finns i [översikt av Händelsehubbar][Event Hubs overview].

Den här kursen visar hur du tar emot händelser i en händelsehubb med hjälp av ett konsolprogram som skrivits i Java.

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här kursen behöver du följande krav:

* Java development environment. Den här självstudiekursen förutsätter vi att [Eclipse](https://www.eclipse.org/).
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett kostnadsfritt konto på bara några minuter. Mer information om den <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">kostnadsfria utvärderingsversionen av Azure</a>.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Ta emot meddelanden med EventProcessorHost i Java

**EventProcessorHost** är en javaklass som förenklar mottagandet av händelser från Event Hubs genom att hantera permanenta kontrollpunkter och parallella mottaganden från Event Hubs. Med EventProcessorHost kan dela du händelser på flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder EventProcessorHost för en enda mottagare.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto
Om du vill använda EventProcessorHost måste du ha en [Azure Storage-konto][Azure Storage account]:

1. Logga in på den [Azure-portalen][Azure portal], och klicka på **+ ny** på vänster sida av skärmen.
2. Klicka på **Lagring** och sedan på **Lagringskonto**. På bladet **Skapa lagringskonto** anger du ett namn för lagringskontot. Slutför resten av fält, Välj önskad region och klicka sedan på **skapa**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Klicka på det nyligen skapade lagringskontot och klicka sedan på **Hantera åtkomstnycklar**:
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Kopiera den primära åtkomstnyckeln till en tillfällig plats för senare användning i den här kursen.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Skapa ett Java-projekt med EventProcessor-värden
Java-klientbibliotek för Händelsehubbar är tillgänglig för användning i Maven-projekt från den [Maven centrallager][Maven Package], och kan refereras med hjälp av följande beroende deklarationen i projektfilen Maven:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```

För olika typer av versionsmiljöer du explicit kan hämta de senaste utgivna JAR-filerna från de [Maven centrallager] [ Maven Package] eller från [distributionsplatsen versionen på GitHub](https://github.com/Azure/azure-event-hubs/releases).  

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
2. Använd följande kod för att skapa en ny klass som kallas `EventProcessor`.
   
    ```java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;
   
    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;
   
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }
   
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
   
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }
   
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
   
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```
3. Skapa en mer klass med namnet `EventProcessorSample`, med följande kod.
   
    ```java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;
   
    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";
   
            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
   
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
   
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
   
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }
   
            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
   
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
   
            System.out.println("End of sample");
        }
    }
    ```
4. Ersätt följande fält med de värden som används när du har skapat kontot händelse NAV- och lagring.
   
    ```java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
   
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
   
    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [!NOTE]
> Den här guiden använder en enda instans av EventProcessorHost. För att öka genomströmning rekommenderas att du kör flera instanser av EventProcessorHost, helst på separata datorer.  Detta ger redundans samt. I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. Om du vill att flera mottagare bearbetar *alla* händelser, måste du använda konceptet **ConsumerGroup**. När du tar emot händelser från olika datorer, kan det vara praktiskt att ange namn för EventProcessorHost-instanser baserat på de datorer (eller roller) som de har distribuerats i.
> 
> 

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
