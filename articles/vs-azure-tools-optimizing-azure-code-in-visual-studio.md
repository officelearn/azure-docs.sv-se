---
title: Optimera din Azure kod i Visual Studio | Microsoft Docs
description: Lär dig mer om hur Azure kod optimering verktyg i Visual Studio gör din kod mer stabilt och bättre prestanda.
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
ms.workload: multiple
ms.date: 11/11/2016
ms.author: cawa
ms.openlocfilehash: 3ee2cc3ac5098ebf205331167faffa2b5f9b6d56
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937565"
---
# <a name="optimizing-your-azure-code"></a>Optimera din Azure kod
När du programmering appar som använder Microsoft Azure, finns det vissa kodningsexempel som du bör följa för att undvika problem med appen skalbarhet, beteende och prestanda i en molnmiljö. Microsoft tillhandahåller ett analysverktyg i Azure kod som identifierar och identifierar de dessa ofta påträffade problem och hjälper dig att lösa dem. Du kan hämta verktyget i Visual Studio via NuGet.

## <a name="azure-code-analysis-rules"></a>Azure kod Analysis-regler
Verktyget Azure kod används följande regler för att flaggan Azure koden automatiskt när den söker efter kända problem som påverkar prestanda. Identifierat problem visas som en varningar eller fel. Koden korrigeringar eller förslag för att lösa varningen eller felet sker ofta via en lampikonen.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Undvik att använda sessionstillståndsläge för standard (pågående)
### <a name="id"></a>ID
AP0000

### <a name="description"></a>Beskrivning
Om du använder den standard (pågående) sessionstillståndsläge för molnprogram, förlorar du sessionstillstånd.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Som standard är sessionstillståndsläge som angetts i web.config-filen i processen. Även om ingen post som angetts i konfigurationsfilen standardvärden läge för sessionstillstånd om du vill i processen. Läget i processen lagrar sessionstillstånd i minnet på webbservern. Sessionstillstånd lagras i minnet på webbservern sparas inte när en instans har startats om eller en ny instans används för belastningsutjämning eller stöd för redundans. Detta hindrar programmet från att vara skalbar i molnet.

Sessionstillståndet ASP.NET stöder flera olika lagringsalternativ för sessionstillståndsdata: InProc, StateServer, SQLServer, anpassad, och ut. Det rekommenderas att du använder läget för anpassade inställningar som värd för data på en extern sessionstillstånd butik som [Azure sessionstillståndsprovider för Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Lösning
En rekommenderad lösning är att lagra sessionstillstånd i en hanterad cache-tjänst. Lär dig hur du använder [Azure sessionstillståndsprovider för Redis](http://go.microsoft.com/fwlink/?LinkId=401521) att lagra dina sessionstillstånd. Du kan också lagra sessionstillstånd i andra platser att se till att programmet är skalbart i molnet. Mer information om alternativa lösningar Läs [Session tillstånd lägen](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Kör metoden får inte vara asynkrona
### <a name="id"></a>ID
AP1000

### <a name="description"></a>Beskrivning
Skapa asynkrona metoder (som [await](https://msdn.microsoft.com/library/hh156528.aspx)) utanför det [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod och sedan anropa de asynkron metoderna från [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Deklarerar den [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoden som asynkron gör worker-rollen att ange en omstart loop.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Anropar asynkrona metoder i den [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoden gör cloud service runtime att återvinna arbetsrollen. När en arbetsroll startar alla programkörningen äger rum inom den [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod. Avslutar den [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoden gör arbetsrollen att starta om. När worker-rollen runtime träffar async-metod, skickar alla åtgärder efter async-metod och returnerar sedan. Detta gör att arbetsrollen du avsluta den [ [ [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod och starta om. I nästa version av körningen arbetsrollen träffar asynkrona metoden igen och startar om orsakar arbetsrollen att återvinna samt igen.

### <a name="solution"></a>Lösning
Placera alla asynkrona åtgärder utanför den [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod. Anropa sedan metoden omstrukturerade asynkrona från inuti den [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod, till exempel RunAsync () .wait. Verktyget Azure kod kan hjälpa dig att lösa problemet.

Följande kodavsnitt visar koden korrigering för problemet:

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

## <a name="use-service-bus-shared-access-signature-authentication"></a>Använda Service Bus signatur för delad åtkomst-autentisering
### <a name="id"></a>ID
AP2000

### <a name="description"></a>Beskrivning
Använda delade signatur åtkomst (SAS) för autentisering. Access Control Service (ACS) är inaktuell för service bus-autentisering.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
För förbättrad säkerhet ersätter Azure Active Directory ACS-autentisering med SAS-autentisering. Se [Azure Active Directory är framtiden för ACS](https://cloudblogs.microsoft.com/enterprisemobility/2013/06/22/azure-active-directory-is-the-future-of-acs/) information om övergång planen.

### <a name="solution"></a>Lösning
Använd SAS-autentisering i dina appar. I följande exempel visar hur du använder en befintlig SAS-token för åtkomst till en service bus-namnrymd eller enhet.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Finns i följande avsnitt för mer information.

* En översikt finns [signatur autentisering för delad åtkomst med Service Bus](https://msdn.microsoft.com/library/dn170477.aspx)
* [Hur du använder autentisering med signatur för delad åtkomst med Service Bus](https://msdn.microsoft.com/library/dn205161.aspx)
* En exempelprojektet finns [autentisering med delad signatur åtkomst (SAS) med Service Bus-prenumerationer](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Överväg att använda OnMessage metod för att undvika ”meddelandemottagning”
### <a name="id"></a>ID
AP2002

### <a name="description"></a>Beskrivning
Att undvika att gå till en ”meddelandemottagning”, anropar den **OnMessage** metoden är en bättre lösning för att ta emot meddelanden än anropar den **Receive** metod. Men om du måste använda den **Receive** metod och ange väntetiden för en icke-standard-servern, kontrollera server väntetiden är mer än en minut.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
När du anropar **OnMessage**, klienten startar ett internt meddelande pump som ständigt avsöker kö eller prenumeration. Det här meddelandet pump innehåller en oändlig loop som utfärdar ett anrop för att ta emot meddelanden. Om anropet uppnår tidsgränsen, utfärdas ett nytt anrop. Timeout-intervall bestäms av värdet för den [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) -egenskapen för den [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)som används.

Fördelen med att använda **OnMessage** jämfört med **Receive** är att användarna inte behöver manuellt söka efter meddelanden, hantera undantag, bearbeta flera meddelanden parallellt och slutföra meddelanden.

Om du anropar **Receive** utan att använda standardvärdet, måste den *ServerWaitTime* värdet är mer än en minut. Ange *ServerWaitTime* till mer än en minut förhindrar att servern timeout innan det är fullständigt ta emot meddelandet.

### <a name="solution"></a>Lösning
Se följande kodexempel för rekommenderade användningsområden. Mer information finns i [QueueClient.OnMessage-metoden (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)och [QueueClient.Receive-metoden (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

För att förbättra prestanda för Azure meddelandeinfrastrukturen finns designmönstret [asynkrona meddelanden introduktion](https://msdn.microsoft.com/library/dn589781.aspx).

Följande är ett exempel på hur **OnMessage** ta emot meddelanden.

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

Följande är ett exempel på hur **ta emot** med standardservern väntetid.

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

Följande är ett exempel på hur **Receive** väntetid med en server som inte är standard.

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

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
När du använder asynkrona metoder kan programmet programmet samtidighet eftersom körs varje anrop inte blockerar huvudtråden. När du använder Service Bus messaging metoder, utför en åtgärd (skicka, ta emot, ta bort, etc.) tar tid. Den här gången innefattar bearbetning av åtgärden av tjänsten Service Bus förutom svarstid för begäran och svar. Om du vill öka antalet åtgärder per tid måste operations köras samtidigt. Mer information finns i [bästa praxis för prestanda förbättringar med Service Bus asynkrona meddelanden](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Lösning
Se [QueueClient klass (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) information om hur du använder den rekommenderade asynkrona metoden.

För att förbättra prestanda för Azure meddelandeinfrastrukturen finns designmönstret [asynkrona meddelanden introduktion](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Överväg att partitionering Service Bus-köer och ämnen
### <a name="id"></a>ID
AP2004

### <a name="description"></a>Beskrivning
Partitionen Service Bus-köer och ämnen för bättre prestanda med Service Bus-meddelanden.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Partitionering Service Bus-köer och ämnen ökar prestanda genomflöde och tjänsten tillgänglighet eftersom det totala genomflödet i en partitionerad kö eller ett ämne begränsas inte längre av prestanda i ett enda meddelande broker eller meddelandearkiv. Dessutom kan spärra ett tillfälligt avbrott i ett meddelandearkiv inte en partitionerad kö eller ett ämne. Mer information finns i [partitionering Meddelandeentiteter](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Lösning
Följande kodavsnitt visar hur du partitionera meddelandeentiteter.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Mer information finns i [partitionerad Service Bus-köer och ämnen | Microsoft Azure-blogg](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) och checka ut den [Microsoft Azure Service Bus partitionerad-kö](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) exempel.

## <a name="do-not-set-sharedaccessstarttime"></a>Ange inte SharedAccessStartTime
### <a name="id"></a>ID
AP3001

### <a name="description"></a>Beskrivning
Du bör undvika att använda SharedAccessStartTimeset till aktuell tid för att starta direkt princip för delad åtkomst. Du behöver bara använda den här egenskapen om du vill starta princip för delad åtkomst vid ett senare tillfälle.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Synkronisering av datorklocka medför en lätt tidsskillnaden mellan datacenter. Till exempel skulle du logiskt tror att starttiden för lagring SAS-principen som den aktuella tiden med DateTime.Now eller orsakar en liknande metoden SAS-principen ska gälla omedelbart. Mindre tidsskillnaderna mellan Datacenter kan emellertid orsaka problem med den här eftersom datacenter ibland kan vara lite senare än starttiden, medan andra i den. Därför SAS-principen kan gälla snabbt (eller även omedelbart) om principen livstid är för kort.

Mer information om med signatur för delad åtkomst på Azure storage finns [introduktion till tabellen SAS (signatur för delad åtkomst), Queue SAS och uppdaterar till Blob SAS - Microsoft Azure Storage-teamets blogg - platsen Start - MSDN-bloggarna](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Lösning
Ta bort den instruktion som anger starttiden för princip för delad åtkomst. Verktyget Azure koden innehåller en korrigering för problemet. Mer information om säkerhetshantering finns designmönstret [Valet nyckeln mönster](https://msdn.microsoft.com/library/dn568102.aspx).

Följande kodavsnitt visar koden korrigering för problemet.

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

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Delad åtkomstprincip förfallotiden måste vara fler än fem minuter
### <a name="id"></a>ID
AP3002

### <a name="description"></a>Beskrivning
Det kan finnas lika mycket som en fem minuters skillnad i klockor mellan Datacenter på olika platser på grund av ett tillstånd som kallas ”klockavvikelser”. För att förhindra SAS ange principen token upphör att gälla tidigare än planerat, förfallotiden vara mer än fem minuter.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Datacenter på olika platser över hela världen synkronisera genom en signal klockan. Eftersom det tar tid för klockan signal överföras till olika platser, kan det finnas en gång avvikelse mellan Datacenter på olika geografiska platser trots allt är synkroniserad. Den här tidsskillnaden kan påverka Shared Access princip start tid och förfallodatum för intervallet. Därför för att säkerställa princip för delad åtkomst träder i kraft omedelbart, inte ange starttiden. Kontrollera dessutom att förfallotiden är mer än 5 minuter för att förhindra tidig timeout.

Mer information om hur du använder signatur för delad åtkomst på Azure storage finns [introduktion till tabellen SAS (signatur för delad åtkomst), Queue SAS och uppdaterar till Blob SAS - Microsoft Azure Storage-teamets blogg - platsen Start - MSDN-bloggarna](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Lösning
Mer information om säkerhetshantering finns designmönstret [Valet nyckeln mönster](https://msdn.microsoft.com/library/dn568102.aspx).

Följande är ett exempel på inte att ange en starttid för delad åtkomst princip.

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

Följande är ett exempel på att ange en starttid för delad åtkomst principen med princip upphör att gälla mer än fem minuter.

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

## <a name="use-cloudconfigurationmanager"></a>Använd CloudConfigurationManager
### <a name="id"></a>ID
AP4000

### <a name="description"></a>Beskrivning
Med hjälp av den [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) klassen för projekt som Azure-webbplats och Azure mobile services inte introducerar runtime problem. Som bästa praxis, men det är en bra idé att använda molnet[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) som ett enhetligt sätt att hantera konfigurationer för alla program i Azure-molnet.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
CloudConfigurationManager läser konfigurationsfilen som är lämpliga för program-miljö.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Lösning
Refactor din kod för att använda den [CloudConfigurationManager-klassen](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). En kod korrigering för problemet tillhandahålls av verktyget Azure kod.

Följande kodavsnitt visar koden korrigering för problemet. Ersätt

`var settings = ConfigurationManager.AppSettings["mySettings"];`

med 

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Här är ett exempel på hur du lagrar Konfigurationsinställningen i App.config eller Web.config-filen. Lägga till inställningarna i avsnittet AppSettings i konfigurationsfilen. Följande är Web.config-filen för det förra kodexemplet.

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
Om du använder hårdkodade anslutningssträngar och du måste uppdatera dem senare, måste du göra ändringar i källkoden och kompilerar om programmet. Men om du sparar din anslutningssträngar i en konfigurationsfil, kan du ändra dem senare genom att helt enkelt uppdatera konfigurationsfilen.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Hårdkoda anslutningssträngar är felaktigt eftersom problem när anslutningssträngar behöver snabbt ska kunna ändras. Dessutom om projektet måste checkas in till källkontroll, införa hårdkodade anslutningssträngar säkerhetsrisker eftersom strängarna som kan visas i källkoden.

### <a name="solution"></a>Lösning
Lagrar anslutningssträngar i configuration-filer eller miljöer i Azure.

* Använda app.config för att lagra strängen anslutningsinställningar för fristående program.
* Använda web.config för att lagra anslutningssträngar för IIS-värdbaserade webbprogram.
* Använda configuration.json för att lagra anslutningssträngar för ASP.NET-program vNext.

Information om hur du använder konfigurationer filer, till exempel web.config eller app.config finns [riktlinjer för konfiguration av ASP.NET Web](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx). Mer information om hur Azure miljö variabler arbete finns [Azure webbplatser: hur programmet strängar och anslutningen strängar fungerar](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Mer information om lagra anslutningssträngen i källkontrollen finns [undvika att sätta känslig information, till exempel anslutningssträngar i filer som lagras i koden källdatabasen](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Använd diagnostik-konfigurationsfil
### <a name="id"></a>ID
AP5000

### <a name="description"></a>Beskrivning
I stället för att konfigurera diagnostikinställningar i din kod som genom att använda Microsoft.WindowsAzure.Diagnostics programmering API, bör du konfigurera inställningarna för webbprogramdiagnostik i filen diagnostics.wadcfg. (Eller diagnostics.wadcfgx om du använder Azure SDK 2.5). Du kan ändra inställningarna för webbprogramdiagnostik utan att kompilera om din kod genom att göra.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
Innan Azure SDK 2,5 (som använder Azure diagnostics 1.3), Azure Diagnostics (BOMULLSTUSS) kan konfigureras med hjälp av flera olika metoder: lägger till den konfiguration blob i lagring, med hjälp av tvingande koden, deklarativa konfiguration eller standard konfiguration. Det bästa sättet att konfigurera diagnostik är dock att använda en XML-konfigurationsfil (diagnostics.wadcfg eller diagnositcs.wadcfgx för SDK-2.5 och senare) i projektet för konsolprogrammet. I den här metoden kan diagnostics.wadcfg filen helt definierar konfigurationen och uppdateras och omdistribueras när du vill. Blandning av användningen av konfigurationsfilen diagnostics.wadcfg med programmering för konfigurationer med hjälp av den [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)eller [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)klasser kan leda till förvirring. Se [initieras eller ändra Azure Diagnostics konfiguration](https://msdn.microsoft.com/library/azure/hh411537.aspx) för mer information.

Från och med BOMULLSTUSS 1.3 (ingår i Azure SDK 2.5) kan den inte längre att använda koden för att konfigurera diagnostik. Därför kan du bara ange konfigurationen när du tillämpar eller uppdaterar tillägget diagnostik.

### <a name="solution"></a>Lösning
Använd diagnostik configuration designer för att flytta diagnostikinställningar till konfigurationsfilen diagnostik (diagnositcs.wadcfg eller diagnositcs.wadcfgx för SDK-2.5 och senare). Det rekommenderas också att du installerar [Azure SDK 2,5](http://go.microsoft.com/fwlink/?LinkId=513188) och använder funktionen senaste diagnostik.

1. Välj Egenskaper på snabbmenyn för den roll som du vill konfigurera och välj sedan fliken konfiguration.
2. I den **diagnostik** avsnittet, se till att den **aktivera diagnostik** är markerad.
3. Välj den **konfigurera** knappen.

   ![Åtkomst till alternativet Aktivera diagnostik](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   Se [konfigurera diagnostik för virtuella datorer och Azure Cloud Services](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) för mer information.

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Undvik att deklarera DbContext-objekt som statisk
### <a name="id"></a>ID
AP6000

### <a name="description"></a>Beskrivning
Om du vill spara minne Undvik att deklarera DBContext-objekt som statisk.

Du kan dela idéer och feedback på [Azure kod analys feedback](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Orsak
DBContext-objekt innehåller resultatet av frågan från varje anrop. Statiska DBContext-objekt är inte bort förrän programdomänen tas bort. Därför kan ett statiskt DBContext-objekt förbruka stora mängder minne.

### <a name="solution"></a>Lösning
Deklarera DBContext som en lokal variabel eller icke-statisk instansfält, använda den för en aktivitet och låt den avyttras efter användning.

I följande exempel MVC kontrollantklassen visas hur du använder DBContext-objektet.

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
Läs mer om hur du optimerar och felsöka Azure apps i [felsöka en webbapp i Azure App Service med Visual Studio](app-service/web-sites-dotnet-troubleshoot-visual-studio.md).
