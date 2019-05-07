---
title: Hur du använder Queue storage från Java – Azure Storage
description: Lär dig hur du använder Azure-Kötjänsten för att skapa och ta bort köer, infoga, hämta och ta bort meddelanden. Exemplen är skrivna i Java.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 12/08/2016
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 3cbd1445640f37cbc63d74d1366c390c774aecd5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151115"
---
# <a name="how-to-use-queue-storage-from-java"></a>Använda Queue Storage från Java
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med Azure Queue storage-tjänsten. Exemplen är skrivna i Java och använder [Azure Storage SDK för Java][Azure Storage SDK for Java]. Scenarier som omfattas är **infoga**, **granskning**, **komma**, och **tar bort** köa meddelanden, samt  **Skapa** och **tar bort** köer. Mer information om köer finns i den [nästa steg](#next-steps) avsnittet.

Obs! En SDK finns tillgänglig för utvecklare som använder Azure Storage på Android-enheter. Mer information finns i [Azure Storage SDK för Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Skapa ett Java-program
I den här guiden använder lagringsfunktioner som kan köras i ett Java-program lokalt eller i koden som körs i en web-roll eller worker-roll i Azure.

Du vill göra det behöver du installera Java Development Kit (JDK) och skapa ett Azure storage-konto i Azure-prenumerationen. När du har gjort det, behöver du kontrollera att utvecklingssystemet uppfyller de minimikrav och beroenden som anges i den [Azure Storage SDK för Java] [ Azure Storage SDK for Java] arkivet på GitHub. Om datorn uppfyller dessa krav, kan du följa anvisningarna för att ladda ned och installera Azure Storage-bibliotek för Java på datorn från den lagringsplatsen. När du har slutfört uppgifterna, kommer du att kunna skapa en Java-program som använder exemplen i den här artikeln.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera programmet för att komma åt kölagring
Lägg till följande importuttryck längst upp i Java-filen där du vill använda API: er för Azure storage för att få åtkomst till köer:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurera en anslutningssträng för Azure storage
En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När du kör i ett klientprogram kan du måste ange anslutningssträng för lagring i följande format, med hjälp av namnet på ditt lagringskonto och den primära åtkomstnyckeln för lagringskontot som anges i den [Azure-portalen](https://portal.azure.com) för den *AccountName* och *AccountKey* värden. Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

I ett program som körs inom en roll i Microsoft Azure, kan den här strängen lagras i tjänstekonfigurationsfilen, *ServiceConfiguration.cscfg*, och kan nås med ett anrop till den  **RoleEnvironment.getConfigurationSettings** metod. Här är ett exempel på hur anslutningssträngen från ett **inställningselement** med namnet *StorageConnectionString* i tjänstkonfigurationsfilen kan hämtas:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.

## <a name="how-to-create-a-queue"></a>Anvisningar: Skapa en kö
En **CloudQueueClient** objektet kan användas för att hämta referensobjekt för köer. Följande kod skapar en **CloudQueueClient** objekt. (Obs: Det finns ytterligare sätt att skapa **CloudStorageAccount** objekt; mer information finns i **CloudStorageAccount** i den [Azure Storage Client SDK-referens].)

Använd den **CloudQueueClient** objekt för att hämta en referens till den kö som du vill använda. Du kan skapa kön om det inte finns.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>Anvisningar: Lägg till ett meddelande till en kö
Om du vill infoga ett meddelande i en befintlig kö börjar du med att skapa ett nytt **CloudQueueMessage**. Därefter anropar den **addMessage** metod. En **CloudQueueMessage** kan skapas från en sträng (i UTF-8-format) eller en bytematris. Här är kod som skapar en kö (om den inte finns) och som infogar meddelandet ”Hello, World”.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>Anvisningar: En titt på nästa meddelande
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa **peekMessage**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Anvisningar: Ändra innehållet i ett meddelande i kön
Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar kömeddelandet med nytt innehåll och utökar tidsgränsen för visning med ytterligare 60 sekunder. Koden sparar statusen för arbetsuppgiften som associeras med meddelandet och ger klienten ytterligare en minut att fortsätta arbeta med meddelandet. Du kan använda den här tekniken för att spåra arbetsflöden med flera steg i kömeddelanden, utan att behöva börja om från början om ett bearbetningssteg misslyckas på grund av maskin- eller programvarufel. Normalt räknar du även antalet omförsök och tar bort meddelandet om fler än *n* försök misslyckas. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

I följande exempel på sökningar i kod via kön för meddelanden, söker efter det första meddelandet som motsvarar ”Hello, World” för innehåll, och sedan ändrar meddelandet innehåll och avslutas.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Följande kodexempel uppdaterar du kan också bara det första visas meddelandet i kön.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>Anvisningar: Hämta kölängden
Du kan hämta en uppskattning av antalet meddelanden i en kö. Den **downloadAttributes** metoden ber Kötjänsten för flera aktuella värden, inklusive hur många meddelanden som finns i en kö. Antalet är ungefärliga eftersom meddelanden kan läggas till eller tas bort efter kötjänsten svarar på din begäran. Den **getApproximateMessageCount** metoden returnerar det sista värdet som hämtas av anropet till **downloadAttributes**, utan att kötjänsten.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>Anvisningar: Ta bort från kön nästa meddelande
Din kod dequeues ett meddelande från en kö i två steg. När du anropar **retrieveMessage**, du får nästa meddelande i en kö. Ett meddelande som returneras från **retrieveMessage** blir osynligt för andra kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön, måste du även anropa **deleteMessage**. Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Koden anropar **deleteMessage** direkt efter att meddelandet har bearbetats.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för meddelanden mellan köer
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande.

Följande kodexempel används den **retrieveMessages** metod för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **för** loop. Den anger också in tidsgränsen för osynlighet till fem minuter (300 sekunder) för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, så när fem minuter sedan anropet till **retrieveMessages**, alla meddelanden som inte har tagits bort kommer att bli synliga igen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>Anvisningar: Lista köer
För att få en lista över de aktuella köerna kan anropa den **CloudQueueClient.listQueues()** metod som returnerar en samling **CloudQueue** objekt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>Anvisningar: Ta bort en kö
Ta bort en kö och alla meddelanden som finns i den genom att anropa den **deleteIfExists** metoden på den **CloudQueue** objekt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i queue storage kan du följa dessa länkar om du vill lära dig mer komplexa lagringsuppgifter.

* [Azure Storage SDK för Java][Azure Storage SDK for Java]
* [Azure Storage Client SDK-referens][Azure Storage Client SDK-referens]
* [Azure Storage Services REST API][Azure Storage Services REST API]
* [Azure Storage-teamets blogg][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK-referens]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
