---
title: Använda Queue storage från PHP | Microsoft Docs
description: Lär dig hur du använder tjänsten Azure Queue storage för att skapa och ta bort köer, infoga, hämta och ta bort meddelanden. Exempel är skrivna i PHP.
documentationcenter: php
services: storage
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7582b208-4851-4489-a74a-bb952569f55b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 01/11/2018
ms.author: rogarana
ms.openlocfilehash: 737054aefa684b3fc486c860253c69afbb6eaf0c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-queue-storage-from-php"></a>Använda Queue Storage från PHP
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Azure Queue storage-tjänsten. Exemplen är skrivna via klasser från den [Azure Storage-klientbibliotek för PHP][download]. Skyddad scenarier är Infoga, granskning, hämtar, och tar bort Kömeddelanden, samt hur du skapar och tar bort köer.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Skapa en PHP-program
Det enda kravet för att skapa en PHP-program som har åtkomst till Azure Queue storage är refererar till klasserna i den [Azure Storage-klientbibliotek för PHP] [ download] från inom din kod. Du kan använda alla utvecklingsverktyg för att skapa programmet, inklusive anteckningar.

I den här guiden kan du använda Queue storage tjänstens funktioner som kan anropas i en PHP-program lokalt eller i koden körs i en Azure-webbroll, en arbetsroll eller en webbplats.

## <a name="get-the-azure-client-libraries"></a>Hämta Azures klientbibliotek
### <a name="install-via-composer"></a>Installera via Composer
1. Skapa en fil med namnet **composer.json** i roten av projektet och Lägg till följande kod:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Hämta **[composer.phar] [ composer-phar]** i projektroten.
3. Öppna en kommandotolk och kör följande kommando i projektroten
   
    ```
    php composer.phar install
    ```

Du kan också gå till den [Azure Storage-klientbibliotek PHP] [ download] på GitHub att klona källkoden.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera ditt program för att komma åt Queue storage
Om du vill använda API: erna för Azure Queue storage, måste du:

1. Referera till bandladdaren-filen med hjälp av den [require_once] instruktionen.
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du lägger till den automatiska bandladdaren fil- och referens av **QueueRestProxy** klass.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

I följande exempel på `require_once` instruktionen visas alltid, men de klasser som krävs för att köra refereras.

## <a name="set-up-an-azure-storage-connection"></a>Skapa en Azure storage-anslutning
Om du vill skapa en instans av en Azure Queue storage-klient, måste du ha en giltig anslutningssträng. Formatet för anslutningssträngen för kön service är som följer.

För att komma åt en live-tjänst:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

För att komma åt lagringsplatsen emulatorn:

```php
UseDevelopmentStorage=true
```

Om du vill skapa ett Azure Queue-tjänstklienten, måste du använda den **QueueRestProxy** klass. Du kan använda någon av följande metoder:

* Skicka anslutningssträngen till den.
* Använda miljövariabler i ditt webbprogram för att lagra anslutningssträngen. Se [Azure web appkonfigurationsinställningar](../../app-service/web-sites-configure.md) dokument för att konfigurera anslutningssträngar.
Exempel som beskrivs här skickas anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Skapa en kö
En **QueueRestProxy** objekt kan du skapa en kö med hjälp av den **createQueue** metod. När du skapar en kö kan du ange alternativ för kön, men detta så krävs inte. (I exemplet nedan visar hur du anger metadata för en kö).

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Du bör inte förlita dig på skiftlägeskänslighet för metadata nycklar. Alla nycklar läses från tjänsten med små bokstäver.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande till en kö
Om du vill lägga till ett meddelande till en kö, använda **QueueRestProxy -> createMessage**. Metoden tar könamnet, meddelandetext och meddelandealternativ (som är valfritt).

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>En titt på nästa meddelande
Du kan kika på meddelandet (eller meddelanden) i början av en kö utan att ta bort det från kön genom att anropa **QueueRestProxy -> peekMessages**. Som standard den **peekMessage** metoden returnerar ett enda meddelande, men du kan ändra värdet genom att använda den **PeekMessagesOptions -> setNumberOfMessages** metod.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
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
Koden tar bort ett meddelande från en kö i två steg. Först måste du anropa **QueueRestProxy -> listMessages**, vilket gör att meddelandet osynligt för andra kod som läser från kön. Som standard är det här meddelandet osynligt i 30 sekunder. (Om meddelandet inte tas bort i den här tiden, den blir synlig i kön igen.) Om du vill slutföra borttagningen av meddelandet från kön, måste du anropa **QueueRestProxy -> deleteMessage**. Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att när din kod inte kan bearbeta ett meddelande på grund av maskinvara eller programvara, kan en annan instans av koden hämta samma meddelande och försök igen. Koden anropar **deleteMessage** direkt efter att meddelandet har bearbetats.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön
Du kan ändra innehållet i ett meddelande direkt i kön genom att anropa **QueueRestProxy -> updateMessage**. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar kömeddelandet med nytt innehåll och anger att tidsgränsen för visning utöka ytterligare 60 sekunder. Detta sparar statusen för arbetsuppgiften som är associerad med meddelandet och det ger klienten ytterligare en minut att fortsätta arbeta med meddelandet. Du kan använda den här tekniken för att spåra arbetsflöden med flera steg i kömeddelanden, utan att behöva börja om från början om ett bearbetningssteg misslyckas på grund av maskin- eller programvarufel. Normalt räknar du även antalet omförsök och tar bort meddelandet om fler än *n* försök misslyckas. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Ytterligare alternativ för meddelanden ur kön
Det finns två sätt som du kan anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). Dessutom kan du ange en längre eller kortare synlighet tidsgräns att ge koden mer eller mindre tid att bearbeta klart varje meddelande. Följande kodexempel används den **GetMessage** metod för att hämta 16 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **för** loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Hämta kölängden
Du kan hämta en uppskattning av antalet meddelanden i en kö. Den **QueueRestProxy -> getQueueMetadata** metoden ber kötjänsten att returnera metadata om kön. Anropar den **getApproximateMessageCount** metod på det returnerade objektet innehåller ett antal hur många meddelanden som finns i en kö. Antalet är ungefärliga eftersom meddelanden kan läggas till eller tas bort efter kötjänsten svarar på begäran.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Ta bort en kö
Om du vill ta bort en kö och alla meddelanden i den anropar den **QueueRestProxy -> deleteQueue** metod.

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Queue storage kan du följa dessa länkar för att lära dig mer komplexa lagringsuppgifter:

* Besök den [API-referens för Azure Storage-klientbibliotek för PHP](http://azure.github.io/azure-storage-php/)
* Finns det [avancerade kön exempel](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Mer information finns också i [PHP Developer Center](/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: http://getcomposer.org/composer.phar

