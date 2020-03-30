---
title: 'Snabbstart: Azure Queue storage library v12 - Java'
description: Lär dig hur du använder Java v12-biblioteket i Azure Queue för att skapa en kö och lägga till meddelanden i kön. Därefter får du lära dig att läsa och ta bort meddelanden från kön. Du får också lära dig hur du tar bort en kö.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 9cfedd322db721156584844e949724ab2d104968
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199809"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Snabbstart: Azure Queue storage client library v12 för Java

Kom igång med Azure Queue storage client library version 12 för Java. Azure Queue storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ dessa steg för att installera paketet och prova exempelkod för grundläggande uppgifter.

Använd Azure Queue storage client library v12 för Java för att:

* Skapa en kö
* Lägga till meddelanden i en kö
* Granska meddelanden i en kö
* Uppdatera ett meddelande i en kö
* Ta emot och ta bort meddelanden från en kö
* Ta bort en kö

[Api-referensdokumentation](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | [Mällkodpaket](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) | [(Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | [Exempel](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Krav

* [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) version 8 eller senare
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
* Azure storage-konto - [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Inrätta

I det här avsnittet får du hjälp med att förbereda ett projekt för att arbeta med Azure Queue storage client library v12 för Java.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Java-program med namnet *queues-quickstart-v12*.

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du Maven för att skapa en ny konsolapp med *namnköerna-quickstart-v12*. Skriv följande **mvn** kommando för att skapa en "Hello world!" Java-projektet.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Utdata från att generera projektet bör se ut ungefär så här:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Växla till den nyskapade *kökatalogen-quickstart-v12.*

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Installera paketet

Öppna *filen pom.xml* i textredigeraren. Lägg till följande beroendeelement i gruppen av beroenden.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

Från projektkatalogen:

1. Navigera till *snabbstartskatalogen /src/main/java/com/queues/quickstart*
1. Öppna *filen App.java* i redigeraren
1. Ta `System.out.println("Hello world!");` bort utdraget
1. Lägga `import` till direktiv

Här är koden:

```java
package com.queues.quickstart;

/**
 * Azure queue storage v12 SDK quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett kömeddelande kan vara upp till 64 kB i storlek. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning av arbete för att bearbeta asynkront. Kölagring erbjuder tre typer av resurser:

* Lagringskontot
* En kö i lagringskontot
* Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över lagringsarkitektur för kö](./media/storage-queues-introduction/queue1.png)

Använd följande Java-klasser för att interagera med dessa resurser:

* [QueueClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html): `QueueClientBuilder` Klassen konfigurerar och `QueueClient` instansierar ett objekt.
* [QueueServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html): `QueueServiceClient` Med möjlighet att hantera alla köer i ditt lagringskonto.
* [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html): `QueueClient` Klassen låter dig hantera och manipulera en enskild kö och dess meddelanden.
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html): `QueueMessageItem` Klassen representerar de enskilda objekt som returneras när du [anropar mottaMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) i en kö.

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitten hur du utför följande åtgärder med Azure Queue storage-klientbiblioteket för Java:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en kö](#create-a-queue)
* [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
* [Granska meddelanden i en kö](#peek-at-messages -in-a-queue)
* [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
* [Ta emot och ta bort meddelanden från en kö](#receive-and-delete-messages-from-a-queue)
* [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot. Anslutningssträngen lagras den miljövariabel som skapas i avsnittet [Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den `main` här koden i metoden:

```java
System.out.println("Azure Queues storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Skapa en kö

Bestäm ett namn för den nya kön. Koden nedan lägger till ett GUID-värde i könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemener, siffror och bindestreck och måste börja med en bokstav eller ett tal. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste också vara mellan 3 och 63 tecken långt. Mer information om namngivning av köer finns i [Namnge köer och metadata](/rest/api/storageservices/naming-queues-and-metadata).


Skapa en instans av klassen [QueueClient.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) Anropa sedan [metoden create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) för att skapa kön i ditt lagringskonto.

Lägg till den här `main` koden i slutet av metoden:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Lägga till meddelanden i en kö

Följande kodavsnitt lägger till meddelanden i kö genom att anropa [metoden sendMessage.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) Det sparar också en [SendMessageResult](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html) `sendMessage` tillbaka från ett samtal. Resultatet används för att uppdatera meddelandet senare i programmet.

Lägg till den här `main` koden i slutet av metoden:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Kika på meddelandena i kön genom att anropa [peekMessages-metoden.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) Metoden `peelkMessages` hämtar ett eller flera meddelanden från framsidan av kön men ändrar inte meddelandets synlighet.

Lägg till den här `main` koden i slutet av metoden:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa [metoden updateMessage.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) Metoden `updateMessage` kan ändra ett meddelandes synlighetstidsutgång och innehåll. Meddelandeinnehållet måste vara en UTF-8-kodad sträng som är upp till 64 kB i storlek. Tillsammans med nytt innehåll för meddelandet skickar du in meddelande-ID:t och popkvittot med hjälp av `SendMessageResult` det som sparades tidigare i koden. Meddelande-ID och pop kvitto identifiera vilket meddelande som ska uppdateras.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Ta emot och ta bort meddelanden från en kö

Hämta tidigare tillagda meddelanden genom att anropa metoden [receiveMessages.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) Exempelkoden tar också bort meddelanden från kön när de har tagits emot och bearbetats. I det här fallet är bearbetning bara visar meddelandet på konsolen.

Appen pausar för användarindata genom att ringa `System.console().readLine();` innan den tar emot och tar bort meddelandena. Kontrollera i din [Azure-portal](https://portal.azure.com) att resurserna har skapats korrekt innan de tas bort. Alla meddelanden som inte uttryckligen tas bort kommer så småningom att bli synliga i kön igen för en ny chans att bearbeta dem.

Lägg till den här `main` koden i slutet av metoden:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Ta bort en kö

Följande kod rensar de resurser som appen har skapat genom att ta bort kön med [borttagningsmetoden.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--)

Lägg till den här `main` koden i slutet av metoden:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. Koden visar meddelandena i kön och hämtar och tar sedan bort dem innan du slutligen tar bort kön.

I konsolfönstret navigerar du till programkatalogen och skapar och kör programmet.

```console
mvn compile
```

Bygg sedan paketet.

```console
mvn package
```

Kör följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Utdata för appen liknar följande exempel:

```output
Azure Queues storage v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

När appen pausas innan du tar emot meddelanden kontrollerar du ditt lagringskonto i [Azure-portalen](https://portal.azure.com). Kontrollera att meddelandena finns i kön.

Tryck på **Retur** för att ta emot och ta bort meddelandena. När du uppmanas till det trycker du på **Retur** igen för att ta bort kön och slutför demon.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar en kö och lägger till meddelanden i den med Java-kod. Sedan lärde du dig att granska, hämta och ta bort meddelanden. Slutligen har du lärt dig hur du tar bort en meddelandekö.

För självstudier, exempel, snabbstarter och annan dokumentation, besök:

> [!div class="nextstepaction"]
> [Azure för Java-molnutvecklare](https://docs.microsoft.com/azure/java/)

* Om du vill se fler exempelappar för Azure Queue storage fortsätter du till [Azure Queue storage SDK v12 Java-klientbiblioteksexempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
