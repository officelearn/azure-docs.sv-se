---
title: "Använda Azure Service Bus-ämnen med .NET | Microsoft Docs"
description: "Lär dig hur du använder Service Bus-ämnen och -prenumerationer med .NET i Azure. Kodexemplen är skrivna för .NET-program."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: bec18e91ef8798a791d4b1fe93bd529593197e01
ms.lasthandoff: 03/24/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Använda Service Bus-ämnen och -prenumerationer
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I den här artikeln beskrivs hur du använder Service Bus-ämnen och -prenumerationer. Exemplen är skrivna i C# och använder .NET-API:erna. Scenarier som tas upp är bland annat: skapa ämnen och prenumerationer, skapa prenumerationsfilter, skicka meddelanden till ett ämne, ta emot meddelanden från en prenumeration och ta bort ämnen och prenumerationer. Mer information om ämnen och prenumerationer, finns i avsnittet [Nästa steg](#next-steps).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Konfigurera programmet för att använda Service Bus
När du skapar ett program som använder Service Bus måste du lägga till en referens till Service Bus-sammansättningen och inkludera motsvarande namnområden. Det enklaste sättet att göra det på är att hämta ett lämpligt [NuGet](https://www.nuget.org)-paket.

## <a name="get-the-service-bus-nuget-package"></a>Hämta Service Bus-NuGet-paketet
[Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) är det enklaste sättet att konfigurera ditt program med alla nödvändiga Service Bus-beroenden. Om du vill installera NuGet-paketet för Service Bus i din app gör du följande:

1. Högerklicka på **Referenser** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
2. Klicka på **Bläddra**, sök efter ”Azure Service Bus” och välj sedan objektet **Microsoft Azure Service Bus**. Klicka på **Installera** för att slutföra installationen och stäng sedan dialogrutan:
   
   ![][7]

Du är nu redo att skriva kod för Service Bus.

## <a name="create-a-service-bus-connection-string"></a>Skapa en anslutningssträng för Service Bus
Service Bus använder en anslutningssträng för att lagra slutpunkter och autentiseringsuppgifter. Du kan lägga anslutningssträngen i en konfigurationsfil istället för att hårdkoda den:

* När du använder Azure Services rekommenderar vi att du lagrar anslutningssträngen med hjälp av konfigurationssystemet för Azure-tjänster (.csdef- och .cscfg-filer).
* När du använder Azure Websites eller Azure Virtual Machines rekommenderar vi att du lagrar anslutningssträngen med konfigurationssystemet för .NET (till exempel Web.config-filen).

I båda fallen kan du hämta din anslutningssträng genom att använda metoden `CloudConfigurationManager.GetSetting`, som visas lite längre fram i denna artikel.

### <a name="configure-your-connection-string"></a>Konfigurera anslutningssträngen
Tjänstkonfigurationens mekanism låter dig dynamiskt ändra konfigurationsinställningarna från [Azure-portalen][Azure portal] utan att behöva distribuera ditt program en gång till. Du kan till exempel lägga till en `Setting`-etikett till tjänstdefinitionsfilen (***.csdef**), enligt nästa exempel.

```xml
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Du anger sedan värden i tjänstekonfigurationsfilen (.cscfg).

```xml
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Använd signaturen för delad åtkomst (SAS) och de nyckelvärden som hämtades från portalen, enligt den tidigare beskrivningen.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Konfigurera anslutningssträngen när du använder Azure Websites eller Azure Virtual Machines
När du använder webbplatser eller Virtual Machines rekommenderar vi att du använder konfigurationssystemet för .NET (till exempel Web.config). Du sparar anslutningssträngen med hjälp av `<appSettings>`-elementet.

```xml
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Använd det SAS-namn och -nyckelvärden som du hämtade från [Azure-portalen][Azure portal], enligt den tidigare beskrivningen.

## <a name="create-a-topic"></a>Skapa ett ämne
Du kan utföra hanteringsåtgärder för Service Bus-ämnen och -prenumerationer genom att använda klassen [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). Den här klassen innehåller metoder för att skapa, räkna upp och ta bort ämnen.

I följande exempel skapas ett `NamespaceManager`-objekt med klassen Azure`CloudConfigurationManager` som innehåller en anslutningssträng bestående av basadressen för namnområdet för Service Bus och lämpliga autentiseringsuppgifter för SAS med hanteringsbehörigheter. Den här anslutningssträngen har följande format:

```xml
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Använd följande exempel, med konfigurationsinställningarna från föregående avsnitt som utgångspunkt.

```csharp
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Det finns överlagringar av [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager)-metoden som gör att du kan justera ämnets egenskaper (till exempel för att ställa in det förvalda TTL-värde (Time-To-Live) som ska tillämpas på meddelanden som skickas till ämnet. De här inställningarna tillämpas genom att använda [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)-klassen. I följande exempel visas hur du skapar ett ämne med namnet **TestTopic**, med en maximal storlek på 5 GB och förvalt TTL-värde för meddelanden på 1 minut.

```csharp
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> Du kan använda metoden [TopicExists](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_TopicExists_System_String_) på [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)-objekt för att kontrollera om det redan finns ett ämne med ett visst angivet namn i ett namnområde.
> 
> 

## <a name="create-a-subscription"></a>Skapa en prenumeration
Du kan även skapa ämnesprenumerationer med hjälp av klassen [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar den uppsättning av meddelanden som skickas till prenumerationens virtuella kö.

> [!IMPORTANT]
> Du måste skapa den prenumerationen innan du skickar meddelanden till avsnittet för meddelanden ska kunna tas emot av en prenumeration. Om det inte finns någon prenumeration för ett avsnitt ignorerar avsnittet dessa meddelanden.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
**MatchAll**-filtret är det standardfilter som används om inget filter anges när en ny prenumeration skapas. När du använder **MatchAll**-filtret kommer alla meddelanden som publiceras till ämnet att placeras i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet "AllMessages" och vi använder standardfiltret **MatchAll**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan även ställa in filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas inom en viss ämnesprenumeration.

Den mest flexibla typen av filter som stöds av prenumerationerna är klassen [SqlFilter][SqlFilter] som implementerar en deluppsättning av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om uttryck som kan användas med ett SQL-filter finns i syntaxen [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Följande exempel skapar en prenumeration med namnet **HighMessages** med ett [SqlFilter][SqlFilter]-objekt som endast sorterar ut meddelanden som har en anpassad **MessageNumber**-egenskap som är större än 3.

```csharp
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

På ett liknande sätt skapar följande exempel en prenumeration med namnet **LowMessages** med ett [SqlFilter][SqlFilter] som endast väljer meddelanden som har en **MessageNumber**-egenskap som är mindre än eller lika med 3.

```csharp
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

När ett meddelande skickas till `TestTopic` levereras det alltid till mottagare som prenumererar på ämnesprenumerationen **AllMessages**. Det levereras selektivt till mottagare som prenumererar på ämnesprenumerationerna **HighMessages** och **LowMessages** (beroende på innehållet i meddelandena).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Om du vill skicka ett meddelande till ett Service Bus-ämne skapar ditt program ett [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient)-objekt med hjälp av anslutningssträngen.

Följande kod visar hur du skapar ett [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient)-objekt för det **TestTopic**-ämne som skapades tidigare med hjälp av [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.topicclient#Microsoft_ServiceBus_Messaging_TopicClient_CreateFromConnectionString_System_String_System_String_)-API:n.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Meddelanden som skickas till Service Bus-ämnen är instanser av klassen [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). **BrokeredMessage**-objekt har en uppsättning standardegenskaper (t.ex. [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) och [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), en ordlista som används för att lagra specifika egenskaper för anpassade program och en brödtext med godtyckliga programdata. Ett program kan konfigurera meddelandets brödtext genom att skicka någon typ av serialiserbara objekt till konstruktören av **BrokeredMessage**-objektet. Sedan används önskad **DataContractSerializer** för att serialisera objektet. Annars kan ett **System.IO.Stream**-objekt tillhandahållas.

Följande exempel visar hur du skickar fem testmeddelanden till objektet **TestTopic** [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) som du fick i det förra kodexemplet. Observera att värdet för [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId)-egenskapen för varje meddelande varierar beroende på upprepningen av loopen (detta avgör vilka prenumerationer som tar emot det).

```csharp
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageId"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB. Den övre gränsen är högre om partitionering är aktiverad. Mer information finns i [Partitionerade meddelandeentiteter](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Det rekommenderade sättet att ta emot meddelanden från en prenumeration är att använda objektet [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient). **SubscriptionClient**-objekt kan arbeta i två olika lägen: [*ReceiveAndDelete* och *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** är standard.

När du använder läget **ReceiveAndDelete** är inleveransen en engångsåtgärd, det vill säga, när Service Bus tar emot en läsbegäran för ett meddelande i en prenumeration så markerar den meddelandet som förbrukat och skickar tillbaka det till programmet. Läget **ReceiveAndDelete** är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat så kommer programmet – när det startas om och börjar förbruka meddelanden igen – att ha missat meddelandet som förbrukades innan kraschen.

I **PeekLock**-läget (standardläget) blir inleveransen en åtgärd i två steg, vilket ger stöd för program som inte tolererar att meddelanden saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), slutför programmet det andra steget i processen genom att anropa [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) för det mottagna meddelandet. När Service Bus ser anropet **Slutför**, kommer det att markera meddelandet som förbrukat och det tas därefter bort från prenumerationen.

Följande exempel visar hur meddelanden kan tas emot och bearbetas med det förvalda **PeekLock**-läget. Om du vill ange ett annat värde för [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode), kan du använda en annan överlagring för [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_CreateFromConnectionString_System_String_System_String_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_). I det här exemplet används motringningen [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) för att bearbeta meddelanden när de tas emot i prenumerationen **Highmessages**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

I det här exemplet konfigureras motringningen [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) med hjälp av objektet [OnMessageOptions](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions). [AutoComplete](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoComplete) är inställt på **false** för att du ska kunna ha manuell kontroll över när anropet [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) ska göras för det mottagna meddelandet. [AutoRenewTimeout](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout) har ställts in på en minut, vilket leder till att klienten kommer att vänta i upp till en minut innan den avslutar funktionen för automatisk förnyelse och klienten gör ett nytt anrop för att söka efter meddelanden. Det här egenskapsvärdet minskar antalet gånger som klienten skickar avgiftsbelagda anrop som inte hämtar meddelanden.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagande program av något skäl inte kan bearbeta meddelandet kan det anropa metoden [Avbryt](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon_System_Collections_Generic_IDictionary_System_String_System_Object__) för det mottagna meddelandet (i stället för metoden [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Detta gör att Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt att tas emot igen, antingen genom att samma användningsprogram eller ett annat användningsprogram.

Det finns även en tidsgräns som är associerad med ett meddelande som ligger låst i prenumerationen. Om programmet misslyckas med att bearbeta meddelandet innan tidsgränsen för låsning går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och sedan göra det tillgängligt så att det kan tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan begäran [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) har utfärdats, kommer meddelandet att levereras på nytt till programmet när detta startas om. Det här kallas ofta för *At Least Once Processing*, det vill säga, varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av egenskapen [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) för meddelandet. Detta är och förblir konstant under alla leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Följande exempel visar hur du tar bort ämnet **TestTopic** från tjänstenamnområdet **HowToSample**.

```csharp
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. Följande kod visar hur du tar bort en prenumeration med namnet **HighMessages** från ämnet **TestTopic**.

```csharp
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-ämnen och -prenumerationer, kan du följa dessa länkar om du vill veta mer.

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions].
* [Exempel på ämnesfilter][Topic filters sample]
* API-referens för [SqlFilter][SqlFilter].
* Skapa ett fungerande program som skickar och tar emot meddelanden till och från en Service Bus-kö: [.NET-självstudie om asynkron meddelandetjänst i Service Bus][Service Bus brokered messaging .NET tutorial].
* Service Bus-exempel: Hämta från [Azure-exempel][Azure samples] eller gå till [översikten](service-bus-samples.md).

[Azure portal]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Topic filters sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

