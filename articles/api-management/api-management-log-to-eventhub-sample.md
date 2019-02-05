---
title: 'Övervaka API: er med Azure API Management, Event Hubs och Runscope | Microsoft Docs'
description: Exempelprogrammet som visar loggen till eventhub-principen genom att ansluta Azure API Management, Azure Event Hubs och Runscope för HTTP-loggning och övervakning
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: 3a868eb98121ff2e2a30657e301afba7b8618361
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728488"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Övervaka dina API: er med Azure API Management, Event Hubs och Runscope
Den [API Management-tjänsten](api-management-key-concepts.md) innehåller många funktioner för att förbättra bearbetningen av HTTP-begäranden som skickas till din HTTP-API. Förekomsten av begäranden och svar är dock tillfälligt. Begäran gjordes och den förs vidare via API Management-tjänsten till ditt serverdels-API. Ditt API bearbetar begäran och svaret som flödar tillbaka via API-konsumenter. API Management-tjänsten ser till att vissa viktig statistik om API: er för visning i instrumentpanelen för Azure portal, men även efteråt som informationen är borta.

Med principen logga till eventhub i API Management-tjänsten, du kan skicka data från begäran och svar på en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Det finns en mängd orsaker till varför du kanske vill generera händelser från HTTP-meddelanden som skickas till dina API: er. Några exempel är verifieringskedja för uppdateringar, användningsanalys, undantag varningar och tredjeparts-integreringar.

Den här artikeln visar hur du avbildar hela HTTP-begäranden och svar meddelandet, skickar den till en Händelsehubb och sedan vidarebefordra meddelandet till en tredje parts-tjänst som ger HTTP-loggning och övervakning av tjänster.

## <a name="why-send-from-api-management-service"></a>Varför skicka från API Management-tjänsten?
Det går att skriva HTTP mellanprogram som ansluter till HTTP-API-ramverk för att avbilda HTTP-begäranden och svar och skicka dem till loggning och övervakningssystem. Nackdelen med att den här metoden är HTTP-mellanprogram behöver integreras i serverdels-API och måste matcha plattformen för API: et. Om det finns flera API: er, distribuera var och en oss mellanprogrammet. Det finns ofta orsaker till varför serverdel API: er inte kan uppdateras.

Med Azure API Management-tjänsten för att integrera med infrastruktur för loggning ger en central och plattformsoberoende lösning. Det är också skalbara, delvis på grund av att den [geo-replikering](api-management-howto-deploy-multi-region.md) funktionerna i Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Varför skickar till en Azure Event Hub?
Det är ett rimligt att fråga, Varför skapa en princip som är specifika för Azure Event Hubs? Det finns många olika ställen där kanske du vill logga Mina förfrågningar. Varför inte bara skicka begäranden direkt till slutmålet?  Det är ett alternativ. Vid loggningsbegäranden från en API management-tjänsten måste krävs det att tänka på hur meddelandeloggning påverka prestandan för API: et. Gradvis ökad belastning kan hanteras genom att öka tillgängliga instanser av systemkomponenter eller genom att utnyttja geo-replikering. Kort trafiktoppar kan dock orsaka begäranden till fördröjas om förfrågningar till loggning infrastruktur startar långsammare under belastning.

Azure Event Hubs har utformats att ingående stora mängder data, med kapacitet för att ta itu med ett mycket större antal händelser än antalet HTTP-begäranden processen för de flesta API: er. Event Hub fungerar som en typ av avancerade buffert mellan API management-tjänsten och den infrastruktur som lagrar och bearbetar meddelanden. Detta säkerställer att din API-prestanda blir inte lidande på grund av infrastrukturen som loggning.

När data har skickats till en Händelsehubb, bevaras och väntar på att Event Hub-användare kan bearbeta den. Event Hub bryr sig inte hur den bearbetas, det värnar bara om att se till att meddelandet har levereras.

Händelsehubbar har möjlighet att strömma händelser till flera konsumentgrupper. På så sätt kan händelser som bearbetas av olika system. Detta gör att stödja många integrationsscenarier utan att lägga tillägg fördröjningar i bearbetningen av API-anrop i API Management-tjänsten som behöver bara en händelse skapas.

## <a name="a-policy-to-send-applicationhttp-messages"></a>En princip för att skicka programmet/HTTP-meddelanden
En Händelsehubb tar emot händelsedata som en sträng. Innehållet i strängen är upp till dig. Om du vill packa upp en HTTP-begäran och skicka den till Event Hubs, som vi behöva formatera strängen med informationen om begäran eller ett svar. I situationer som detta, om det finns ett befintligt format vi kan återanvända och vi kanske inte att skriva egen kod-parsning. Inledningsvis jag anses vara med hjälp av den [HAR](http://www.softwareishard.com/blog/har-12-spec/) för att skicka HTTP-begäranden och svar. Men är det här formatet optimerad för att lagra en sekvens med HTTP-begäranden i ett format som JSON-baserade. Det finns ett antal obligatoriska element som har lagts till onödig komplexitet för scenariot med att skicka HTTP-meddelande i rörelse.

En alternativ var att använda den `application/http` medietyp enligt beskrivningen i HTTP-specifikationen [RFC 7230](https://tools.ietf.org/html/rfc7230). Den här medietypen använder exakt samma format som används för att faktiskt skicka HTTP-meddelanden över nätverket, men hela meddelandet kan vara placera i brödtexten i en annan HTTP-begäran. I vårt fall ska vi bara använda brödtext som vårt meddelande för att skicka till Event Hubs. Det är bekvämt, en tolk som finns i [Microsoft ASP.NET Web API 2.2-klient](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) bibliotek som kan parsa detta format och konvertera den till ursprungliga `HttpRequestMessage` och `HttpResponseMessage` objekt.

Om du vill kunna skapa det här meddelandet vi behöver att dra nytta av C#-baserat [principuttryck](https://msdn.microsoft.com/library/azure/dn910913.aspx) i Azure API Management. Här är principen som skickar ett HTTP-begäran-meddelande till Azure Event Hubs.

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
Det några specifika saker värt att nämna om det här Principuttrycket. Loggen till eventhub-principen har ett attribut som kallas logger-id som refererar till namnet på loggaren som har skapats i API Management-tjänsten. Information om hur du ställer in en Event Hub-loggaren i API Management-tjänsten finns i dokumentet [logga händelser till Azure Event Hubs i Azure API Management](api-management-howto-log-event-hubs.md). Det andra attributet är en valfri parameter som instruerar Event Hubs som partitionera för att lagra meddelandet i. Händelsehubbar använder partitioner för att aktivera skalbarhet och kräver minst två. Sorterad leverans av meddelanden garanteras endast inom en partition. Om vi inte instruera Event Hub i vilken partition att placera meddelandet, använder en algoritm för resursallokering för att fördela belastningen. Som kan dock orsaka några av våra meddelanden som ska bearbetas i ordning.

### <a name="partitions"></a>Partitioner
För att säkerställa att våra meddelanden levereras till kunder i ordning och dra nytta av funktionen belastningen för distribution av partitioner, jag har valt att skicka HTTP-begäran-meddelanden till en partition och HTTP-svar-meddelanden till en andra partition. Detta säkerställer en jämn belastningsfördelning och vi kan garantera att alla förfrågningar som ska användas i ordning och alla svar används i ordning. Det är möjligt för ett svar som ska konsumeras innan motsvarande begäran, men eftersom det inte är ett problem som vi har en annan mekanism för att korrelera begäranden till svar och vi vet att begäranden alltid ligga före svar.

### <a name="http-payloads"></a>HTTP-nyttolaster
Efter att skapa den `requestLine`, vi kontrollerar om du vill se om begärandetexten ska trunkeras. Begärandetexten trunkeras endast 1024 tecken. Detta kan öka, men enskilda Event Hub-meddelanden är begränsade till 256 KB, så är det troligt att vissa HTTP-meddelande organ kommer inte passar in i ett enda meddelande. När du gör loggning och analytics kan en betydande mängd information härledas från bara HTTP-begäran rad och rubriker. Dessutom många API: er begär bara returnera små organ och förlust av information värde genom att trunkera stora organ är alltså ganska minimal jämförelse med minskningen överföring, bearbetnings- och kostnader för lagring för att hålla alla innehållet i meddelandetexten. En slutlig anteckning om bearbetning av brödtexten är att vi behöver skicka `true` till den `As<string>()` metoden eftersom vi läser brödtext-innehållet, men det var även ville serverdels-API för att kunna läsa innehållet. Genom att skicka sant för den här metoden orsaka vi brödtexten buffras så att de kan läsas en andra gång. Detta är viktigt att känna till om du har ett API som stöder överföring av stora filer, eller använder lång avsökning. I dessa fall skulle det vara bäst att undvika att läsa brödtexten alls.

### <a name="http-headers"></a>HTTP-huvuden
HTTP-huvuden kan överföras via till meddelandeformat i ett format för enkel nyckel/värde-par. Vi har valt att ta bort vissa känsliga säkerhetsområden, att undvika att onödigt läcker autentiseringsinformation. Det är osannolikt att API-nycklar och andra autentiseringsuppgifter ska användas för analysändamål. Om vi vill göra analyser på användaren och den särskilda produkt som de använder och vi kan hämta det från den `context` objekt och lägga till som i meddelandet.

### <a name="message-metadata"></a>Message Metadata
När du skapar hela meddelandet att skicka till event hub, den första raden är inte faktiskt en del av den `application/http` meddelande. Den första raden är ytterligare metadata som består av om meddelandet är en begäran eller svarsmeddelandet och ett meddelande-ID som används för att korrelera begäranden till svar. Meddelande-ID skapas med hjälp av en annan princip som ser ut så här:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Vi kan ha skapats meddelandet med begäran, lagras i en variabel som tills svaret var returneras och sedan skickas begäran och svaret som ett enda meddelande. Men genom att skicka begäran och svaret oberoende av varandra och använder ett meddelande-id för att kombinera två, få vi lite mer flexibilitet i meddelandelagringsstorlek kan dra nytta av flera partitioner samtidigt underhålla Meddelandeordningen och begäran kommer att visas i vår loggning instrumentpanel snabbare. Det kan också finnas vissa scenarier där ett giltigt svar skickas aldrig till event hub, möjligen på grund av ett allvarligt fel i API Management-tjänsten, men vi fortfarande har en post för begäran.

Principen för att skicka HTTP-svarsmeddelandet liknar begäran och så fullständig principkonfigurationen ser ut så här:

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

Den `set-variable` principen skapas ett värde som kan nås av både den `log-to-eventhub` principen i den `<inbound>` avsnittet och `<outbound>` avsnittet.

## <a name="receiving-events-from-event-hubs"></a>Tar emot händelser från Event Hubs
Händelser från Azure Event Hub tas emot med hjälp av den [AMQP-protokollet](https://www.amqp.org/). Microsoft Service Bus-teamet har gjort klienten bibliotek som är tillgängliga för att underlätta de konsumerande händelserna. Det finns två olika metoder som stöds, en håller på att en *direkt konsument* och den andra använder den `EventProcessorHost` klass. Exempel på dessa två metoder kan hittas i den [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md). Den korta versionen av skillnaderna är `Direct Consumer` ger dig fullständig kontroll och `EventProcessorHost` gör en del av arbetet mekanik för du men gör du vissa antaganden om hur du bearbetar dessa händelser.

### <a name="eventprocessorhost"></a>EventProcessorHost
I det här exemplet använder vi den `EventProcessorHost` för enkelhetens skull, men det kanske inte det bästa valet för det här scenariot. `EventProcessorHost` gör arbetet med att se till att du inte behöver bekymra dig om threading problem inom en viss händelse processor-klass. I vårt scenario, är vi dock helt enkelt konvertera meddelandet till ett annat format och skicka den tillsammans till en annan tjänst med hjälp av en async-metod. Det finns inget behov för att uppdatera delade tillstånd och därför ingen risk för threading problem. De flesta scenarier med `EventProcessorHost` förmodligen är det bästa valet och det är alternativet enklare.

### <a name="ieventprocessor"></a>IEventProcessor
Den centrala begreppen när du använder `EventProcessorHost` är att skapa en implementering av den `IEventProcessor` gränssnitt, vilket innehåller metoden `ProcessEventAsync`. Grunden i metoden visas här:

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

En lista över EventData objekt har skickats till metoden och vi kan iterera över listan. Byte för varje metod parsas i ett HttpMessage-objekt och objektet skickas till en instans av IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
Den `HttpMessage` instans innehåller tre typer av data:

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

Den `HttpMessage` instans innehåller en `MessageId` GUID som gör att vi kan ansluta HTTP-begäran till motsvarande HTTP-svaret och ett booleskt värde som anger om objektet innehåller en instans av en HttpRequestMessage och HttpResponseMessage. Med hjälp av inbyggda HTTP klasserna från `System.Net.Http`, jag kunde dra nytta av den `application/http` parsning av kod som ingår i `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Den `HttpMessage` instans sedan vidarebefordras till implementeringen av `IHttpMessageProcessor`, vilket är ett gränssnitt som jag skapade för att frikoppla som tar emot och tolkning av händelsen från Azure Event Hub och den faktiska bearbetningen av den.

## <a name="forwarding-the-http-message"></a>Vidarebefordran av HTTP-meddelande
I det här exemplet bestämde jag det skulle vara intressant att skicka HTTP-begäran över till [Runscope](https://www.runscope.com). Runscope är en molnbaserad tjänst som specialiserar sig på HTTP-felsökning, loggning och övervakning. De har en kostnadsfri nivå, så att det är enkelt att försök och det gör att vi kan se HTTP-förfrågningar i realtid som passerar genom vår API Management-tjänsten.

Den `IHttpMessageProcessor` implementering ser ut så här,

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

Jag kunde dra nytta av en [befintliga klientbibliotek för Runscope](https://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) som gör det enkelt att skicka den `HttpRequestMessage` och `HttpResponseMessage` instanser upp till sina tjänster. För att komma åt Runscope API: et, behöver du ett konto och en API-nyckel. Anvisningar för att hämta en API-nyckel finns i den [skapar program till åtkomst Runscope API](https://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) skärmbild.

## <a name="complete-sample"></a>Fullständigt exempel
Den [källkod](https://github.com/darrelmiller/ApimEventProcessor) och tester för exemplet på GitHub. Du behöver en [API Management-tjänsten](get-started-create-service-instance.md), [anslutna Händelsehubben](api-management-howto-log-event-hubs.md), och en [Lagringskonto](../storage/common/storage-create-storage-account.md) att köra exemplet själv.   

I exemplet är bara ett enkelt konsolprogram som lyssnar efter händelser som kommer från Event Hub, konverterar dem till en `HttpRequestMessage` och `HttpResponseMessage` objekt och vidarebefordrar dem till Runscope API: et.

I följande animerade bild ser du en begäran som görs till ett API i Developer-portalen, visar meddelandet tas emot, bearbetas och vidarebefordras konsolprogrammet och sedan begäran och svar som visas i Runscope trafik inspector.

![Demonstration av begäran som vidarebefordras till Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Sammanfattning
Azure API Management-tjänsten tillhandahåller perfekta platsen för att samla in all HTTP-trafik till och från dina API: er. Azure Event Hubs är en mycket skalbar och billig lösning för att skapa den trafiken och vehicle det sekundära bearbetning system för loggning, övervakning och andra avancerade analyser. Ansluter till tredje parts trafik övervakningssystem som Runscope är lika enkelt som att några dussin rader med kod.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Event Hubs
  * [Kom igång med Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Mer information om API Management och Event Hubs-integrering
  * [Logga händelser till Azure Event Hubs i Azure API Management](api-management-howto-log-event-hubs.md)
  * [Loggaren enhetsreferens](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Referens för loggen till eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
