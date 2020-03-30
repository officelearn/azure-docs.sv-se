---
title: Så här använder du Azure Service Bus-ämnen med PHP
description: I den här självstudien får du lära dig hur du använder Azure Service Bus ämnen och prenumerationer från ett PHP-program.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 92f25f4bdac4942478c93f717c81eadd2c2f5b4a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760682"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Snabbstart: Så här använder du Service Bus-ämnen och prenumerationer med PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I den här artikeln visas hur du använder servicebussavsnitt och prenumerationer. Exemplen är skrivna i PHP och använder [Azure SDK för PHP](https://github.com/Azure/azure-sdk-for-php). De scenarier som behandlas omfattar:

- Skapa ämnen och prenumerationer 
- Skapa prenumerationsfilter 
- Skicka meddelanden till ett ämne 
- Ta emot meddelanden från en prenumeration
- Ta bort ämnen och prenumerationer

## <a name="prerequisites"></a>Krav
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina fördelar för [Visual Studio- eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i [snabbstart: Använd Azure-portalen för att skapa ett Service Bus-ämne och prenumerationer på ämnet](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa ett **servicebussnamnområde** och hämta **anslutningssträngen**.

    > [!NOTE]
    > Du kommer att skapa ett **ämne** och en **prenumeration** på ämnet med hjälp av **PHP** i denna snabbstart. 

## <a name="create-a-php-application"></a>Skapa ett PHP-program
Det enda kravet för att skapa ett PHP-program som får åtkomst till Azure Blob-tjänsten är att referera klasser i [Azure SDK för PHP](https://github.com/Azure/azure-sdk-for-php) inifrån din kod. Du kan använda alla utvecklingsverktyg för att skapa ditt program eller Anteckningar.

> [!NOTE]
> Din PHP-installation måste också ha [OpenSSL-tillägget](https://php.net/openssl) installerat och aktiverat.
> 
> 

I den här artikeln beskrivs hur du använder tjänstfunktioner som kan anropas i ett PHP-program lokalt eller i kod som körs i en Azure-webbroll, arbetsroll eller webbplats.

## <a name="get-the-azure-client-libraries"></a>Skaffa Azure-klientbibliotek

### <a name="install-via-composer"></a>Installera via Composer
1. Skapa en fil med namnet **composer.json** i projektets rot och lägg till följande kod i den:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Ladda ner **[composer.phar][composer-phar]** i projektroten.
3. Öppna en kommandotolk och kör följande kommando i projektroten
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program så att det använder Service Bus
Så här använder du Service Bus-API:erna:

1. Referera till autoloader-filen med [hjälp][require-once] av require_once-satsen.
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du tar med autoloaderfilen och refererar till klassen **ServiceBusService.**

> [!NOTE]
> Det här exemplet (och andra exempel i den här artikeln) förutsätter att du har installerat PHP-klientbiblioteken för Azure via Composer. Om du har installerat biblioteken manuellt eller som ett PEAR-paket måste du referera till filen **WindowsAzure.php** autoloader.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I följande exempel `require_once` visas alltid satsen, men endast de klasser som krävs för att köra exempel refereras.

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en servicebussanslutning
Om du vill instansiera en Service Bus-klient måste du först ha en giltig anslutningssträng i det här formatet:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Var `Endpoint` är vanligtvis `https://[yourNamespace].servicebus.windows.net`av formatet .

Om du vill skapa en Azure-tjänstklient måste du använda `ServicesBuilder` klassen. Du kan:

* Skicka anslutningssträngen direkt till den.
* Använd **CloudConfigurationManager (CCM)** för att kontrollera flera externa källor för anslutningssträngen:
  * Som standard levereras det med stöd för en extern källa - miljövariabler.
  * Du kan lägga till nya källor genom att utöka `ConnectionStringSource`-klassen.

I exemplen som beskrivs här anges anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Skapa ett ämne
Du kan utföra hanteringsåtgärder för `ServiceBusRestProxy` Service Bus-ämnen via klassen. Ett `ServiceBusRestProxy` objekt konstrueras `ServicesBuilder::createServiceBusService` via fabriksmetoden med en lämplig anslutningssträng som kapslar in tokenbehörigheterna för att hantera det.

I följande exempel visas hur `ServiceBusRestProxy` du `ServiceBusRestProxy->createTopic` instansierar ett `mytopic` och `MySBNamespace` anropar för att skapa ett ämne som heter inom ett namnområde:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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
> Du kan `listTopics` använda `ServiceBusRestProxy` metoden för objekt för att kontrollera om det redan finns ett ämne med ett angivet namn inom ett tjänstnamnområde.
> 
> 

## <a name="create-a-subscription"></a>Skapa en prenumeration
Ämnesprenumerationer `ServiceBusRestProxy->createSubscription` skapas också med metoden. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar den uppsättning av meddelanden som skickas till prenumerationens virtuella kö.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
Om inget filter anges när en ny prenumeration skapas används **Filtret MatchAll** (standard). När **Filtret MatchAll** används placeras alla meddelanden som publiceras i ämnet i prenumerationens virtuella kö. I följande exempel skapas `mysubscription` en prenumeration med namnet och standardfiltret **MatchAll** används.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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
Du kan även ställa in filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas inom en viss ämnesprenumeration. Den mest flexibla typen av filter som stöds av prenumerationer är [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), som implementerar en delmängd av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om SqlFilters finns i [sqlfilter.sqlexpression- egenskap][sqlfilter].

> [!NOTE]
> Varje regel för en prenumeration bearbetar inkommande meddelanden oberoende av varandra och lägger till sina resultatmeddelanden i prenumerationen. Dessutom har varje ny prenumeration ett **standardregelobjekt** med ett filter som lägger till alla meddelanden från ämnet i prenumerationen. Om du bara vill ta emot meddelanden som matchar filtret måste du ta bort standardregeln. Du kan ta bort standardregeln med hjälp av `ServiceBusRestProxy->deleteRule` metoden.
> 
> 

I följande exempel skapas `HighMessages` en prenumeration med namnet **SqlFilter** som `MessageNumber` bara väljer meddelanden som har en anpassad egenskap som är större än 3. Se [Skicka meddelanden till ett ämne](#send-messages-to-a-topic) för information om hur du lägger till anpassade egenskaper i meddelanden.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Den här koden kräver användning av `WindowsAzure\ServiceBus\Models\SubscriptionInfo`ytterligare ett namnområde: .

På samma sätt skapar följande exempel `LowMessages` en `SqlFilter` prenumeration som namnges med `MessageNumber` en som bara väljer meddelanden som har en egenskap som är mindre än eller lika med 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Nu, när ett meddelande `mytopic` skickas till ämnet, det alltid levereras `mysubscription` till mottagare prenumererar på prenumerationen, `HighMessages` `LowMessages` och selektivt levereras till mottagare prenumererar på och prenumerationer (beroende på meddelandet innehåll).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Om du vill skicka ett meddelande till `ServiceBusRestProxy->sendTopicMessage` ett Service Bus-ämne anropar programmet metoden. Följande kod visar hur du skickar `mytopic` ett meddelande `MySBNamespace` till det ämne som tidigare skapats inom tjänstens namnområde.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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

Meddelanden som skickas till Service Bus-ämnen är instanser av klassen [BrokeredMessage][BrokeredMessage]. [BrokeredMessage-objekt][BrokeredMessage] har en uppsättning standardegenskaper och metoder, samt egenskaper som kan användas för att lagra anpassade programspecifika egenskaper. I följande exempel visas hur du skickar `mytopic` fem testmeddelanden till det ämne som tidigare skapats. Metoden `setProperty` används för att lägga`MessageNumber`till en anpassad egenskap ( ) i varje meddelande. Egenskapsvärdet `MessageNumber` varierar beroende på varje meddelande (du kan använda det här värdet för att avgöra vilka prenumerationer som får det, som visas i avsnittet [Skapa en prenumeration):](#create-a-subscription)

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

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här övre gränsen för ämnesstorlek är 5 GB. Mer information om kvoter finns i [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Det bästa sättet att ta emot meddelanden `ServiceBusRestProxy->receiveSubscriptionMessage` från en prenumeration är att använda en metod. Meddelanden kan tas emot i två olika lägen: [ *ReceiveAndDelete* och *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** är standard.

När du använder läget [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) är inleveransen en engångsåtgärd, det vill säga, när Service Bus tar emot en läsbegäran för ett meddelande i en prenumeration så markerar den meddelandet som förbrukat och skickar tillbaka det till programmet. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * läge är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande när ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat, då när programmet startas om och börjar konsumera meddelanden igen, har det missat meddelandet som förbrukades före kraschen.

I [standardläget PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) blir mottagning av ett meddelande en tvåstegsåtgärd, vilket gör det möjligt att stödja program som inte tolererar meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida `ServiceBusRestProxy->deleteMessage`bearbetning) slutförs det andra steget i mottagningsprocessen genom att skicka det mottagna meddelandet till . När Service Bus `deleteMessage` ser samtalet markeras meddelandet som förbrukat och tar bort det från kön.

I följande exempel visas hur du får och bearbetar ett meddelande med [PeekLock-läge](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) (standardläget). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Så här hanterar du programkrascher och oläsliga meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram inte kan bearbeta meddelandet av någon `unlockMessage` anledning kan det anropa `deleteMessage` metoden på det mottagna meddelandet (i stället för metoden). Det gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt för att tas emot igen, antingen av samma tidskrävande program eller av ett annat tidskrävande program.

Det finns också en timeout som är associerad med ett meddelande låst i kön, och om programmet inte kan bearbeta meddelandet innan låstidsgränsen upphör att gälla (till exempel om programmet kraschar), låses Service Bus upp meddelandet automatiskt och gör det tillgängliga för att tas emot igen.

I händelse av att programmet kraschar efter bearbetning `deleteMessage` av meddelandet men innan begäran utfärdas, levereras meddelandet till programmet när det startas om. Denna typ av bearbetning kallas ofta *minst en gång* bearbetning; det vill säga varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras på nytt. Om scenariot inte kan tolerera dubblettbearbetning bör programutvecklare lägga till ytterligare logik i program för att hantera leverans av dubblettmeddelanden. Det uppnås ofta `getMessageId` med hjälp av metoden för meddelandet, som förblir konstant över leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Om du vill ta bort `ServiceBusRestProxy->deleteTopic` ett `ServiceBusRestProxy->deleteSubscripton` ämne eller en prenumeration använder du metoderna eller metoderna. Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet.

I följande exempel visas hur `mytopic` du tar bort ett med namn ett ämne och dess registrerade prenumerationer.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
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

Med hjälp `deleteSubscription` av metoden kan du ta bort en prenumeration oberoende av följande:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
