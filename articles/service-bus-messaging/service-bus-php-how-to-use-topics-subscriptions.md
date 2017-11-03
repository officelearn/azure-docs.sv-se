---
title: "Hur du använder Service Bus-ämnen med PHP | Microsoft Docs"
description: "Lär dig hur du använder Service Bus-ämnen med PHP i Azure."
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: 
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: 4763b172375668213372e6f4f8fc87431c430e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Hur du använder Service Bus-ämnen och prenumerationer med PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här artikeln visar hur du använder Service Bus-ämnen och prenumerationer. Exemplen är skrivna i PHP och Använd den [Azure SDK för PHP](../php-download-sdk.md). Scenarier som tas upp inkluderar **skapa ämnen och prenumerationer**, **skapa prenumerationsfilter**, **skicka meddelanden till ett ämne**, **ta emot meddelanden från en prenumeration**, och **ta bort ämnen och prenumerationer**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Skapa en PHP-program
Det enda kravet för att skapa en PHP-program som har åtkomst till Azure Blob-tjänsten är att referera till klasserna i den [Azure SDK för PHP](../php-download-sdk.md) från inom din kod. Du kan använda alla utvecklingsverktyg för att skapa program, eller anteckningar.

> [!NOTE]
> PHP-installation måste också ha den [OpenSSL tillägget](http://php.net/openssl) installerat och aktiverat.
> 
> 

Den här artikeln beskriver hur du använder tjänstens funktioner som kan anropas i en PHP-program lokalt eller i koden körs i en Azure-webbroll, en arbetsroll eller en webbplats.

## <a name="get-the-azure-client-libraries"></a>Hämta Azure klienten bibliotek
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program att använda Service Bus
Använda Service Bus-API: er:

1. Referera till den automatiska bandladdaren filen med hjälp av den [require_once] [ require-once] instruktionen.
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du lägger till den automatiska bandladdaren fil- och referens av **ServiceBusService** klass.

> [!NOTE]
> Det här exemplet (och andra exempel i den här artikeln) förutsätter att du har installerat PHP-klientbibliotek för Azure via Composer. Om du har installerat biblioteken manuellt eller som ett PÄRONTRÄD paket måste du referera till den **WindowsAzure.php** bandladdaren filen.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I följande exempel på `require_once` instruktionen visas alltid, men endast klasserna som krävs för att köra refereras.

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus-anslutning
Om du vill skapa en instans av en Service Bus-klient måste du ha en giltig anslutningssträng i det här formatet:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Där `Endpoint` är vanligtvis i formatet `https://[yourNamespace].servicebus.windows.net`.

Så här skapar du en Azure-tjänsten måste du använda den `ServicesBuilder` klass. Du kan:

* Skicka anslutningssträngen till den.
* Använd den **CloudConfigurationManager (CCM)** till flera externa källor för anslutningssträngen:
  * Som standard levereras den med stöd för en extern källa - miljövariabler.
  * Du kan lägga till nya källor genom att utöka den `ConnectionStringSource` klass.

Exempel som beskrivs här skickas anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Skapa ett ämne
Du kan utföra hanteringsåtgärder för Service Bus-ämnen via den `ServiceBusRestProxy` klass. En `ServiceBusRestProxy` objektet har skapats den `ServicesBuilder::createServiceBusService` fabriksmetoden med en lämplig anslutningssträng som innehåller behörigheterna som är token för att hantera den.

I följande exempel visas hur du kan skapa en instans av en `ServiceBusRestProxy` och anropa `ServiceBusRestProxy->createTopic` att skapa ett ämne med namnet `mytopic` inom en `MySBNamespace` namnområde:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> Du kan använda den `listTopics` metod på `ServiceBusRestProxy` objekt för att kontrollera om det redan finns ett ämne med ett angivet namn i ett namnområde för tjänsten.
> 
> 

## <a name="create-a-subscription"></a>Skapa en prenumeration
Ämnesprenumerationer skapas också med den `ServiceBusRestProxy->createSubscription` metoden. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar den uppsättning av meddelanden som skickas till prenumerationens virtuella kö.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
**MatchAll**-filtret är det standardfilter som används om inget filter anges när en ny prenumeration skapas. När den **MatchAll** filter används, alla meddelanden som publiceras till ämnet placeras i prenumerationens virtuella kö. I följande exempel skapar en prenumeration med namnet 'mysubscription' och använder förvalet **MatchAll** filter.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan även ställa in filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas inom en viss ämnesprenumeration. Den mest flexibla typen av filter som stöds av prenumerationerna är den [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter), som implementerar en deluppsättning av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Läs mer om SqlFilters [SqlFilter.SqlExpression egenskapen][sqlfilter].

> [!NOTE]
> Varje regel för en prenumeration bearbetar inkommande meddelanden oberoende av varandra, lägga till sina resultatet meddelanden i prenumerationen. Dessutom kan varje ny prenumeration har standard **regeln** objekt med ett filter som lägger till alla meddelanden från avsnittet i prenumerationen. Du måste ta bort Standardregeln för att ta emot endast meddelanden som matchar filtret. Du kan ta bort Standardregeln med hjälp av den `ServiceBusRestProxy->deleteRule` metoden.
> 
> 

I följande exempel skapas en prenumeration med namnet `HighMessages` med en **SqlFilter** som endast väljer meddelanden som har en anpassad `MessageNumber` egenskap som är större än 3. Se [skicka meddelanden till ett ämne](#send-messages-to-a-topic) information om att lägga till anpassade egenskaper för meddelanden.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Observera att den här koden kräver användning av ett ytterligare namnområde: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

På samma sätt i följande exempel skapas en prenumeration med namnet `LowMessages` med en `SqlFilter` som endast väljer meddelanden som har en `MessageNumber` egenskap som är mindre än eller lika med 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Nu när ett meddelande skickas till den `mytopic` avsnittet levereras det alltid till mottagare som prenumererar på den `mysubscription` prenumeration, och levereras selektivt till mottagare som prenumererar på den `HighMessages` och `LowMessages` prenumerationer (beroende När innehållet i meddelandet).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Skicka ett meddelande till en Service Bus-ämne, program-anrop i `ServiceBusRestProxy->sendTopicMessage` metod. Följande kod visar hur du skickar ett meddelande till den `mytopic` avsnittet som tidigare har skapat i den `MySBNamespace` namnområde för tjänsten.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

Meddelanden som skickas till Service Bus-ämnen är instanser av den [BrokeredMessage] [ BrokeredMessage] klass. [BrokeredMessage] [ BrokeredMessage] objekt har en uppsättning standardegenskaper och metoder samt egenskaper som kan användas för att lagra anpassade programspecifika egenskaper. I följande exempel visas hur du skickar 5 testmeddelanden till den `mytopic` ämne som skapats tidigare. Den `setProperty` metoden används för att lägga till en anpassad egenskap (`MessageNumber`) till varje meddelande. Observera att den `MessageNumber` egenskapsvärde varierar för varje meddelande (du kan använda det här värdet för att avgöra vilka prenumerationer som får den, enligt den [skapa en prenumeration](#create-a-subscription) avsnitt):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här övre gräns på avsnittet storlek är 5 GB. Mer information om kvoter finns [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Det bästa sättet att ta emot meddelanden från en prenumeration är att använda en `ServiceBusRestProxy->receiveSubscriptionMessage` metod. Meddelanden kan tas emot i två olika lägen: [ *ReceiveAndDelete* och *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** är standard.

När du använder läget [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) är inleveransen en engångsåtgärd, det vill säga, när Service Bus tar emot en läsbegäran för ett meddelande i en prenumeration så markerar den meddelandet som förbrukat och skickar tillbaka det till programmet. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * läge är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, och sedan när programmet startas om och börjar förbruka meddelanden igen, att ha missat meddelandet som förbrukades innan kraschen.

I standard [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) läge, ett meddelande blir en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att skicka det mottagna meddelandet till `ServiceBusRestProxy->deleteMessage`. När Service Bus ser den `deleteMessage` den markerar meddelandet som Förbrukat och tas bort från kön.

I följande exempel visas hur du tar emot och bearbeta ett meddelande med hjälp av [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) läge (standardläget). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Så här: hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram går inte att bearbeta meddelandet av någon anledning, så kan det anropa den `unlockMessage` metod för det mottagna meddelandet (i stället för den `deleteMessage` metod). Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerad med ett meddelande som ligger låst i kön och om programmet misslyckas med att bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet automatiskt och göra det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan det `deleteMessage` begäran utfärdas och meddelandet är levereras på nytt till programmet när den startas om. Det här kallas ofta *minst när* bearbetningen, det vill säga, varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik för begäran om att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av den `getMessageId` -metoden i meddelandet som förblir konstant under alla leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Ta bort ett avsnitt eller en prenumeration genom att använda den `ServiceBusRestProxy->deleteTopic` eller `ServiceBusRestProxy->deleteSubscripton` metoder, respektive. Observera att om du tar bort ett ämne tas även bort alla prenumerationer som är registrerade med ämnet.

I följande exempel visas hur du tar bort ett ämne med namnet `mytopic` och dess registrerade prenumerationer.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

Med hjälp av den `deleteSubscription` metoden, du kan ta bort en prenumeration som är oberoende av varandra:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-köer, se [köer, ämnen och prenumerationer] [ Queues, topics, and subscriptions] för mer information.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
