<properties
    pageTitle="Använda Service Bus-ämnen med .NET | Microsoft Azure"
    description="Lär dig hur du använder Service Bus-ämnen och -prenumerationer med .NET i Azure. Kodexemplen är skrivna för .NET-program."
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>


# Använda Service Bus-ämnen och -prenumerationer

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I den här artikeln beskrivs hur du använder Service Bus-ämnen och -prenumerationer. Exemplen är skrivna i C# och använder .NET-API:erna. Scenarier som tas upp är bland annat: skapa ämnen och prenumerationer, skapa prenumerationsfilter, skicka meddelanden till ett ämne, ta emot meddelanden från en prenumeration och ta bort ämnen och prenumerationer. Mer information om ämnen och prenumerationer, finns i avsnittet [Nästa steg](#next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Konfigurera programmet för att använda Service Bus

När du skapar ett program som använder Service Bus måste du lägga till en referens till Service Bus-sammansättningen och inkludera motsvarande namnområden. Det enklaste sättet att göra det på är att hämta ett lämpligt [NuGet](https://www.nuget.org)-paket.

## Hämta Service Bus-NuGet-paketet

[NuGet-paketet för Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) är det enklaste sättet att hämta Service Bus-API:er på och för att konfigurera ditt program med alla nödvändiga Service Bus-beroenden. Om du vill installera NuGet-paketet för Service Bus i din app gör du följande:

1.  Högerklicka på **Referenser** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
2.  Sök efter "Service Bus" och markera posten **Microsoft Azure Service Bus**. Klicka på **Installera** för att slutföra installationen och stäng sedan följande dialogruta:

    ![][7]

Du är nu redo att skriva kod för Service Bus.

## Skapa en anslutningssträng för Service Bus

Service Bus använder en anslutningssträng för att lagra slutpunkter och autentiseringsuppgifter. Du kan lägga anslutningssträngen i en konfigurationsfil istället för att hårdkoda den:

- När du använder Azure Services rekommenderar vi att du lagrar anslutningssträngen med hjälp av konfigurationssystemet för Azure-tjänster (.csdef- och .cscfg-filer).
- När du använder Azure Websites eller Azure Virtual Machines rekommenderar vi att du lagrar anslutningssträngen med konfigurationssystemet för .NET (till exempel Web.config-filen).

I båda fallen kan du hämta din anslutningssträng genom att använda metoden `CloudConfigurationManager.GetSetting`, som visas lite längre fram i denna artikel.

### Konfigurera anslutningssträngen

Med tjänstkonfigurationen kan du på ett dynamiskt sätt ändra konfigurationsinställningarna från [Azure Portal][] utan att behöva distribuera ditt program en gång till. Du kan till exempel lägga till en `Setting`-etikett till tjänstdefinitionsfilen (***.csdef**), enligt nästa exempel.

```
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

```
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

### Konfigurera anslutningssträngen när du använder Azure Websites eller Azure Virtual Machines

När du använder webbplatser eller Virtual Machines rekommenderar vi att du använder konfigurationssystemet för .NET (till exempel Web.config). Du sparar anslutningssträngen med hjälp av `<appSettings>`-elementet.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Använd det SAS-namn och de nyckelvärden som du hämtade från [Azure Portal][], enligt den tidigare beskrivningen.

## Skapa ett ämne

Du kan utföra hanteringsåtgärder för Service Bus-ämnen och -prenumerationer genom att använda klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Den här klassen innehåller metoder för att skapa, räkna upp och ta bort ämnen.

I följande exempel skapas ett `NamespaceManager`-objekt med klassen Azure`CloudConfigurationManager` som innehåller en anslutningssträng bestående av basadressen för namnområdet för Service Bus och lämpliga autentiseringsuppgifter för SAS med hanteringsbehörigheter. Den här anslutningssträngen har följande format:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Använd följande exempel, med konfigurationsinställningarna från föregående avsnitt som utgångspunkt.

```
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

Det finns överlagringar av [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx)-metoden som gör att du kan justera ämnets egenskaper (till exempel för att ställa in det förvalda TTL-värde (Time-To-Live) som ska tillämpas på meddelanden som skickas till ämnet. De här inställningarna tillämpas genom att använda [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx)-klassen. I följande exempel visas hur du skapar ett ämne med namnet **TestTopic**, med en maximal storlek på 5 GB och förvalt TTL-värde för meddelanden på 1 minut.

```
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

> [AZURE.NOTE] Du kan använda metoden [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) på [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-objekt för att kontrollera om det redan finns ett ämne med ett visst angivet namn i ett namnområde.

## Skapa en prenumeration

Du kan även skapa ämnesprenumerationer med hjälp av klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar den uppsättning av meddelanden som skickas till prenumerationens virtuella kö.

### Skapa en prenumeration med standardfiltret (MatchAll)

**MatchAll**-filtret är det standardfilter som används om inget filter anges när en ny prenumeration skapas. När du använder **MatchAll**-filtret kommer alla meddelanden som publiceras till ämnet att placeras i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet "AllMessages" och vi använder standardfiltret **MatchAll**.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### Skapa prenumerationer med filter

Du kan även ställa in filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas inom en viss ämnesprenumeration.

Den mest flexibla typen av filter som stöds av prenumerationerna är klassen [SqlFilter][] som implementerar en deluppsättning av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om uttryck som kan användas med ett SQL-filter finns i syntaxen [SqlFilter.SqlExpression][].

I följande exempel skapar vi en prenumeration med namnet **HighMessages** med ett [SqlFilter][]-objekt som endast sorterar ut meddelanden som har en anpassad **MessageNumber**-egenskap som är större än 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

På ett liknande sätt skapar vi i följande exempel en prenumeration med namnet **HighMessages** med ett [SqlFilter][]-objekt som endast väljer meddelanden som har en **MessageNumber**-egenskap som är mindre än eller lika med 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

När ett meddelande skickas till `TestTopic` levereras det alltid till mottagare som prenumererar på ämnesprenumerationen **AllMessages**. Det levereras selektivt till mottagare som prenumererar på ämnesprenumerationerna **HighMessages** och **LowMessages** (beroende på innehållet i meddelandena).

## Skicka meddelanden till ett ämne

Om du vill skicka ett meddelande till ett Service Bus-ämne skapar ditt program ett [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)-objekt med hjälp av anslutningssträngen.

Följande kod visar hur du skapar ett [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)-objekt för det **TestTopic**-ämne som skapats tidigare med hjälp av API-anropet [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Meddelanden som skickas till Service Bus-ämnen är instanser av klassen [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)-objekt har en uppsättning standardegenskaper (t.ex. [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) och [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), en ordlista som används för att lagra specifika egenskaper för anpassade program och en brödtext med godtyckliga programdata. Ett program kan konfigurera meddelandets brödtext genom att skicka någon typ av serialiserbara objekt till konstruktören av [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)-objektet. Sedan används önskad **DataContractSerializer** för att serialisera objektet. Ett annat alternativ är att tillhandahålla en **System.IO.Stream**.

Följande exempel visar hur du skickar fem testmeddelanden till objektet **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) som du fick i det förra kodexemplet. Observera att egenskapsvärdet [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) för varje meddelande varierar beroende på upprepning av loopen (detta avgör vilka prenumerationer som får det).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB. Den övre gränsen är högre om partitionering är aktiverad. Mer information finns i [Partitionerade meddelandeentiteter](service-bus-partitioning.md).

## Ta emot meddelanden från en prenumeration

Det rekommenderade sättet att ta emot meddelanden från en prenumeration är att använda objektet [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx). [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx)-objekt kan arbeta i två olika lägen: [*ReceiveAndDelete* och *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

När du använder läget **ReceiveAndDelete** är inleveransen en engångsåtgärd, det vill säga, när Service Bus tar emot en läsbegäran för ett meddelande i en prenumeration så markerar den meddelandet som förbrukat och skickar tillbaka det till programmet. Läget **ReceiveAndDelete** är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat så kommer programmet – när det startas om och börjar förbruka meddelanden igen – att ha missat meddelandet som förbrukades innan kraschen.

I **PeekLock**-läget (som är standardläget) blir inleveransprocessen en åtgärd i två steg. Tack vare det är det möjligt att stödja program som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), slutför programmet det andra steget i processen genom att anropa [Slutför](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) för det mottagna meddelandet. När Service Bus ser anropet **Slutför**, kommer det att markera meddelandet som förbrukat och det tas därefter bort från prenumerationen.

Följande exempel visar hur meddelanden kan tas emot och bearbetas med det förvalda **PeekLock**-läget. Om du vill ange ett annat värde för [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), kan du använda en annan överlagring för [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). I det här exemplet används motringningen [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) för att bearbeta meddelanden när de tas emot i prenumerationen **Highmessages**.

```
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

I det här exemplet konfigureras motringningen [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) med hjälp av objektet [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) är inställt på **false** för att du ska kunna ha manuell kontroll över när anropet [Slutför](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ska göras för det mottagna meddelandet. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) har ställts in på en minut, vilket leder till att klienten kommer att vänta i upp till en minut innan den avslutar funktionen för automatisk förnyelse och klienten gör ett nytt anrop för att söka efter meddelanden. Det här egenskapsvärdet minskar antalet gånger som klienten skickar avgiftsbelagda anrop som inte hämtar meddelanden.

## Hantera programkrascher och oläsbara meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagande program av något skäl inte kan bearbeta meddelandet kan det anropa metoden [Avbryt](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) för det mottagna meddelandet (i stället för metoden [Slutför](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Detta gör att Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt att tas emot igen, antingen genom att samma användningsprogram eller ett annat användningsprogram.

Det finns även en tidsgräns som är associerad med ett meddelande som ligger låst i prenumerationen. Om programmet misslyckas med att bearbeta meddelandet innan tidsgränsen för låsning går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och sedan göra det tillgängligt så att det kan tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan begäran [Slutför](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) har utfärdats, kommer meddelandet att levereras på nytt till programmet när detta startas om. Det här kallas ofta för *At Least Once Processing*, det vill säga, varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av egenskapen [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) för meddelandet. Detta är och förblir konstant under alla leveransförsök.

## Ta bort ämnen och prenumerationer

Följande exempel visar hur du tar bort ämnet **TestTopic** från tjänstenamnområdet **HowToSample**.

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. Följande kod visar hur du tar bort en prenumeration med namnet **HighMessages** från ämnet **TestTopic**.

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## Nästa steg

Nu när du har lärt dig grunderna om Service Bus-ämnen och -prenumerationer, kan du följa dessa länkar om du vill veta mer.

-   [Köer, ämnen och prenumerationer][].
-   [Exempel på ämnesfilter][]
-   API-referens för [SqlFilter][].
-   Skapa ett fungerande program som skickar och tar emot meddelanden till och från en Service Bus-kö: [.NET-självstudie om asynkrona meddelanden i Service Bus][].
-   Service Bus-exempel: Hämta från [Azure-exempel][] eller gå till [översikt](../service-bus/service-bus-samples.md).

  [Azure Portal]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Köer, ämnen och prenumerationer]: service-bus-queues-topics-subscriptions.md
  [Exempel på ämnesfilter]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [.NET-självstudie om asynkrona meddelanden i Service Bus]: service-bus-brokered-tutorial-dotnet.md
  [Azure-exempel]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2



<!--HONumber=Sep16_HO4-->


