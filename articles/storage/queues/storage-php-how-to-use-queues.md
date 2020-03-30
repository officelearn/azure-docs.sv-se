---
title: Så här använder du kölagring från PHP - Azure Storage
description: Lär dig hur du använder azure queue storage-tjänsten för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden. Prover är skrivna i PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 692c943e48c08771b5f1c60b66412270081cf0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302965"
---
# <a name="how-to-use-queue-storage-from-php"></a>Använda Queue Storage från PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Den här guiden visar hur du utför vanliga scenarier med hjälp av Azure Queue storage-tjänsten. Exemplen skrivs via klasser från [Azure Storage Client Library för PHP][download]. Scenarierna som omfattas omfattar att infoga, granska, hämta och ta bort kömeddelanden samt skapa och ta bort köer.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Skapa ett PHP-program

Det enda kravet för att skapa ett PHP-program som har åtkomst till Azure Queue storage är hänvisningen till klasser i [Azure Storage Client Library för PHP][download] inifrån koden. Du kan använda valfritt utvecklingsverktyg för att skapa programmet, inklusive Anteckningar.

I den här guiden använder du funktionerna för kölagringstjänst som kan anropas i ett PHP-program lokalt eller i kod som körs i ett webbprogram i Azure.

## <a name="get-the-azure-client-libraries"></a>Hämta Azure-klientbiblioteken

### <a name="install-via-composer"></a>Installera via Composer

1. Skapa en fil med namnet **composer.json** i projektets rot och lägg till följande kod i den:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Ladda ner **[composer.phar][composer-phar]** i projektroten.
3. Öppna en kommandotolk och kör följande kommando i projektroten
   
    ```
    php composer.phar install
    ```

Du kan också gå till [Azure Storage PHP-klientbiblioteket][download] på GitHub för att klona källkoden.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera ditt program för åtkomst till kölagring

Om du vill använda API:erna för Azure Queue storage måste du:

1. Referera till autoloader-filen [require_once] med hjälp av require_once-satsen.
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du inkluderar filen för automatisk inläsning och refererar till klassen **QueueRestProxy.**

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

I följande exempel `require_once` visas satsen alltid, men endast de klasser som är nödvändiga för att köra exempel refereras.

## <a name="set-up-an-azure-storage-connection"></a>Konfigurera en Azure-lagringsanslutning

Om du vill instansiera en Azure Queue storage-klient måste du först ha en giltig anslutningssträng. Formatet för anslutningssträngen för kötjänsten är följande.

För åtkomst till en live-tjänst:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

För åtkomst till emulatorlagringen:

```php
UseDevelopmentStorage=true
```

Om du vill skapa en Azure Queue-tjänstklient måste du använda klassen **QueueRestProxy.** Du kan använda någon av följande tekniker:

* Skicka anslutningssträngen direkt till den.
* Använd miljövariabler i Web App för att lagra anslutningssträngen. Se [Azure web app konfigurationsinställningar](../../app-service/configure-common.md) dokument för att konfigurera anslutningssträngar.
I exemplen som beskrivs här anges anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Skapa en kö

Med ett **QueueRestProxy-objekt** kan du skapa en kö med hjälp av metoden **createQueue.** När du skapar en kö kan du ange alternativ i kön, men det krävs inte. (Exemplet nedan visar hur du ställer in metadata i en kö.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Du bör inte förlita dig på skiftlägeskänslighet för metadatanycklar. Alla tangenter läss från tjänsten i gemener.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö

Om du vill lägga till ett meddelande i en kö använder du **QueueRestProxy->createMessage**. Metoden tar könamnet, meddelandetexten och meddelandealternativen (som är valfria).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>En titt på nästa meddelande

Du kan granska ett meddelande (eller meddelanden) längst fram i en kö utan att ta bort det från kön genom att ringa **QueueRestProxy->peekMessages**. Som standard **returnerar peekMessage-metoden** ett enda meddelande, men du kan ändra det värdet med hjälp av metoden **PeekMessagesOptions->setNumberOfMessages.**

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Ta bort nästa meddelande från kön

Koden tar bort ett meddelande från en kö i två steg. Först ringer du **QueueRestProxy->listMessages**, vilket gör meddelandet osynligt för alla andra koder som läser från kön. Som standard är det här meddelandet osynligt i 30 sekunder. (Om meddelandet inte tas bort under den här tidsperioden visas det i kön igen.) Om du vill ta bort meddelandet från kön måste du anropa **QueueRestProxy->deleteMessage**. Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att när koden inte bearbetar ett meddelande på grund av maskinvaru- eller programvarufel kan en annan instans av koden få samma meddelande och försöka igen. Dina kodanrop **tar bortMessage** direkt efter att meddelandet har bearbetats.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande på plats i kön genom att anropa **QueueRestProxy->updateMessage**. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar kömeddelandet med nytt innehåll och anger att tidsgränsen för synligheten ska sträckas ut ytterligare 60 sekunder. Detta sparar tillståndet för arbete som är associerat med meddelandet och ger klienten en minut att fortsätta arbeta med meddelandet. Du kan använda den här tekniken för att spåra arbetsflöden med flera steg i kömeddelanden, utan att behöva börja om från början om ett bearbetningssteg misslyckas på grund av maskin- eller programvarufel. Normalt räknar du även antalet omförsök och tar bort meddelandet om fler än *n* försök misslyckas. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Ytterligare alternativ för deköeringsmeddelanden

Det finns två sätt att anpassa meddelandehämtning från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ställa in en längre eller kortare tidsgränsen för synlighet, så att koden mer eller mindre tid kan bearbetas fullständigt i varje meddelande. I följande kodexempel används metoden **getMessages** för att hämta 16 meddelanden i ett samtal. Sedan bearbetar varje meddelande med hjälp av en **för** slinga. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Hämta kölängd

Du kan hämta en uppskattning av antalet meddelanden i en kö. **Metoden QueueRestProxy->getQueueMetadata** ber kötjänsten att returnera metadata om kön. Anropa metoden **getApproximateMessageCount** på det returnerade objektet ger en räkning av hur många meddelanden som finns i en kö. Antalet är bara ungefärlig eftersom meddelanden kan läggas till eller tas bort när kötjänsten svarar på din begäran.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Ta bort en kö

Om du vill ta bort en kö och alla meddelanden i den anropar du **metoden QueueRestProxy->deleteQueue.**

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Azure Queue storage följer du de här länkarna för att lära dig mer komplexa lagringsuppgifter:

* Besök [API-referensen för Azure Storage PHP-klientbiblioteket](https://azure.github.io/azure-storage-php/)
* Se [exemplet avancerad kö](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Mer information finns i [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
