---
title: Optimera dina Azure-kod i Visual Studio | Microsoft Docs
description: Lär dig mer om hur Azure-kod optimering verktygen i Visual Studio gör koden mer stabilt och bättre prestanda.
services: visual-studio-online
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ed48ee06-e2d2-4322-af22-07200fb16987
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/11/2016
ms.author: cawa
ms.openlocfilehash: f6949cec551d3a015ea6a1312ebe232d0643f473
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969624"
---
# <a name="optimizing-your-azure-code"></a>Optimera dina Azure-kod
När du programming appar som använder Microsoft Azure, finns det några kodningsexempel som du bör följa för att undvika problem med appen skalbarhet, beteende och prestanda i en molnmiljö. Microsoft tillhandahåller ett analysverktyg i Azure-kod som identifierar och identifierar de dessa ofta påträffade problem och hjälper dig att lösa dem. Du kan hämta verktyget i Visual Studio via NuGet.

## <a name="azure-code-analysis-rules"></a>Azure Kodanalys regler
Verktyget Azure kod använder följande regler för att automatiskt flagga din Azure-kod när det hittar kända problem som påverkar prestanda. Identifierade problem visas som en varningar eller fel. Kodfixar eller förslag för att lösa varningen eller felet sker ofta via en lampikonen.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Undvik att använda sessionstillståndsläge för standard (pågående)
### <a name="id"></a>ID
AP0000

### <a name="description"></a>Beskrivning
Om du använder sessionstillståndsläge för (pågående) i standard för molnprogram, kan du förlora sessionstillstånd.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Som standard är sessionstillståndsläge som angetts i web.config-filen i processen. Även om ingen post som angetts i konfigurationsfilen läge för sessionstillstånd som standard om du vill i processen. Läge i processen lagrar sessionstillstånd i minnet på webbservern. Sessionstillstånd lagras i minnet på webbservern sparas inte när en instans startas eller en ny instans används för belastningsutjämning eller stöd för redundans. Den här situationen hindrar programmet från att vara skalbar i molnet.

ASP.NET-sessionstillstånd har stöd för flera olika lagringsalternativ för sessionstillståndsdata: InProc, StateServer, SQLServer, anpassad, och av. Vi rekommenderar att du använder anpassade läge som värd för data på en extern lagring för sessionstillstånd, till exempel [Azure sessionstillståndsprovider för Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Lösning
En rekommenderad lösning är att lagra sessionstillståndet på en hanterad cachetjänst. Lär dig hur du använder [Azure sessionstillståndsprovider för Redis](http://go.microsoft.com/fwlink/?LinkId=401521) att lagra dina sessionstillstånd. Du kan också lagra sessionstillstånd på andra ställen för att se till att ditt program är skalbar i molnet. Mer information om alternativa lösningar Läs [Session tillstånd lägen](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Kör metoden får inte vara async
### <a name="id"></a>ID
AP1000

### <a name="description"></a>Beskrivning
Skapa asynkrona metoder (till exempel [await](https://msdn.microsoft.com/library/hh156528.aspx)) utanför det [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod och sedan anropa async-metoder från [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Deklarera de [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoden som asynkron gör worker-roll att ange en omstart-loop.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Anropa asynkrona metoder i den [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoden gör att cloud service runtime återanvänds worker-roll. När en arbetsroll startar alla programkörningen äger rum inom den [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod. Avslutar den [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoden gör att arbetsrollen startas om. När worker-rollen runtime når async-metod, skickar alla åtgärder efter async-metod och returnerar sedan. Detta leder till arbetsrollen avslutar den [ [ [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod och starta om. I nästa iteration av körningen, worker-roll kommer async-metod igen och startar om, gör den worker-rollen återanvänds samt igen.

### <a name="solution"></a>Lösning
Placera alla asynkrona åtgärder utanför den [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod. Anropa sedan omstruktureras async-metod från inuti den [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod, till exempel RunAsync () .wait. Verktyget Azure kod kan hjälpa dig att åtgärda problemet.

Följande kodavsnitt visar den kod lösa problemet:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Använd Service Bus signatur för delad åtkomst-autentisering
### <a name="id"></a>ID
AP2000

### <a name="description"></a>Beskrivning
Använda signatur för delad åtkomst (SAS) för autentisering. Access Control Service (ACS) är inaktuell för service bus-autentisering.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
För ökad säkerhet ersätter Azure Active Directory ACS-autentisering med SAS-autentisering. Se [Azure Active Directory är framtiden för ACS](https://cloudblogs.microsoft.com/enterprisemobility/2013/06/22/azure-active-directory-is-the-future-of-acs/) information om övergången planen.

### <a name="solution"></a>Lösning
Använd SAS-autentisering i dina appar. I följande exempel visar hur du använder en befintlig SAS-token för att få åtkomst till en service bus-namnområde eller enhet.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Se följande avsnitt för mer information.

* En översikt finns i [signatur autentisering för delad åtkomst med Service Bus](https://msdn.microsoft.com/library/dn170477.aspx)
* [Hur du använder autentisering med signatur för delad åtkomst med Service Bus](https://msdn.microsoft.com/library/dn205161.aspx)
* Exempelprojektet, se [autentisering med signatur för delad åtkomst (SAS) med Service Bus-prenumerationer](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Överväg att använda OnMessage metod för att undvika ”ta emot loop”
### <a name="id"></a>ID
AP2002

### <a name="description"></a>Beskrivning
Att undvika en ”får loop” anropa den **OnMessage** metoden är en bättre lösning för att ta emot meddelanden än anropar den **Receive** metod. Men om du måste använda den **Receive** metoden och ange en väntetid för icke-standard-server, se till att väntetid server är mer än en minut.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
När du anropar **OnMessage**, klienten startas ett internt meddelande pump som ständigt avsöker kö eller prenumeration. Det här meddelandet pump innehåller en oändlig loop som utfärdar ett anrop för att ta emot meddelanden. Om anropet uppnår tidsgränsen, skickar ett nytt anrop. Timeout-intervall bestäms av värdet för den [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) egenskapen för den [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)som används.

Fördelen med att använda **OnMessage** jämfört med **Receive** är att användarna inte behöver manuellt söka efter meddelanden, hantera undantag, bearbeta flera meddelanden parallellt och slutföra meddelanden.

Om du anropar **Receive** utan att använda dess standardvärde, måste den *ServerWaitTime* värdet är mer än en minut. Ange *ServerWaitTime* förhindrar att servern går ut innan meddelandet tas emot fullständigt till mer än en minut.

### <a name="solution"></a>Lösning
Se följande kodexempel för rekommenderade användningsområden. Mer information finns i [QueueClient.OnMessage-metoden (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)och [QueueClient.Receive-metoden (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

För att förbättra prestanda för Azure meddelandeinfrastrukturen, se designmönstret [introduktionen till asynkron meddelandehantering](https://msdn.microsoft.com/library/dn589781.aspx).

Följande är ett exempel på hur du använder **OnMessage** att ta emot meddelanden.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Följande är ett exempel på hur du använder **Receive** med standardservern väntetid.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Följande är ett exempel på hur du använder **Receive** med en icke-standard-server väntetid.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Överväg att använda asynkrona Service Bus-metoder
### <a name="id"></a>ID
AP2003

### <a name="description"></a>Beskrivning
Använda asynkrona Service Bus-metoder för att förbättra prestanda med asynkrona meddelanden.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Med hjälp av asynkrona metoder kan programmet programmet samtidighet eftersom körs varje anrop inte blockerar huvudtråden. När du använder Service Bus messaging metoder, utför en åtgärd (skicka, ta emot, ta bort osv.) tar tid. Den här gången innefattar bearbetning av åtgärden av Service Bus-tjänsten utöver svarstiden för begäran och svaret. Om du vill öka antalet åtgärder per tid måste operations köras samtidigt. Mer information finns i [bästa praxis för prestanda förbättringar med hjälp av Service Bus Brokered Messaging](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Lösning
Se [QueueClient klass (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) information om hur du använder den rekommenderade metoden för asynkron.

För att förbättra prestanda för Azure meddelandeinfrastrukturen, se designmönstret [introduktionen till asynkron meddelandehantering](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Överväg att partitionering Service Bus-köer och ämnen
### <a name="id"></a>ID
AP2004

### <a name="description"></a>Beskrivning
Partition Service Bus-köer och ämnen för bättre prestanda med Service Bus-meddelanden.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Partitionera Service Bus-köer och ämnen ökar prestanda-dataflöde och tjänstens tillgänglighet eftersom hela dataflödet för en partitionerad kö eller ett ämne begränsas inte längre av prestanda för en enda asynkron meddelandekö eller ett meddelandearkiv. Dessutom kan spärra ett tillfälligt avbrott i ett meddelandearkiv inte en partitionerad kö eller ett ämne. Mer information finns i [partitionering Meddelandeentiteter](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Lösning
Följande kodfragment visar hur du ska partitionera meddelandeentiteter.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Mer information finns i [partitionerad Service Bus-köer och ämnen | Microsoft Azure-bloggen](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) och ta en titt på [Microsoft Azure Service Bus partitionerad kö](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) exemplet.

## <a name="do-not-set-sharedaccessstarttime"></a>Ange inte SharedAccessStartTime
### <a name="id"></a>ID
AP3001

### <a name="description"></a>Beskrivning
Du bör undvika att använda SharedAccessStartTimeset till aktuell tid för att starta direkt princip för delad åtkomst. Du behöver bara använda den här egenskapen om du vill starta principen för delad åtkomst vid ett senare tillfälle.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Synkronisering av datorklocka orsakar en liten tidsskillnaden mellan datacenter. Exempelvis kan du tror logiskt att starttiden för en SAS-princip för lagring som den aktuella tiden med DateTime.Now eller orsakar en liknande metoden SAS-principen ska börja gälla omedelbart. Mindre tidsskillnaderna mellan Datacenter kan dock orsaka problem med den här eftersom datacenter ibland kan vara lite senare än starttiden, medan andra före den. Därför SAS-princip kan upphöra att gälla snabbt (eller även omedelbart) om principen livslängd har angetts för kort.

Mer information om hur du använder signatur för delad åtkomst i Azure storage finns [introduktion till Table SAS (signatur för delad åtkomst), Queue SAS och uppdatering till Blob SAS - Microsoft Azure Storage-teamets blogg - Site Start - MSDN-bloggarna](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Lösning
Ta bort instruktionen som anger starttiden för princip för delad åtkomst. Verktyget Azure kod är en lösning på problemet. Mer information om hanteringen av informationssäkerhet finns designmönstret [Valet-Nyckelmönster](https://msdn.microsoft.com/library/dn568102.aspx).

Följande kodavsnitt visar den kod lösa problemet.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Delad åtkomstprincip förfallotiden måste vara mer än fem minuter
### <a name="id"></a>ID
AP3002

### <a name="description"></a>Beskrivning
Det kan vara så mycket som en fem minuters skillnad i klockor mellan Datacenter på olika platser på grund av ett villkor som kallas ”klockan skeva”. Om du vill förhindra SAS ange principen token upphör att gälla tidigare än planerat, förfallotiden ska vara mer än fem minuter.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Datacenter på olika platser runt om i världen synkronisera genom en signal för klockan. Eftersom det tar tid för klocksignalen att resa till olika platser, kan det vara en tid avvikelse mellan Datacenter på olika geografiska platser men allt är synkroniserad. Den här tidsskillnaden kan påverka den Shared Access start tid och upphör att gälla avsökningsintervallet för klientprinciper. Därför för att säkerställa princip för delad åtkomst träder i kraft omedelbart, inte ange starttiden. Kontrollera dessutom att förfallotiden är mer än 5 minuter att förhindra att tidsgränsen för tidig.

Mer information om hur du använder signatur för delad åtkomst i Azure storage finns i [introduktion till Table SAS (signatur för delad åtkomst), Queue SAS och uppdatering till Blob SAS - Microsoft Azure Storage-teamets blogg - Site Start - MSDN-bloggarna](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Lösning
Mer information om hanteringen av informationssäkerhet finns designmönstret [Valet-Nyckelmönster](https://msdn.microsoft.com/library/dn568102.aspx).

Följande är ett exempel på att inte ange en starttid för delad åtkomst-principen.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Följande är ett exempel på att ange en starttid för delad åtkomst princip med en princip giltighetstid som är större än fem minuter.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Mer information finns i [skapar och använder en signatur för delad åtkomst](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Använda CloudConfigurationManager
### <a name="id"></a>ID
AP4000

### <a name="description"></a>Beskrivning
Med hjälp av den [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) klassen för projekt som Azure-webbplats och Azure mobila tjänster inte införa körningsproblem. Som bästa praxis, det är dock en bra idé att använda molnet[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) som ett enhetligt sätt att hantera konfigurationer för alla Azure-molnprogram.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
CloudConfigurationManager läser lämpligt att programmiljön konfigurationsfilen.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Lösning
Omstrukturera din kod för att använda den [CloudConfigurationManager-klassen](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). En kod korrigering av det här problemet tillhandahålls av verktyget Azure kod.

Följande kodavsnitt visar den kod lösa problemet. Ersätt

`var settings = ConfigurationManager.AppSettings["mySettings"];`

med 

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Här är ett exempel på hur du lagrar Konfigurationsinställningen i en App.config eller Web.config-fil. Lägga till inställningarna till avsnittet appSettings i konfigurationsfilen. Följande är Web.config-filen för det förra exemplet.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Undvik att använda hårdkodade anslutningssträngar
### <a name="id"></a>ID
AP4001

### <a name="description"></a>Beskrivning
Om du använder hårdkodade anslutningssträngar och du måste uppdatera dem senare, måste du göra ändringar i källkoden och kompilera om programmet. Om du sparar anslutningssträngarna i en konfigurationsfil, kan du dock ändra dem senare genom att helt enkelt uppdatera konfigurationsfilen.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Hårdkoda anslutningssträngar är en felaktig idé eftersom problem när anslutningssträngar måste ändras snabbt. Dessutom om projektet måste checkas in till källkontroll, introducerar hårdkodade anslutningssträngar säkerhetsrisker eftersom strängarna kan ses i källkoden.

### <a name="solution"></a>Lösning
Store-anslutningssträngar i konfigurationsfiler eller Azure-miljöer.

* För fristående program kan du använda app.config för att lagra inställningar för anslutningssträngen.
* Använd web.config för att lagra anslutningssträngar för IIS-värdbaserade webbprogram.
* Använd configuration.json för att lagra anslutningssträngar för ASP.NET vNext-program.

Information om hur du använder konfigurationer filer, till exempel web.config eller app.config finns i [riktlinjerna för ASP.NET Web](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx). Information om hur Azure miljö variabler fungerar finns i [Azure Web Sites: hur Programsträngar och anslutningen strängar fungerar](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Mer information om hur du lagrar anslutningssträngen i källkontroll finns i [Undvik att placera känslig information, till exempel anslutningssträngar i filer som lagras i lagringsplatsen för källkod](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Använd diagnostik-konfigurationsfil
### <a name="id"></a>ID
AP5000

### <a name="description"></a>Beskrivning
I stället för att konfigurera diagnostikinställningar i din kod som genom att använda Microsoft.WindowsAzure.Diagnostics programming API, bör du konfigurera inställningarna för startdiagnostik i filen diagnostics.wadcfg. (Eller diagnostics.wadcfgx om du använder Azure SDK 2.5). Du kan ändra inställningarna för startdiagnostik utan att behöva kompilera om din kod genom att göra detta.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Innan Azure SDK 2.5 (som använder Azure-diagnostik 1.3), Azure Diagnostics SÄKERHETSSPECIFIKA kunde konfigureras med hjälp av flera olika metoder: lägger till den konfigurationen blob i lagring, med hjälp av tvingande kod, deklarativa konfiguration eller standard konfiguration. Det bästa sättet att konfigurera diagnostik är dock att använda en XML-konfigurationsfil (diagnostics.wadcfg eller diagnositcs.wadcfgx för SDK 2.5 och senare) i programprojektet. Den här metoden kan kan filen diagnostics.wadcfg helt definierar konfigurationen och uppdateras och distribueras om när du vill. Blanda användningen av konfigurationsfilen diagnostics.wadcfg med programmering av angett de konfigurationer med hjälp av den [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)eller [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)klasser kan leda till förvirring. Se [initieras eller ändra Azure Diagnostics-konfiguration](https://msdn.microsoft.com/library/azure/hh411537.aspx) för mer information.

Från och med WAD 1.3 (ingår i Azure SDK 2.5) kan den inte längre går att använda koden för att konfigurera diagnostik. Därför kan du bara ange konfigurationen när du tillämpar eller uppdaterar diagnostics-tillägg.

### <a name="solution"></a>Lösning
Använd diagnostik configuration designer för att flytta diagnostikinställningar till konfigurationsfilen diagnostik (diagnositcs.wadcfg eller diagnositcs.wadcfgx för SDK 2.5 och senare). Vi rekommenderar också att du installerar [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) och använder funktionen senaste diagnostik.

1. Välj Egenskaper på snabbmenyn för den roll som du vill konfigurera, och sedan klicka på fliken konfiguration.
2. I den **diagnostik** avsnittet, se till att den **aktivera diagnostik** kryssrutan är markerad.
3. Välj den **konfigurera** knappen.

   ![Åtkomst till alternativet Aktivera diagnostik](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   Se [konfigurera diagnostik för Azure Cloud Services och virtuella datorer](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) för mer information.

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Undvik att deklarera DbContext-objekt som statiskt
### <a name="id"></a>ID
AP6000

### <a name="description"></a>Beskrivning
Undvik att deklarera DBContext-objekt som statisk för att spara minne.

Dela dina idéer och feedback på [Azure Kodanalys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
DBContext-objekt innehåller resultatet av frågan från varje anrop. Statiska DBContext-objekt är inte bort förrän programdomänen tas bort. Därför kan ett statiskt DBContext-objekt förbruka stora mängder minne.

### <a name="solution"></a>Lösning
Deklarera DBContext som en lokal variabel eller icke-statisk instansfält, använda den för en aktivitet och låt den vara kasseras när du har använt.

I följande exempel MVC kontrollantklassen visar hur du använder DBContext-objektet.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du optimerar och felsökning av Azure-appar i [felsöka en webbapp i Azure App Service med Visual Studio](app-service/web-sites-dotnet-troubleshoot-visual-studio.md).
