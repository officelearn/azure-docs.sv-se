---
title: Hur du använder Azure Service Bus-ämnen med Java | Microsoft Docs
description: Använd Service Bus-ämnen och prenumerationer i Azure.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/17/2018
ms.author: spelluru
ms.openlocfilehash: 0be5f9842cd3aa90d82f3efe44451e624ed5d371
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395686"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Hur du använder Service Bus-ämnen och prenumerationer med Java

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här guiden beskriver hur du använder Service Bus-ämnen och prenumerationer. Exemplen är skrivna i Java och Använd den [Azure SDK för Java][Azure SDK for Java]. Scenarier som omfattas är **skapa ämnen och prenumerationer**, **skapa prenumerationsfilter**, **skicka meddelanden till ett ämne**, **tar emot meddelanden från en prenumeration**, och **ta bort ämnen och prenumerationer**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Vad är Service Bus-ämnen och -prenumerationer?
Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Till skillnad från Service Bus-köer, där varje meddelande bearbetas av en enskild konsument så ger ämnen och prenumerationer en en-till-många-form av kommunikation, med hjälp av ett Publicera/prenumerera-mönster. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning.

En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Du kan välja att registrera filterregler för ett ämne per prenumeration, där du kan filtrera eller begränsa vilka meddelanden i ett ämne tas emot av olika ämnesprenumerationer.

Service Bus-ämnen och prenumerationer kan du skala för att bearbeta ett stort antal meddelanden över ett stort antal användare och program.

## <a name="create-a-service-namespace"></a>Skapa ett namnområde för tjänsten
Om du vill komma igång med Service Bus-ämnen och prenumerationer i Azure måste du först skapa en *namnområde*, som tillhandahåller en omfångsbehållare för adressering av Service Bus-resurser i ditt program.

Så här skapar du ett namnområde:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet att använda Service Bus
Kontrollera att du har installerat den [Azure SDK för Java] [ Azure SDK for Java] innan du skapar det här exemplet. Om du använder Eclipse kan du installera den [Azure Toolkit för Eclipse] [ Azure Toolkit for Eclipse] som innehåller Azure SDK för Java. Du kan sedan lägga till den **Microsoft Azure-biblioteken för Java** i projektet:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Lägg till följande `import` uttryck längst upp i filen Java:

```java
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Lägg till Azure-biblioteken för Java till build-sökväg och inkludera den i projektet distribution sammansättningen.

## <a name="create-a-topic"></a>Skapa ett ämne
Hanteringsåtgärder för Service Bus-ämnen kan utföras via den **ServiceBusContract** klass. En **ServiceBusContract** objekt har konstruerats med en lämplig konfiguration som innehåller SAS-token med behörigheter för att hantera det, och **ServiceBusContract** klass är den enda kommunikation med Azure.

Den **ServiceBusService** klassen innehåller metoder för att skapa, räkna upp och ta bort ämnen. I följande exempel visas hur en **ServiceBusService** objekt som kan användas för att skapa ett ämne med namnet `TestTopic`, med ett namnområde som kallas `HowToSample`:

```java
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
      "HowToSample",
      "RootManageSharedAccessKey",
      "SAS_key_value",
      ".servicebus.windows.net"
      );

ServiceBusContract service = ServiceBusService.create(config);
TopicInfo topicInfo = new TopicInfo("TestTopic");
try  
{
    CreateTopicResult result = service.createTopic(topicInfo);
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Det finns metoder på **TopicInfo** som gör att egenskaper anges (till exempel: Ange time to live (TTL) standardvärdet ska tillämpas på meddelanden som skickas till ämnet). I följande exempel visas hur du skapar ett ämne med namnet `TestTopic` med en maximal storlek på 5 GB:

```java
long maxSizeInMegabytes = 5120;  
TopicInfo topicInfo = new TopicInfo("TestTopic");  
topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateTopicResult result = service.createTopic(topicInfo);
```

Du kan använda den **listTopics** metoden på **ServiceBusContract** objekt för att kontrollera om det redan finns ett ämne med ett visst angivet namn i ett namnområde för tjänsten.

## <a name="create-subscriptions"></a>Skapa prenumerationer
Prenumerationer på ämnen skapas också med den **ServiceBusService** klass. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar den uppsättning av meddelanden som skickas till prenumerationens virtuella kö.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
**MatchAll**-filtret är det standardfilter som används om inget filter anges när en ny prenumeration skapas. När den **MatchAll** filter används, alla meddelanden som publiceras till ämnet placeras i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet `AllMessages` och vi använder `MatchAll` filter.

```java
SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
CreateSubscriptionResult result =
    service.createSubscription("TestTopic", subInfo);
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan också skapa filter som gör det möjligt att omfattning som meddelanden som skickas till ett ämne visas inom en viss ämnesprenumeration.

Den mest flexibla typen av filter som stöds av prenumerationerna är den [SqlFilter][SqlFilter], som implementerar en deluppsättning av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om uttryck som kan användas med ett SQL-filter granskar den [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntax.

I följande exempel skapas en prenumeration med namnet `HighMessages` med en [SqlFilter] [ SqlFilter] objekt som endast väljer meddelanden som har en anpassad **MessageNumber** egenskap som är större än 3:

```java
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

På samma sätt i följande exempel skapas en prenumeration med namnet `LowMessages` med en [SqlFilter] [ SqlFilter] objekt som endast väljer meddelanden som har en **MessageNumber** egenskap som är mindre än eller lika med 3:

```java
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

När ett meddelande skickas nu till `TestTopic`, skickas alltid till mottagare som prenumererar på den `AllMessages` prenumeration, och det levereras selektivt till mottagare som prenumererar på den `HighMessages` och `LowMessages` prenumerationer (beroende på den meddelandeinnehåll).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Om du vill skicka ett meddelande till en Service Bus-ämne, ditt program hämtar en **ServiceBusContract** objekt. Följande kod visar hur du skickar ett meddelande den `TestTopic` ämne som skapades tidigare i den `HowToSample` namnområde:

```java
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Meddelanden som skickas till Service Bus-ämnen är instanser av den [BrokeredMessage] [ BrokeredMessage] klass. [BrokeredMessage][BrokeredMessage]* objekt har en uppsättning metoder som standard (till exempel **setLabel** och **TimeToLive**), en ordlista som används för att lagra anpassade programspecifika egenskaper och en brödtext med godtyckliga programdata. Ett program kan konfigurera meddelandets brödtext genom att skicka någon typ av serialiserbara objekt till konstruktören av den [BrokeredMessage][BrokeredMessage], önskad **DataContractSerializer** används sedan för att serialisera objektet. Du kan också en **java.io.InputStream** kan anges.

I följande exempel visar hur du skickar fem testmeddelanden till den `TestTopic` **MessageSender** vi hämtades i föregående kodfragment.
Observera hur **MessageNumber** egenskapsvärdet för varje meddelande varierar på upprepning av loopen (det här värdet avgör vilka prenumerationer som får det):

```java
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för hur många meddelanden som ligger i ett ämne men det finns en gräns för den totala storleken på de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Om du vill ta emot meddelanden från en prenumeration, använder en **ServiceBusContract** objekt. Mottagna meddelanden kan arbeta i två olika lägen: **ReceiveAndDelete** och **PeekLock** (standard).

När du använder den **ReceiveAndDelete** läge, ta emot är inleveransen en engångsåtgärd – det vill säga när Service Bus tar emot en läsbegäran för ett meddelande, den markerar meddelandet som Förbrukat och tillbaka till programmet. **ReceiveAndDelete** läge är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. Tänk dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan bearbetningen. Eftersom Service Bus har markerat meddelandet som Förbrukat, har sedan när programmet startas om och börjar förbruka meddelanden igen, det missat meddelandet som förbrukades innan kraschen.

I **PeekLock** läge, ta emot en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört behandlingen av meddelandet (eller lagrar den på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att anropa **ta bort** för det mottagna meddelandet. När Service Bus ser den **ta bort** anrop, den markerar meddelandet som Förbrukat och tar bort den från ämnet.

Följande exempel visar hur meddelanden kan tas emot och bearbetat använder **PeekLock** (standardläget). I exemplet utför en slinga och bearbetar meddelanden i den `HighMessages` prenumeration och sedan avslutas när det finns inga fler meddelanden (du kan också den kan ställas in att vänta tills nya meddelanden).

```java
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram är det går inte att bearbeta meddelandet av någon anledning så kan det anropa den **unlockMessage** metod för det mottagna meddelandet (i stället för den **deleteMessage** metod). Det här metodanropet gör att Service Bus att låsa upp meddelandet i avsnittet om och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerade med ett meddelande som ligger låst i ämnet om programmet inte kan bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter behandlingen av meddelandet men innan de **deleteMessage** begäran utfärdas sedan meddelandet once till programmet när den startas om. Den här processen kallas ofta **bearbetning minst en gång**, det vill säga varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande kan levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Du kan göra det med hjälp av den **getMessageId** -metoden för meddelandet som förblir konstant under alla leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Det vanligaste sättet att ta bort ämnen och prenumerationer är att använda en **ServiceBusContract** objekt. Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat.

```java
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Service Bus-köer, ämnen och prenumerationer] [ Service Bus queues, topics, and subscriptions] för mer information.

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
