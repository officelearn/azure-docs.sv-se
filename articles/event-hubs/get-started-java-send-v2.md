---
title: Skicka eller ta emot händelser från Azure Event Hubs med Java (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett Java-program som skickar/tar emot händelser till/från Azure Event Hubs med det senaste paketet Azure-Messaging-eventhubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 69c994eed0907c8bb46f47016769e2078432ee99
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85320077"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Använda Java för att skicka händelser till eller ta emot händelser från Azure Event Hubs (Azure-Messaging-eventhubs)
Den här snabb starten visar hur du skickar händelser till och tar emot händelser från en händelsehubben med hjälp av Java **-paketet Azure-Messaging-eventhubs** .

> [!IMPORTANT]
> I den här snabb starten används det nya paketet **Azure-Messaging-eventhubs** . En snabb start som använder gamla **Azure-eventhubs** -och **Azure-eventhubs-EPH-** paket finns i [skicka och ta emot händelser med Azure-eventhubs och Azure-eventhubs-EPH](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Krav
Om du inte har använt Azure Event Hubs tidigare, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- En Java Development Environment. Den här snabb starten använder [Sol förmörkelse](https://www.eclipse.org/). Java Development Kit (JDK) med version 8 eller senare krävs. 
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutnings strängen för Event Hubs namn området genom att** följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutnings strängen senare i den här snabb starten.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett Java-program för att skicka händelser till en Event Hub. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Lägg till en referens till Azure Event Hubs-biblioteket

Java-klientens bibliotek för Event Hubs är tillgängligt i [maven Central-lagringsplatsen](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Du kan referera till det här biblioteket med hjälp av följande beroende deklaration i Maven-projekt filen:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Lägg till en klass med namnet `Sender` och Lägg till följande kod i klassen:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Anslutnings sträng och händelsehubben
Den här koden använder anslutnings strängen för Event Hubs namn området och namnet på händelsehubben för att bygga en Event Hubs-klient. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Skapa en Event Hubs Producer-klient 
Den här koden skapar ett producent klient objekt som används för att skapa/skicka händelser till händelsehubben. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Förbereda en grupp händelser
Den här koden förbereder en batch med händelser. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Skicka en händelse grupp till händelsehubben
Den här koden skickar den grupp med händelser som du för beredde i föregående steg till Event Hub. Följande kodblock i sändnings åtgärden. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Stäng och rensa
Den här koden stänger producenten. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Slutför kod för att skicka händelser
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

Bygg programmet och se till att det inte finns några fel. Du kör det här programmet när du har kört mottagar programmet. 

## <a name="receive-events"></a>Ta emot händelser
Koden i den här självstudien baseras på [EventProcessorClient-exemplet på GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), som du kan granska för att se hela det fungerande programmet.

> [!NOTE]
> Om du kör på Azure Stack hubb, kan plattformen stödja en annan version av Storage BLOB SDK än vad som normalt är tillgängligt på Azure. Om du till exempel kör [på Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagrings tjänsten version 2017-11-09. I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod som mål för Storage Service API-versionen 2017-11-09. Ett exempel på hur du riktar in en speciell Storage API-version finns i [det här exemplet på GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). Mer information om Azure Storage tjänst versioner som stöds på Azure Stack Hub finns i [Azure Stack hubb lagring: skillnader och överväganden](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-a-java-project"></a>Skapa ett Java-projekt

Java-klientens bibliotek för Event Hubs är tillgängligt för användning i Maven-projekt från [maven Central-lagringsplatsen](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)och kan refereras till med hjälp av följande beroende deklaration i din Maven-projekt fil: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Använd följande kod för att skapa en ny klass som kallas `Receiver`. Ersätt plats hållarna med de värden som används när du skapade händelsehubben och lagrings kontot:
   
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
    
2. Hämta filen **SampleCheckpointStore. java** från [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/SampleCheckpointStore.java)och Lägg till den i projektet. 
3. Bygg programmet och se till att det inte finns några fel. 

## <a name="run-the-applications"></a>Köra programmen
1. Kör **mottagar** programmet först.
1. Kör sedan **avsändar** programmet. 
1. I fönstret för **mottagar** programmet bekräftar du att du ser de händelser som publicerades av avsändar programmet.
1. Tryck på **RETUR** i fönstret för mottagar program för att stoppa programmet. 

## <a name="next-steps"></a>Nästa steg
Kolla [in Java SDK-exempel på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

