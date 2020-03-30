---
title: Så här använder du Azure Service Bus-köer med PHP
description: I den här självstudien får du lära dig hur du skapar PHP-program för att skicka meddelanden till och ta emot meddelanden från en servicebusskö.
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
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: fcb735d81cac587c75a133ad582f2a839551dcfa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760699"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Snabbstart: Så här använder du Service Bus-köer med PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

I den här självstudien får du lära dig hur du skapar PHP-program för att skicka meddelanden till och ta emot meddelanden från en servicebusskö. 

## <a name="prerequisites"></a>Krav
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [msdn-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Om du inte har en kö att arbeta med följer du stegen i [Azure-portalen för att skapa en](service-bus-quickstart-portal.md) köartikel för Service Bus för att skapa en kö.
    1. Läs snabb **översikten över** servicebussköer . **queues** 
    2. Skapa ett **servicebussnamnområde**. 
    3. Hämta **anslutningssträngen**. 

        > [!NOTE]
        > Du skapar en **kö** i servicebussens namnområde med hjälp av PHP i den här självstudien. 
3. [Azure SDK för PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Skapa ett PHP-program
Det enda kravet för att skapa ett PHP-program som får åtkomst till Azure Blob-tjänsten är hänvisningen till klasser i [Azure SDK för PHP](https://github.com/Azure/azure-sdk-for-php) inifrån koden. Du kan använda alla utvecklingsverktyg för att skapa ditt program eller Anteckningar.

> [!NOTE]
> Din PHP-installation måste också ha [OpenSSL-tillägget](https://php.net/openssl) installerat och aktiverat.

I den här guiden använder du tjänstfunktioner som kan anropas inifrån ett PHP-program lokalt eller i kod som körs inom en Azure-webbroll, arbetsroll eller webbplats.

## <a name="get-the-azure-client-libraries"></a>Skaffa Azure-klientbibliotek
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program så att det använder Service Bus
Så här använder du API:erna för servicebusskön:

1. Referera till autoloader-filen med [hjälp][require_once] av require_once-satsen.
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du tar med `ServicesBuilder` filen för automatisk inläsning och refererar till klassen.

> [!NOTE]
> Det här exemplet (och andra exempel i den här artikeln) förutsätter att du har installerat PHP-klientbiblioteken för Azure via Composer. Om du har installerat biblioteken manuellt eller som ett PEAR-paket måste du referera till filen **WindowsAzure.php** autoloader.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I exemplen nedan `require_once` visas alltid satsen, men endast de klasser som krävs för att köra exempel refereras.

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en servicebussanslutning
Om du vill instansiera en Service Bus-klient måste du först ha en giltig anslutningssträng i det här formatet:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Var `Endpoint` är vanligtvis `[yourNamespace].servicebus.windows.net`av formatet .

Om du vill skapa en Azure-tjänstklient måste du använda `ServicesBuilder` klassen. Du kan:

* Skicka anslutningssträngen direkt till den.
* Använd **CloudConfigurationManager (CCM)** för att kontrollera flera externa källor för anslutningssträngen:
  * Som standard levereras med stöd för en extern källa - miljövariabler
  * Du kan lägga till `ConnectionStringSource` nya källor genom att utöka

I exemplen som beskrivs här anges anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Skapa en kö
Du kan utföra hanteringsåtgärder för Service `ServiceBusRestProxy` Bus-köer via klassen. Ett `ServiceBusRestProxy` objekt konstrueras `ServicesBuilder::createServiceBusService` via fabriksmetoden med en lämplig anslutningssträng som kapslar in tokenbehörigheterna för att hantera det.

I följande exempel visas hur `ServiceBusRestProxy` du `ServiceBusRestProxy->createQueue` instansierar en `myqueue` och `MySBNamespace` anropar för att skapa en kö som heter inom ett tjänstnamnområde:

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
> Du kan `listQueues` använda `ServiceBusRestProxy` metoden på objekt för att kontrollera om det redan finns en kö med ett angivet namn inom ett namnområde.
> 
> 

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Om du vill skicka ett meddelande till `ServiceBusRestProxy->sendQueueMessage` en Service Bus-kö anropar programmet metoden. Följande kod visar hur du skickar `myqueue` ett meddelande `MySBNamespace` till kön som tidigare skapats inom tjänstens namnområde.

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

Meddelanden som skickas till (och tas emot från) Service Bus-köer är instanser av klassen [BrokeredMessage.][BrokeredMessage] [BrokeredMessage-objekt][BrokeredMessage] har en uppsättning standardmetoder och egenskaper som används för att lagra anpassade programspecifika egenskaper och en samling godtyckliga programdata.

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här övre gränsen för köstorlek är 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Det bästa sättet att ta emot meddelanden `ServiceBusRestProxy->receiveQueueMessage` från en kö är att använda en metod. Meddelanden kan tas emot i två olika lägen: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) och [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** är standard.

När du använder [ReceiveAndDelete-läge](/dotnet/api/microsoft.servicebus.messaging.receivemode) är mottagning en enda bildåtgärd. det vill exempel på när Service Bus tar emot en läsbegäran för ett meddelande i en kö markeras meddelandet som förbrukat och returnerar det till programmet. Läget [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat, då när programmet startas om och börjar konsumera meddelanden igen, kommer det att ha missat meddelandet som förbrukades före kraschen.

I [standardläget PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) blir mottagning av ett meddelande en tvåstegsåtgärd, vilket gör det möjligt att stödja program som inte tolererar meddelanden som saknas. När Service Bus tar emot en begäran hittar den nästa meddelande som ska förbrukas, låser det för att förhindra att andra konsumenter tar emot den och returnerar det sedan till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida `ServiceBusRestProxy->deleteMessage`bearbetning) slutförs det andra steget i mottagningsprocessen genom att skicka det mottagna meddelandet till . När Service Bus `deleteMessage` ser samtalet markeras meddelandet som förbrukat och tar bort det från kön.

I följande exempel visas hur du får och bearbetar ett meddelande med [PeekLock-läge](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (standardläget).

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

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram inte kan bearbeta meddelandet av någon `unlockMessage` anledning kan det anropa `deleteMessage` metoden på det mottagna meddelandet (i stället för metoden). Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt för att tas emot igen, antingen av samma tidskrävande program eller av ett annat tidskrävande program.

Det finns också en timeout som är associerad med ett meddelande låst i kön, och om programmet inte kan bearbeta meddelandet innan låstidsgränsen går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och göra det tillgängliga för att tas emot igen.

I händelse av att programmet kraschar efter bearbetning `deleteMessage` av meddelandet men innan begäran utfärdas, kommer meddelandet att levereras till programmet när det startas om. Detta kallas ofta *minst en gång* bearbetning; det vill säga varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras på nytt. Om scenariot inte kan tolerera dubblettbearbetning rekommenderas du att lägga till ytterligare logik i program för att hantera leverans av dubblettmeddelanden. Detta uppnås ofta `getMessageId` med hjälp av metoden för meddelandet, som förblir konstant över leveransförsök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i servicebussköer läser du [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions] för mer information.

För mer information, besök också [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


