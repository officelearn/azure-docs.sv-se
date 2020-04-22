---
title: Skicka eller ta emot händelser från Azure Event Hubs med Java (senaste)
description: Den här artikeln innehåller en genomgång av hur du skapar ett Java-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det senaste azure-messaging-eventhubs-paketet.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: spelluru
ms.openlocfilehash: ca22f4481750abb3bd4432c8b42fbce93ede8ffd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770870"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Använd Java för att skicka händelser till eller ta emot händelser från Azure Event Hubs (azure-messaging-eventhubs)
Den här snabbstarten visar hur du skickar händelser till och ta emot händelser från en händelsehubb med **java-paketet Azure-messaging-eventhubs.**

> [!IMPORTANT]
> Den här snabbstarten använder det nya **azure-messaging-eventhubs-paketet.** En snabbstart som använder de gamla **azure-eventhubs-** och **azure-eventhubs-eph-paketen** finns i [Skicka och ta emot händelser med azure-eventhubs och azure-eventhubs-eph](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Krav
Om du inte har gjort det tidigare i Azure Event Hubs läser du [översikt över eventhubbar](event-hubs-about.md) innan du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure-prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) eller använda dina msdn-prenumerationsförmåner när du [skapar ett konto](https://azure.microsoft.com).
- En Java-utvecklingsmiljö. Den här snabbstarten använder [Eclipse](https://www.eclipse.org/). Java Development Kit (JDK) med version 8 eller högre krävs. 
- **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Det första steget är att använda [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutningssträngen för namnområdet Event Hubs** genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutningssträngen senare i den här snabbstarten.

## <a name="send-events"></a>Skicka händelser 
I det här avsnittet visas hur du skapar ett Java-program för att skicka händelser till en händelsehubb. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Lägga till referens till Azure Event Hubs-bibliotek

Java-klientbiblioteket för eventhubbar är tillgängligt i [Mavens centrala databas](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Du kan referera till det här biblioteket med hjälp av följande beroendedeklaration i maven-projektfilen:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Lägg till `Sender`en klass med namnet och lägg till följande kod i klassen:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Anslutningssträng och händelsehubb
Den här koden använder anslutningssträngen till namnområdet Event Hubs och namnet på händelsehubben för att skapa en Event Hubs-klient. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Skapa en eventhubs-producentklient 
Den här koden skapar ett producentklientobjekt som används för att skapa/skicka händelser till händelsehubben. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Förbereda en grupp händelser
Den här koden förbereder en grupp händelser. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Skicka bunten med händelser till händelsehubben
Den här koden skickar den uppsättning händelser som du har förberett i föregående steg till händelsehubben. Följande kodblock för sändningsåtgärden. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Stänga och rensa
Denna kod stänger producenten. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Fullständig kod för att skicka händelser
Här är den fullständiga koden för att skicka händelser till händelsehubben. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Bygg programmet och se till att det inte finns några fel. Du kör det här programmet när du har kört mottagarprogrammet. 

## <a name="receive-events"></a>Ta emot händelser
Koden i den här självstudien baseras på [exemplet EventProcessorClient på GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), som du kan undersöka för att se hela arbetsprogrammet.

> [!NOTE]
> Om du körs på Azure Stack Hub kan den plattformen ha stöd för en annan version av Storage Blob SDK än de som vanligtvis är tillgängliga på Azure. Om du till exempel kör [på Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagringstjänsten version 2017-11-09. I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod för att rikta lagringstjänstens API-version 2017-11-09. Ett exempel på hur du inriktar dig på en specifik storage API-version finns i [det här exemplet på GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). Mer information om Azure Storage-tjänstversionerna som stöds på Azure Stack Hub finns i [Azure Stack Hub storage: Skillnader och överväganden](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-a-java-project"></a>Skapa ett Java-projekt

Java-klientbiblioteket för eventhubbar är tillgängligt för användning i Maven-projekt från [Mavens centrala databas](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)och kan refereras med hjälp av följande beroendedeklaration i din Maven-projektfil: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Använd följande kod för att skapa en ny klass som kallas `Receiver`. Ersätt platshållarna med de värden som användes när du skapade händelsehubben och lagringskontot:
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new SampleCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. Hämta **filen SampleCheckpointStore.java** från [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/SampleCheckpointStore.java)och lägg till den i projektet. 
3. Bygg programmet och se till att det inte finns några fel. 

## <a name="run-the-applications"></a>Köra programmen
1. Kör **mottagarprogrammet** först.
1. Kör sedan **avsänarprogrammet.** 
1. I **fönstret mottagarprogram** bekräftar du att du ser de händelser som har publicerats av avsändningsprogrammet.
1. Tryck **på RETUR** i mottagarprogramfönstret för att stoppa programmet. 

## <a name="next-steps"></a>Nästa steg
Kolla in [Java SDK-exempel på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

