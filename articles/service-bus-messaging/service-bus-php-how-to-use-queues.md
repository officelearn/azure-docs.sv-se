---
title: Hur du använder Service Bus-köer med PHP | Microsoft Docs
description: Lär dig hur du använder Service Bus-köer i Azure. Kodexempel som är skrivet i PHP.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 09/10/2018
ms.author: aschhab
ms.openlocfilehash: 9915392f7bb12b31dce6e141383a48b69c6f70a9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842778"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Hur du använder Service Bus-köer med PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här guiden visar hur du använder Service Bus-köer. Exemplen är skrivna i PHP och Använd den [Azure SDK för PHP](../php-download-sdk.md). Scenarier som omfattas är **skapande av köer**, **skicka och ta emot meddelanden**, och **tar bort köer**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-php-application"></a>Skapa ett PHP-program
Det enda kravet för att skapa en PHP-program som har åtkomst till Azure Blob-tjänsten är refererar till klasser i den [Azure SDK för PHP](../php-download-sdk.md) från i din kod. Du kan använda alla utvecklingsverktyg för att skapa ditt program eller anteckningar.

> [!NOTE]
> PHP-installationen måste också ha den [OpenSSL tillägget](https://php.net/openssl) installerat och aktiverat.
> 
> 

I den här guiden använder du service-funktioner som kan anropas från inom ett PHP-program lokalt eller i koden som körs i en Azure-webbroll, arbetsroll eller en webbplats.

## <a name="get-the-azure-client-libraries"></a>Hämta Azure libraries
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet att använda Service Bus
Om du vill använda Service Bus-kö API: er, gör du följande:

1. Referera till den automatiska bandladdaren fil med hjälp av den [require_once] [ require_once] instruktionen.
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du lägger till den automatiska bandladdaren filen och en referens i `ServicesBuilder` klass.

> [!NOTE]
> Det här exemplet (och andra exempel i den här artikeln) förutsätter att du har installerat PHP-Klientbiblioteken för Azure via Composer. Om du har installerat biblioteken manuellt eller som ett PÄRONTRÄD paket måste du referera den **WindowsAzure.php** automatiska bandladdaren fil.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I exemplen nedan, den `require_once` uttryck visas alltid, men endast klasserna som krävs för att köra refererar till.

## <a name="set-up-a-service-bus-connection"></a>Skapa en Service Bus-anslutning
Om du vill skapa en instans av en Service Bus-klient, måste du först ha en giltig anslutningssträng i följande format:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Där `Endpoint` har vanligtvis formatet `[yourNamespace].servicebus.windows.net`.

Att skapa alla Azure-tjänst-klienter måste du använda den `ServicesBuilder` klass. Du kan:

* Skicka anslutningssträngen till den direkt.
* Använd den **CloudConfigurationManager (CCM)** att kontrollera flera externa källor för anslutningssträngen:
  * Som standard levereras det med stöd för en extern källa - miljövariabler
  * Du kan lägga till nya källor genom att utöka den `ConnectionStringSource` klass

I exemplen som beskrivs här anges anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Skapa en kö
Du kan utföra hanteringsåtgärder för Service Bus-köer via den `ServiceBusRestProxy` klass. En `ServiceBusRestProxy` objektet skapas den `ServicesBuilder::createServiceBusService` standardmetod med en lämplig anslutningssträng som kapslar in token behörighet att hantera den.

I följande exempel visas hur du skapa en instans av en `ServiceBusRestProxy` och anropa `ServiceBusRestProxy->createQueue` att skapa en kö med namnet `myqueue` inom en `MySBNamespace` namnområde för tjänsten:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Du kan använda den `listQueues` metoden på `ServiceBusRestProxy` objekt för att kontrollera om det redan finns en kö med ett visst angivet namn i ett namnområde.
> 
> 

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Att skicka ett meddelande till en Service Bus-kö, program-anrop i `ServiceBusRestProxy->sendQueueMessage` metod. Följande kod visar hur du skickar ett meddelande till den `myqueue` kö som tidigare har skapat i den `MySBNamespace` namnområde för tjänsten.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Meddelanden skickas till (och tas emot från) Service Bus-köer är instanser av den [BrokeredMessage] [ BrokeredMessage] klass. [BrokeredMessage] [ BrokeredMessage] objekt har en uppsättning metoder som standard och egenskaper som används för att lagra anpassade programspecifika egenskaper och en brödtext med godtyckliga programdata.

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här maxgräns för köstorlek är 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Det bästa sättet att ta emot meddelanden från en kö är att använda en `ServiceBusRestProxy->receiveQueueMessage` metod. Meddelanden kan tas emot i två olika lägen: [*ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode) och [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** är standard.

När du använder [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge, ta emot är inleveransen en engångsåtgärd, det vill säga när Service Bus tar emot en läsbegäran för ett meddelande i en kö, den markerar meddelandet som Förbrukat och tillbaka till programmet. Läget [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, att sedan när programmet startas om och börjar förbruka meddelanden igen, ha missat meddelandet som förbrukades innan kraschen.

I standard [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) läge, ta emot ett meddelande blir en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att saknas. När Service Bus tar emot en begäran, det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare från att ta emot den och tillbaka det till programmet. När programmet har slutfört behandlingen av meddelandet (eller lagrar den på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att skicka det mottagna meddelandet till `ServiceBusRestProxy->deleteMessage`. När Service Bus ser den `deleteMessage` anropet, den markera meddelandet som Förbrukat och ta bort den från kön.

I följande exempel visas hur du tar emot och bearbetar ett meddelande med hjälp av [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) läget (standardläget).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram är det går inte att bearbeta meddelandet av någon anledning så kan det anropa den `unlockMessage` metod för det mottagna meddelandet (i stället för den `deleteMessage` metod). Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerade med ett meddelande som ligger låst i kön. Om programmet inte kan bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus så låser upp meddelandet automatiskt och göra det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter behandlingen av meddelandet men innan det `deleteMessage` begäran utfärdas kommer meddelandet att levereras till programmet när den startas om. Det här kallas ofta *minst när* bearbetningen, det vill säga varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande kan levereras. Om scenariot inte tolererar duplicerad bearbetning, bör sedan att lägga till ytterligare logik i program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av den `getMessageId` metoden för meddelandet som förblir konstant under alla leveransförsök.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Service Bus-köer, se [köer, ämnen och prenumerationer] [ Queues, topics, and subscriptions] för mer information.

Mer information, även på den [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


