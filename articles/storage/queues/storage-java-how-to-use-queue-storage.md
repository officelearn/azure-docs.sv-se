---
title: Använda Queue Storage från Java-Azure Storage
description: Lär dig hur du använder Azure-Kötjänst för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden. Exempel som skrivits i Java.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/08/2016
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 5d2bd94f6b95ff993ae367b99f48746c091f5739
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721418"
---
# <a name="how-to-use-queue-storage-from-java"></a>Använda Queue Storage från Java
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Översikt
I den här guiden får du lära dig hur du utför vanliga scenarier med Azure Queue Storage-tjänsten. Exemplen är skrivna i Java och använder [Azure Storage SDK för Java][Azure Storage SDK for Java]. De scenarier som beskrivs är att **Infoga**, **Granska**, **Hämta**och **ta bort** Kömeddelanden, samt **skapa** och **ta bort** köer. Mer information om köer finns i avsnittet [Nästa steg](#next-steps) .

Anteckning: En SDK finns tillgänglig för utvecklare som använder Azure Storage på Android-enheter. Mer information finns i [Azure Storage SDK för Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Skapa ett Java-program
I den här guiden ska du använda lagrings funktioner som kan köras i ett Java-program lokalt, eller i kod som körs i en webb roll eller arbets roll i Azure.

För att göra det måste du installera Java Development Kit (JDK) och skapa ett Azure Storage-konto i din Azure-prenumeration. När du har gjort det måste du kontrol lera att utvecklings systemet uppfyller minimi kraven och beroendena som anges i [Azure Storage SDK för Java][Azure Storage SDK for Java] -lagringsplatsen på GitHub. Om systemet uppfyller dessa krav kan du följa anvisningarna för att ladda ned och installera Azure Storage biblioteken för java i systemet från den lagrings platsen. När du har slutfört dessa uppgifter kan du skapa ett Java-program som använder exemplen i den här artikeln.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera ditt program till att komma åt Queue Storage
Lägg till följande import-instruktioner överst i Java-filen där du vill använda Azure Storage-API: er för att komma åt köer:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurera en anslutnings sträng för Azure Storage
En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När du kör i ett klient program måste du ange lagrings anslutnings strängen i följande format med namnet på ditt lagrings konto och den primära åtkomst nyckeln för det lagrings konto som anges i [Azure Portal](https://portal.azure.com) för *AccountName* och *AccountKey* -värden. Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

I ett program som körs i en roll i Microsoft Azure, kan den här strängen lagras i tjänst konfigurations filen, *ServiceConfiguration. cscfg*och kan nås med ett anrop till metoden **RoleEnvironment. getConfigurationSettings** . Här är ett exempel på hur anslutningssträngen från ett **inställningselement** med namnet *StorageConnectionString* i tjänstkonfigurationsfilen kan hämtas:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.

## <a name="how-to-create-a-queue"></a>Anvisningar: Skapa en kö
Med ett **CloudQueueClient** -objekt kan du hämta referens objekt för köer. Följande kod skapar ett **CloudQueueClient** -objekt. (Obs: Det finns ytterligare sätt att skapa **CloudStorageAccount** -objekt; Mer information finns i **CloudStorageAccount** i [Azure Storage Client SDK-referens].)

Använd **CloudQueueClient** -objektet för att få en referens till den kö som du vill använda. Du kan skapa kön om den inte finns.

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

## <a name="how-to-add-a-message-to-a-queue"></a>Anvisningar: Lägga till ett meddelande i en kö
Om du vill infoga ett meddelande i en befintlig kö börjar du med att skapa ett nytt **CloudQueueMessage**. Anropa sedan metoden **addMessage** . En **CloudQueueMessage** kan skapas från antingen en sträng (i UTF-8-format) eller en byte mat ris. Här är kod som skapar en kö (om den inte finns) och infogar meddelandet "Hello, World".

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
Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa **peekMessage**.

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

Följande kod exempel söker igenom kön med meddelanden, letar upp det första meddelandet som matchar "Hello, World" för innehållet och ändrar sedan meddelandets innehåll och avslutar.

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

Du kan också uppdatera följande kod exempel bara det första synliga meddelandet i kön.

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
Du kan hämta en uppskattning av antalet meddelanden i en kö. Metoden **downloadAttributes** frågar kötjänst efter flera aktuella värden, inklusive ett antal av hur många meddelanden som finns i en kö. Antalet är bara ungefärlig eftersom meddelanden kan läggas till eller tas bort när Kötjänst svarar på din begäran. Metoden **getApproximateMessageCount** returnerar det sista värdet som hämtades av anropet till **downloadAttributes**, utan att kötjänst anropas.

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

## <a name="how-to-dequeue-the-next-message"></a>Anvisningar: Ta bort nästa meddelande i kö
Din kod avstår ett meddelande från en kö i två steg. När du anropar **retrieveMessage**får du nästa meddelande i en kö. Ett meddelande som returnerades från **retrieveMessage** blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa **deleteMessage**. Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Din kod anropar **deleteMessage** direkt efter att meddelandet har bearbetats.

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

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för demsmq-meddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande.

I följande kod exempel används metoden **retrieveMessages** för att få 20 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en **for** -slinga. Den anger också timeout för insikter till fem minuter (300 sekunder) för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, så när fem minuter har passerat sedan anropet till **retrieveMessages**, kommer alla meddelanden som inte har tagits bort att bli synliga igen.

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

## <a name="how-to-list-the-queues"></a>Anvisningar: Visa en lista över köer
Om du vill hämta en lista över aktuella köer anropar du metoden **CloudQueueClient. listQueues ()** som returnerar en samling **CloudQueue** -objekt.

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
Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden **deleteIfExists** på **CloudQueue** -objektet.

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
Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter.

* [Azure Storage SDK för Java][Azure Storage SDK for Java]
* [Azure Storage Client SDK-referens][Azure Storage Client SDK Reference]
* [REST-API för Azure Storage Services][Azure Storage Services REST API]
* [Azure Storage Teamblogg][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK-referens]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
