---
title: Hur du använder Service Bus-köer med PHP | Microsoft Docs
description: Lär dig hur du använder Service Bus-köer i Azure. Kodexempel som skrivits i PHP.
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 3514812f7f087582035dad5d9a4d620652aa4da9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868328"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Hur du använder Service Bus-köer med PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här guiden visar hur du använder Service Bus-köer. Exemplen är skrivna i PHP och Använd den [Azure SDK för PHP](../php-download-sdk.md). Scenarier som tas upp inkluderar **skapa köer**, **skicka och ta emot meddelanden**, och **tar bort köer**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Skapa en PHP-program
Det enda kravet för att skapa en PHP-program som har åtkomst till Azure Blob-tjänsten är refererar till klasserna i den [Azure SDK för PHP](../php-download-sdk.md) från inom din kod. Du kan använda alla utvecklingsverktyg för att skapa program, eller anteckningar.

> [!NOTE]
> PHP-installation måste också ha den [OpenSSL tillägget](http://php.net/openssl) installerat och aktiverat.
> 
> 

I den här guiden använder du tjänstens funktioner som kan anropas från ett PHP-program lokalt eller i koden körs i en Azure-webbroll, en arbetsroll eller en webbplats.

## <a name="get-the-azure-client-libraries"></a>Hämta Azure klienten bibliotek
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program att använda Service Bus
Om du vill använda Service Bus-kö API: er, gör du följande:

1. Referera till den automatiska bandladdaren filen med hjälp av den [require_once] [ require_once] instruktionen.
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du lägger till den automatiska bandladdaren fil- och referens av `ServicesBuilder` klass.

> [!NOTE]
> Det här exemplet (och andra exempel i den här artikeln) förutsätter att du har installerat PHP-klientbibliotek för Azure via Composer. Om du har installerat biblioteken manuellt eller som ett PÄRONTRÄD paket måste du referera till den **WindowsAzure.php** bandladdaren filen.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I exemplen nedan, den `require_once` instruktionen visas alltid, men endast klasserna som krävs för att köra refereras.

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus-anslutning
Om du vill skapa en instans av en Service Bus-klient, måste du ha en giltig anslutningssträng i det här formatet:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Där `Endpoint` är vanligtvis i formatet `[yourNamespace].servicebus.windows.net`.

Så här skapar du en Azure-tjänsten måste du använda den `ServicesBuilder` klass. Du kan:

* Skicka anslutningssträngen till den.
* Använd den **CloudConfigurationManager (CCM)** till flera externa källor för anslutningssträngen:
  * Som standard levereras med stöd för en extern källa - miljövariabler
  * Du kan lägga till nya källor genom att utöka den `ConnectionStringSource` klass

Exempel som beskrivs här skickas anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Skapa en kö
Du kan utföra hanteringsåtgärder för Service Bus-köer via den `ServiceBusRestProxy` klass. En `ServiceBusRestProxy` objektet har skapats den `ServicesBuilder::createServiceBusService` fabriksmetoden med en lämplig anslutningssträng som innehåller behörigheterna som är token för att hantera den.

I följande exempel visas hur du kan skapa en instans av en `ServiceBusRestProxy` och anropa `ServiceBusRestProxy->createQueue` du skapar en kö med namnet `myqueue` inom en `MySBNamespace` namnområde för tjänsten:

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
> Du kan använda den `listQueues` metod på `ServiceBusRestProxy` objekt för att kontrollera om det redan finns en kö med ett angivet namn i ett namnområde.
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

Meddelanden skickas till (och mottagna från) Service Bus-köer är instanser av den [BrokeredMessage] [ BrokeredMessage] klass. [BrokeredMessage] [ BrokeredMessage] objekt har en uppsättning standard metoder och egenskaper som används för att lagra anpassade programspecifika egenskaper och en brödtext med godtyckliga programdata.

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här övre gräns för storleken är 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Det bästa sättet att ta emot meddelanden från en kö är att använda en `ServiceBusRestProxy->receiveQueueMessage` metod. Meddelanden kan tas emot i två olika lägen: [ *ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) och [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock). **PeekLock** är standard.

När du använder [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) läge får är en engångsåtgärd, det vill säga när Service Bus tar emot en läsbegäran för ett meddelande i en kö, den markerar meddelandet som Förbrukat och skickar tillbaka det till programmet. Läget [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, och sedan när programmet startas om och börjar förbruka meddelanden igen, att ha missat meddelandet som förbrukades innan kraschen.

I standard [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) läge, ett meddelande blir en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran, den söker efter nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att skicka det mottagna meddelandet till `ServiceBusRestProxy->deleteMessage`. När Service Bus ser den `deleteMessage` -anrop som den markera meddelandet som Förbrukat och tas bort från kön.

I följande exempel visas hur du tar emot och bearbeta ett meddelande med hjälp av [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) läge (standardläget).

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

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram går inte att bearbeta meddelandet av någon anledning, så kan det anropa den `unlockMessage` metod för det mottagna meddelandet (i stället för den `deleteMessage` metod). Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerad med ett meddelande som ligger låst i kön och om programmet misslyckas med att bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och gör den tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan det `deleteMessage` begäran utfärdas och sedan meddelandet att levereras till programmet när den startas om. Det här kallas ofta *minst när* bearbetningen, det vill säga, varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, bör sedan lägga till ytterligare logik i program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av den `getMessageId` -metoden i meddelandet som förblir konstant under alla leveransförsök.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-köer, se [köer, ämnen och prenumerationer] [ Queues, topics, and subscriptions] för mer information.

Mer information finns också finns den [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once


