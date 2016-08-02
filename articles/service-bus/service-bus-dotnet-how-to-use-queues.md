<properties
    pageTitle="Använda Service Bus-köer (.NET) | Microsoft Azure"
    description="Lär dig hur du använder Service Bus-köer i Azure. Kodexempel som skrivits i C# med hjälp av .NET-API:er."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sethm"/>

# Använda Service Bus-köer

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här artikeln beskriver hur du använder Service Bus-köer. Exemplen är skrivna i C# och använder .NET-API:er. Scenarier som tas upp inkluderar skapande av köer samt att skicka och ta emot meddelanden. Mer information om köer finns i avsnittet [Nästa steg](#next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Lägga till Service Bus-NuGet-paketet

[Service Bus-NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) är det enklaste sättet att komma åt Service Bus-API:et och att konfigurera din app med alla Service Bus-beroenden. Om du vill installera NuGet-paketet i din app, ska du göra följande:

1.  Högerklicka på **Referenser** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
2.  Sök efter ”Service Bus” och markera posten **Microsoft Azure Service Bus**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.

    ![][7]

Du är nu redo att skriva kod för Service Bus.

## Konfigurera en anslutningssträng för Service Bus

Service Bus använder en anslutningssträng för att lagra slutpunkter och autentiseringsuppgifter. Du kan lägga anslutningssträngen i en konfigurationsfil istället för att hårdkoda den:

- När du använder Azure Cloud Services, rekommenderas du att lagra anslutningssträngen med hjälp av konfigurationssystemet för Azure-tjänster (.csdef- och .cscfg-filer).
- När du använder Azure Websites eller Azure Virtual Machines, rekommenderas du att lagra anslutningssträngen med konfigurationssystemet för .NET (till exempel Web.config-filen).

I båda fallen kan du hämta din anslutningssträng genom att använda metoden [CloudConfigurationManager.GetSetting][GetSetting], som visas lite längre fram i denna artikel.

### Konfigurera anslutningssträngen

Mekanismen för tjänstekonfiguration gör att du på ett dynamiskt sätt kan ändra konfigurationsinställningarna från den [klassiska Azure-portalen][], utan att omdistribuera din app. Du kan till exempel lägga till en `Setting`-etikett till tjänstedefinitionsfilen (.csdef), som visas i nästa exempel.

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
Du anger sedan värden i tjänstekonfigurationsfilen (.cscfg), som visas i nästa exempel.

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

Använd SAS-nyckelnamnet (signatur för delad åtkomst) och de nyckelvärden som hämtades från den klassiska Azure-portalen, enligt beskrivningen i det förra avsnittet.

### Konfigurera anslutningssträngen när du använder webbplatser eller Azure Virtual Machines

När du använder webbplatser eller Virtual Machines, rekommenderas du att använda konfigurationssystemet för .NET (till exempel **Web.config**). Du sparar anslutningssträngen med hjälp av `<appSettings>`-elementet.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Använd namn på SAS och nyckelvärden som du hämtade från den klassiska Azure-portalen, enligt beskrivningen i föregående avsnitt.

## Skapa en kö

Du kan utföra hanteringsåtgärder för Service Bus-köer genom att använda klassen [NamespaceManager][]. Den här klassen innehåller metoder för att skapa, räkna upp och ta bort köer.

I det här exemplet skapas ett [NamespaceManager][]-objekt med klassen Azure [CloudConfigurationManager][] som innehåller en anslutningssträng bestående av basadressen för tjänstenamnområdet för Service Bus och lämpliga autentiseringsuppgifter för SAS med hanteringsbehörigheter. Den här anslutningssträngen är av den form som visas i följande exempel.

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

Använd följande exempel, med konfigurationsinställningarna från föregående avsnitt som utgångspunkt.

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

Det finns överlagringar av [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx)-metoden som gör att du kan finjustera köns egenskaper (till exempel för att ställa in det förvalda TTL-värde [Time-To-Live] som ska tillämpas på meddelanden som skickas till kön). De här inställningarna tillämpas genom att använda [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)-klassen. I följande exempel visas hur du skapar en kö med namnet `TestQueue`, med en maximal storlek på 5 GB och förvalt TTL-värde för meddelanden på 1 minut.

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE] Du kan använda metoden [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) på [NamespaceManager][]-objekt för att kontrollera om det redan finns en kö med ett visst angivet namn i ett namnområde för tjänsten.

## Skicka meddelanden till en kö

Om du vill skicka ett meddelande till en Service Bus-kö skapar ditt program ett [QueueClient][]-objekt med hjälp av anslutningssträngen.

Följande kod visar hur du skapar ett [QueueClient][]-objekt för den `TestQueue`-kö som du just har skapat med hjälp av API-anropet [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

Meddelanden som skickas till och tas emot från Service Bus-köer är instanser av klassen [BrokeredMessage][]. [BrokeredMessage][]-objekt har en uppsättning standardegenskaper (t.ex. [etikett](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) och [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), en ordlista som används för att lagra specifika egenskaper för anpassade program och en brödtext med godtyckliga programdata. Ett program kan konfigurera meddelandets brödtext genom att skicka någon typ av serialiserbara objekt till konstruktören av [BrokeredMessage][]-objektet. Sedan används önskad **DataContractSerializer** för att serialisera objektet. Om du vill kan du också ange ett **System.IO.Stream**-objekt.

Följande exempel visar hur du skickar fem testmeddelanden till objektet `TestQueue` [QueueClient][] som du fick i det förra exemplet.

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Den övre gränsen är högre om partitionering är aktiverad. Mer information finns i [Partitionerade meddelandeentiteter](service-bus-partitioning.md).

## Ta emot meddelanden från en kö

Det rekommenderade sättet att ta emot meddelanden från en kö är att använda objektet [QueueClient][]. [QueueClient][]-objekt kan arbeta i två olika lägen: [ReceiveAndDelete och PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

När du använder läget **ReceiveAndDelete** är inleveransen en engångsåtgärd, det vill säga att när Service Bus tar emot en läsbegäran för ett meddelande i en kö så markerar den meddelandet som förbrukat och skickar tillbaka det till programmet. **ReceiveAndDelete** är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som förbrukat så kommer programmet – när det startas om och börjar förbruka meddelanden igen – att ha missat meddelandet som förbrukades innan kraschen.

I **PeekLock**-läget (som är standardläget) blir inleveransen en åtgärd i två steg. Tack vare det är det möjligt att stödja program som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), slutför programmet det andra steget i processen genom att anropa [Slutför][] för det mottagna meddelandet. När Service Bus ser anropet [Slutför][], kommer det att markera meddelandet som förbrukat och det tas därefter bort från kön.

Följande exempel visar hur meddelanden kan tas emot och bearbetas med det förvalda **PeekLock**-läget. Om du vill ange ett annat värde för [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), kan du använda en annan överlagring för [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). I det här exemplet används motringningen [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) för att bearbeta meddelanden när de tas emot i `TestQueue`.

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

I det här exemplet konfigureras motringningen [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) med hjälp av objektet [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) är inställt på **false** för att du ska kunna ha manuell kontroll över när anropet [Slutför][] ska göras för det mottagna meddelandet. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) har ställts in på en minut, vilket leder till att klienten kommer att vänta i upp till en minut för ett meddelande innan anropet uppnår tidsgränsen och klienten gör ett nytt anrop för att söka efter meddelanden. Det här egenskapsvärdet minskar antalet gånger som klienten skickar avgiftsbelagda anrop som inte hämtar meddelanden.

## Hantera programkrascher och oläsbara meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i appen eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram av något skäl inte kan bearbeta meddelandet, kan det anropa metoden [Avbryt](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) för det mottagna meddelandet (i stället för metoden [Slutför][]). Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerad med ett meddelande som ligger låst i kön. Om programmet misslyckas med att bearbeta meddelandet innan tidsgränsen för låsning går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och sedan göra det tillgängligt så att det kan tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan begäran [Slutför][] har utfärdats, kommer meddelandet att levereras på nytt till programmet när detta startas om. Det här kallas ofta för **At Least Once Processing**, det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av egenskapen [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) för meddelandet. Detta är och förblir konstant under alla leveransförsök.

## Nästa steg

Nu när du har lärt dig grunderna om Service Bus-köer, kan du följa dessa länkar om du vill veta mer.

-   Läs om meddelandeentiteter för Service Bus i [Köer, ämnen och prenumerationer][].
-   Skapa en fungerande app som skickar och tar emot meddelanden till och från en Service Bus-kö med [.NET-självstudiekurs om asynkrona meddelanden i Service Bus][].
-   Hämta Service Bus-exempel från [Azure-exempel][] eller gå till [översikt över Service Bus-exempel][].

  [klassiska Azure-portalen]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Köer, ämnen och prenumerationer]: service-bus-queues-topics-subscriptions.md
  [.NET-självstudiekurs om asynkrona meddelanden i Service Bus]: service-bus-brokered-tutorial-dotnet.md
  [Azure-exempel]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [översikt över Service Bus-exempel]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Slutför]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx


<!--HONumber=Jun16_HO2-->


