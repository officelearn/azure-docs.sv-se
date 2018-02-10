---
title: "Övervaka API: er med Azure API Management och Händelsehubbar Runscope | Microsoft Docs"
description: "Exempelprogrammet visar loggen till eventhub-principen som ansluter Azure API Management, Azure Event Hubs och Runscope för HTTP-loggning och övervakning"
services: api-management
documentationcenter: 
author: darrelmiller
manager: erikre
editor: 
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: 8ef8d64ba90960281faffc350821d7934e35749a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Övervaka dina API: er med Azure API Management och Händelsehubbar Runscope
Den [API Management-tjänsten](api-management-key-concepts.md) innehåller många funktioner som förbättrar bearbetning av HTTP-begäranden skickas till HTTP-API. Men är i begäran och svar tillfälligt. Begäran gjordes och den förs vidare via API Management-tjänsten till din serverdel API. Din API behandlar begäran och ett svar som flödar tillbaka via för API-konsumenter. API Management-tjänsten håller viktig statistik om API: er för att visas i Azure portal instrumentpanelen, men senare att information försvinner.

Med hjälp av loggen till eventhub-principen i API Management-tjänsten kan du skicka information från begäran och svar på en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Det finns många olika skäl varför du kanske vill generera händelser från HTTP-meddelanden som skickas till din API: er. Några exempel verifieringskedja för uppdateringar, användningsanalys, undantag varningar och fristående integreringar.   

Den här artikeln visar hur du avbilda hela HTTP-begäran och svar meddelandet och skicka den till en Händelsehubb vidarebefordrar meddelandet till en tredje parts-tjänst som tillhandahåller HTTP-loggning och övervakning av tjänster.

## <a name="why-send-from-api-management-service"></a>Varför skicka från API Management-tjänsten?
Det går att skriva HTTP-mellanprogram som ansluter till http-API-ramverk för att samla in HTTP-begäranden och -svar och feed dem till loggning och övervakning av system. Nackdelen med att den här metoden är HTTP-mellanprogram behöver integreras i serverdelen API och måste matcha plattformen för API: et. Om det finns flera API: er, distribuera var och en mellanprogram. Det finns ofta skäl varför backend API: er inte kan uppdateras.

Med hjälp av Azure API Management-tjänsten integreras med loggning infrastrukturen innehåller en centraliserad och plattformsoberoende lösning. Det är också skalbara delvis på grund av att den [geo-replikering](api-management-howto-deploy-multi-region.md) funktionerna i Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Varför skicka till en Azure-Händelsehubb?
Det är ett rimligt att fråga, varför ska man skapa en princip som är specifika för Azure Event Hubs? Det finns många olika ställen där kanske du vill logga Mina förfrågningar. Varför inte bara skicka begäranden direkt till slutmålet?  Det är ett alternativ. Vid av loggningsbegäranden från en API management-tjänsten, är det dock nödvändigt att överväga hur meddelandeloggning påverka prestandan för API: et. Stegvis ökning belastning kan hanteras genom att öka tillgängliga instanser av systemkomponenter eller genom att utnyttja geo-replikering. Kort toppar i trafik kan dock göra förfrågningar till försenas om begäranden till loggning infrastruktur starta långsammare under belastning.

Händelsehubbar i Azure är utformat för att ingång stora mängder data, med kapacitet för behandlar en mycket högre antal händelser än antalet HTTP-begäranden processen för de flesta API: er. Händelsehubben fungerar som en typ av avancerade buffert mellan din API management-tjänsten och den infrastruktur som lagrar och bearbetar meddelanden. Detta säkerställer att din API-prestanda blir inte lidande på grund av infrastrukturen som loggning.  

När data har skickats till en Händelsehubb, sparas och väntar på att Event Hub konsumenterna kan bearbeta den. Händelsehubben bryr sig inte hur den bearbetas, det är bara mån om huruvida meddelandet levereras har.     

Händelsehubbar har möjlighet att dataströmmen händelser till flera konsumentgrupper. På så sätt kan händelser som kan bearbetas i olika system. Detta gör att stödja många integrationsscenarier utan att lägga tillägg fördröjningar i bearbetning av API-begäran inom API Management-tjänsten som behöver bara en händelse skapas.

## <a name="a-policy-to-send-applicationhttp-messages"></a>En princip för att skicka program/HTTP-meddelanden
En Händelsehubb accepterar händelsedata som en sträng. Innehållet i strängen är du. Om du vill packa upp en HTTP-begäran och skicka den till Händelsehubbar, behöver vi formatsträng med informationen om begäran eller ett svar. I situationer som, om det finns ett befintligt format vi kan återanvända sedan vi behöver inte skriva egna parsning kod. Ursprungligen jag betraktas som använder den [HAR](http://www.softwareishard.com/blog/har-12-spec/) för att skicka HTTP-begäranden och -svar. Men är det här formatet optimerad för att lagra en sekvens med HTTP-förfrågningar i en JSON-baserade format. Det finns ett antal obligatoriska element som lagts till onödiga komplexitet för scenariot att skicka HTTP-meddelande via kabel.  

Ett alternativ är att använda den `application/http` medietyp enligt beskrivningen i HTTP-specifikationen [RFC 7230](http://tools.ietf.org/html/rfc7230). Den här medietypen använder exakt samma format som används för att skicka HTTP-meddelanden via kabel, men hela meddelandet kan placeras i brödtexten i ett annat HTTP-begäran. I vårt fall ska vi bara använda brödtexten som våra meddelande du skickar till Händelsehubbar. Det är enkelt, en tolk som finns i [Microsoft ASP.NET Web API 2.2 Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) bibliotek som kan tolka detta format och konvertera det till överordnade länkens interna `HttpRequestMessage` och `HttpResponseMessage` objekt.

För att kunna skapa det här meddelandet, behöver vi dra nytta av C#-baserade [principuttrycken](https://msdn.microsoft.com/library/azure/dn910913.aspx) i Azure API Management. Här är principen som skickar ett meddelande med HTTP-begäran till Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Deklarationen för principen
Det några särskilda saker värt att nämna om den här principuttryck. Logg för eventhub-principen har ett attribut som kallas loggaren-id som refererar till namnet på loggaren som har skapats i API Management-tjänsten. Information om hur du ställer in en Händelsehubb loggaren i API Management-tjänsten finns i dokumentet [logga händelser i Händelsehubbar i Azure API Management](api-management-howto-log-event-hubs.md). Andra attribut är en valfri parameter som instruerar Händelsehubbar som partitionerar för att lagra meddelandet i. Händelsehubbar använder partitioner för att aktivera skalbarhet och kräver minst två. Begärd leverans av meddelanden garanterat enbart inom en partition. Om vi inte instruera Event Hub i vilken partition att placera meddelandet, använder en algoritm för resursallokering för att fördela belastningen. Dock kan orsaka några av våra meddelanden som ska bearbetas i fel ordning.  

### <a name="partitions"></a>Partitioner
För att säkerställa våra meddelanden levereras till konsumenter i ordning och dra nytta av belastningen distribution möjligheterna för partitioner, jag har valt att skicka meddelanden för HTTP-begäran till en partition och HTTP-svar-meddelanden till en andra partition. Detta garanterar en även belastningsdistribution och vi kan garantera att alla begäranden som kommer att användas i ordning och alla svar används i ordning. Det är möjligt att ett svar som ska konsumeras innan motsvarande begäran, men som inte är ett problem som vi har en annan mekanism för korrelation mellan begäranden till svar och vi vet att förfrågningar alltid kommer före svar.

### <a name="http-payloads"></a>HTTP-nyttolaster
Efter byggnad den `requestLine`, vi kontrollerar du om begärandetexten trunkeras. Begärandetexten trunkeras till endast 1024. Detta kan ökas, men enskilda Event Hub-meddelanden är begränsade till 256 KB, så är det troligt att vissa HTTP-meddelande organ kommer inte att få plats i ett enda meddelande. När du gör loggning och analyser kan mycket information härledas från bara HTTP-begäran rad och rubriker. Dessutom många API: er begär bara returnera små organ och så förlust av information värde med trunkera stora organ är ganska minimal jämförelse med minskning i överföringen, bearbetning och lagringskostnader för att behålla innehållet i meddelandetexten. En slutlig anteckning om bearbetning brödtext är att vi behöver skicka `true` till som<string>()-metod eftersom vi läser body-innehållet, men det var även vill serverdelen API för att kunna läsa innehållet. Genom att passera true till den här metoden gör vi brödtext buffras så att den kan läsa en andra gång. Det är viktigt att känna till om du har en API som stöder överföring av stora filer eller använder långt avsökning. I dessa fall kan skulle det vara bäst att undvika läsning av innehållet.   

### <a name="http-headers"></a>HTTP-huvuden
HTTP-huvuden kan överföras via till meddelandeformat i formatet enkla nyckel/värde-par. Vi har valt att ta bort vissa säkerhet känsliga fält, till att undvika att i onödan läcka autentiseringsuppgifter. Det är inte troligt att API-nycklar och andra autentiseringsuppgifter används för analytics ändamål. Om vi vill göra analyser på användaren och viss produkt som de använder och vi kan hämta från den `context` objektet och lägga till som i meddelandet.     

### <a name="message-metadata"></a>Meddelandets Metadata
När du skapar hela meddelandet ska skickas till händelsehubben den första raden inte är faktiskt en del av den `application/http` meddelande. Den första raden är ytterligare metadata som består av om meddelandet är en begäran eller svarsmeddelande och ett meddelande-ID som används för att korrelera begär att få svar. Meddelande-ID skapas med hjälp av en annan princip som ser ut så här:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Vi har skapat begärandemeddelandet kan lagras i en variabel som tills svaret var returneras och sedan skickas förfrågan och svar som ett enda meddelande. Men genom att skicka förfrågan och svar oberoende av varandra och med ett meddelande-id för att korrelera två, få vi lite mer flexibilitet i meddelandestorlek möjlighet att dra nytta av flera partitioner samtidigt upprätthålla meddelandet ordning och begäran kommer att visas i vår loggning instrumentpanelen snabbare. Det kan också finnas vissa scenarier där ett giltigt svar skickas aldrig till händelsehubben, möjligtvis på grund av ett allvarligt begäran-fel i API Management-tjänsten, men vi fortfarande har en post på begäran.

Princip för att skicka HTTP-svarsmeddelandet liknar begäran och så fullständig principkonfigurationen ser ut så här:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

Den `set-variable` princip skapar ett värde som kan nås av både den `log-to-eventhub` princip i den `<inbound>` avsnitt och `<outbound>` avsnitt.  

## <a name="receiving-events-from-event-hubs"></a>Ta emot händelser från Event Hubs
Händelser från Azure Event Hub tas emot med den [AMQP protokollet](http://www.amqp.org/). Microsoft Service Bus-teamet har gjort klienten bibliotek som är tillgängliga för att underlätta användning händelser. Det finns två olika metoder som stöds, ett används en *direkt konsumenten* och den andra använder den `EventProcessorHost` klass. Exempel på dessa två metoder kan hittas i den [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md). Den korta versionen av skillnaderna är `Direct Consumer` ger dig fullständig kontroll och `EventProcessorHost` har del av mekanik för du men görs vissa antaganden om hur du bearbetar dessa händelser.  

### <a name="eventprocessorhost"></a>EventProcessorHost
I det här exemplet använder vi den `EventProcessorHost` för enkelhetens skull, men den kan inte det bästa valet för det här scenariot. `EventProcessorHost`gör det tunga arbetet ser till att du inte behöver bry dig om trådmodell problem inom en viss händelse processor-klass. I vårt scenario, är vi dock bara konvertera meddelandet till ett annat format och skicka den vidare till en annan tjänst som använder en async-metod. Det behövs ingen för att uppdatera delade tillstånd och därför ingen risk för trådmodell problem. I de flesta scenarier `EventProcessorHost` förmodligen är det bästa valet och det är visserligen alternativet enklare.     

### <a name="ieventprocessor"></a>IEventProcessor
När du använder centrala konceptet `EventProcessorHost` är att skapa en implementering av den `IEventProcessor` -gränssnittet, som innehåller metoden `ProcessEventAsync`. Metoden huvudsakligen visas här:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

   foreach (EventData eventData in messages)
   {
       _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

       try
       {
           var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
           await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
       }
       catch (Exception ex)
       {
           _Logger.LogError(ex.Message);
       }
   }
    ... checkpointing code snipped ...
}
```

En lista över EventData objekt har skickats till metoden och vi iterera över listan. Byte för varje metod är parsas till ett HttpMessage objekt och det objektet har överförts till en instans av IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
Den `HttpMessage` instans innehåller tre delar av data:

```csharp
public class HttpMessage
{
   public Guid MessageId { get; set; }
   public bool IsRequest { get; set; }
   public HttpRequestMessage HttpRequestMessage { get; set; }
   public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

Den `HttpMessage` instans innehåller en `MessageId` GUID som gör att vi kan ansluta HTTP-begäran till motsvarande HTTP-svaret och ett booleskt värde som anger om objektet innehåller en instans av en HttpRequestMessage och HttpResponseMessage. Med hjälp av inbyggda HTTP klasserna från `System.Net.Http`, kunde dra nytta av den `application/http` parsning av kod som ingår i `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Den `HttpMessage` instans sedan vidarebefordras till implementeringen av `IHttpMessageProcessor`, vilket är ett gränssnitt som jag skapar frikoppla ta emot och tolkning av händelsen från Azure Event Hub och den faktiska bearbetningen av den.

## <a name="forwarding-the-http-message"></a>Vidarebefordran av HTTP-meddelande
För det här exemplet jag valt det skulle vara av intresse för push-HTTP-begäran över till [Runscope](http://www.runscope.com). Runscope är en molnbaserad tjänst som är specialiserat på HTTP-felsökning, loggning och övervakning. De har en kostnadsfri nivå så att det är enkelt att prova och det gör att vi kan se HTTP-förfrågningar i realtid som passerar genom våra API Management-tjänsten.

Den `IHttpMessageProcessor` implementeringen ser ut så här,

```csharp
public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
{
   private HttpClient _HttpClient;
   private ILogger _Logger;
   private string _BucketKey;
   public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
   {
       _HttpClient = httpClient;
       var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
       _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
       _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
       _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
       _Logger = logger;
   }

   public async Task ProcessHttpMessage(HttpMessage message)
   {
       var runscopeMessage = new RunscopeMessage()
       {
           UniqueIdentifier = message.MessageId
       };

       if (message.IsRequest)
       {
           _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
           runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
       }
       else
       {
           _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
           runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
       }

       var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
       messagesLink.BucketKey = _BucketKey;
       messagesLink.RunscopeMessage = runscopeMessage;
       var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
       _Logger.LogDebug("Request sent to Runscope");
   }
}
```

Jag kunde dra nytta av en [befintliga klientbibliotek för Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) som gör det enkelt att push `HttpRequestMessage` och `HttpResponseMessage` instanser in till tjänsten. För att komma åt Runscope API, behöver du ett konto och en API-nyckel. Anvisningar för att hämta en API-nyckel finns i den [skapar åtkomst Runscope API-program](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) skärmutsändning.

## <a name="complete-sample"></a>Fullständigt exempel
Den [källkod](https://github.com/darrelmiller/ApimEventProcessor) och tester för finns på GitHub. Du behöver ett [API Management-tjänsten](get-started-create-service-instance.md), [anslutna Händelsehubben](api-management-howto-log-event-hubs.md), och en [Lagringskonto](../storage/common/storage-create-storage-account.md) att köra exemplet för dig själv.   

Urvalet är bara ett enkelt konsolprogram som lyssnar efter händelser som kommer från Event Hub konverterar dem till en `HttpRequestMessage` och `HttpResponseMessage` objekt och vidarebefordrar dem till Runscope-API.

Du kan se en begäran till en API: N i Developer-portalen visar meddelandet tas emot, bearbetas och vidarebefordras konsolprogrammet och sedan begäran och svar som visas i Runscope trafik inspector följande animerade bild.

![Demonstration av begäran som vidarebefordras till Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Sammanfattning
Azure API Management-tjänsten tillhandahåller användbar för att avbilda den HTTP-trafik till och från dina API: er. Händelsehubbar i Azure är en mycket skalbar, prisvärda lösning för avbildning av trafiken och går in sekundär bearbetningssystem för loggning, övervakning och andra avancerade analyser. Anslutning till tredjeparts-trafik övervakningssystem som Runscope är så enkelt som några dussin rader med kod.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om Azure Event Hubs
  * [Kom igång med Händelsehubbar i Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Mer information om API-hantering och Händelsehubbar-integrering
  * [Logga händelser i Händelsehubbar i Azure API Management](api-management-howto-log-event-hubs.md)
  * [Loggaren enhetsreferens](https://msdn.microsoft.com/library/azure/mt592020.aspx)
  * [principreferens för loggen till eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
