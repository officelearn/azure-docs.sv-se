---
title: Övervaka API:er med Azure API Management, Event Hubs och Moesif
titleSuffix: Azure API Management
description: Exempelprogram som demonstrerar principen log-to-eventhub genom att ansluta Azure API Management, Azure Event Hubs och Moesif för HTTP-loggning och övervakning
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
ms.openlocfilehash: 4a0717bf7a284668af4808acae3050cc7f42f836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442529"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Övervaka dina API:er med Azure API Management, Event Hubs och Moesif
[API Management-tjänsten](api-management-key-concepts.md) innehåller många funktioner för att förbättra bearbetningen av HTTP-begäranden som skickas till HTTP-API:et. Förekomsten av förfrågningar och svar är dock övergående. Begäran görs och den flödar via API Management-tjänsten till ditt serverd-API. Ditt API bearbetar begäran och ett svar flödar tillbaka till API-konsumenten. API Management-tjänsten behåller en del viktig statistik om API:erna för visning i Azure-portalens instrumentpanel, men utöver det är informationen borta.

Genom att använda log-to-eventhub-principen i API Management-tjänsten kan du skicka information från begäran och svaret till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Det finns en mängd olika anledningar till varför du kanske vill generera händelser från HTTP-meddelanden som skickas till dina API:er. Några exempel är granskningsspår av uppdateringar, användningsanalys, undantagsavisering och tredjepartsintegrationer.

Den här artikeln visar hur du samlar in hela HTTP-begäran och svarsmeddelandet, skickar det till en eventhub och vidarebefordrar sedan meddelandet till en tjänst från tredje part som tillhandahåller HTTP-loggnings- och övervakningstjänster.

## <a name="why-send-from-api-management-service"></a>Varför skicka från API Management Service?
Det är möjligt att skriva HTTP mellanprogram som kan ansluta till HTTP API-ramverk för att samla http-begäranden och svar och mata in dem i loggnings- och övervakningssystem. Nackdelen med den här metoden är HTTP middleware måste integreras i serverdel API och måste matcha plattformen för API. Om det finns flera API:er måste var och en distribuera mellanprogram. Ofta finns det orsaker till att backend API:er inte kan uppdateras.

Att använda Azure API Management-tjänsten för att integrera med loggningsinfrastruktur ger en centraliserad och plattformsoberoende lösning. Det är också skalbart, delvis på grund av [geo-replikeringsfunktionerna](api-management-howto-deploy-multi-region.md) i Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Varför skicka till en Azure Event Hub?
Det är rimligt att fråga sig varför skapa en princip som är specifik för Azure Event Hubs? Det finns många olika platser där jag kanske vill logga mina önskemål. Varför inte bara skicka förfrågningar direkt till slutdestinationen?  Det är ett alternativ. När du gör loggningsbegäranden från en API-hanteringstjänst är det dock nödvändigt att överväga hur loggningsmeddelanden påverkar API:ets prestanda. Gradvisa ökningar i belastningen kan hanteras genom att öka tillgängliga instanser av systemkomponenter eller genom att dra nytta av geo-replikering. Korta toppar i trafiken kan dock orsaka att begäranden försenas om begäranden om loggningsinfrastruktur börjar sakta ned under belastningen.

Azure Event Hubs är utformad för att gå in i enorma mängder data, med kapacitet för att hantera ett mycket högre antal händelser än antalet HTTP-begäranden som de flesta API:er bearbetar. Event Hub fungerar som en slags sofistikerad buffert mellan din API-hanteringstjänst och den infrastruktur som lagrar och bearbetar meddelandena. Detta säkerställer att din API-prestanda inte kommer att drabbas på grund av loggningsinfrastrukturen.

När data har skickats till en eventhubb sparas den och väntar på att Event Hub-konsumenter ska bearbeta den. Event Hub bryr sig inte om hur den bearbetas, den bryr sig bara om att se till att meddelandet levereras.

Event Hubs har möjlighet att strömma händelser till flera konsumentgrupper. Detta gör att händelser kan bearbetas av olika system. Detta gör det möjligt att stödja många integrationsscenarier utan att lägga till tilläggsfördröjningar på bearbetningen av API-begäran inom API Management-tjänsten eftersom endast en händelse behöver genereras.

## <a name="a-policy-to-send-applicationhttp-messages"></a>En princip för att skicka program/http-meddelanden
En händelsehubb accepterar händelsedata som en enkel sträng. Innehållet i strängen är upp till dig. För att kunna paketera en HTTP-begäran och skicka den till Event Hubs måste vi formatera strängen med begäran eller svarsinformation. I situationer som denna, om det finns ett befintligt format som vi kan återanvända, då kanske vi inte behöver skriva vår egen tolkningskod. Inledningsvis övervägde jag att använda [HAR](http://www.softwareishard.com/blog/har-12-spec/) för att skicka HTTP-förfrågningar och svar. Det här formatet är dock optimerat för att lagra en sekvens av HTTP-begäranden i ett JSON-baserat format. Den innehöll ett antal obligatoriska element som lagt till onödig komplexitet för scenariot att skicka HTTP-meddelandet över kabeln.

Ett alternativt alternativ var `application/http` att använda medietypen enligt beskrivningen i HTTP-specifikationen [RFC 7230](https://tools.ietf.org/html/rfc7230). Den här medietypen använder exakt samma format som används för att faktiskt skicka HTTP-meddelanden via kabeln, men hela meddelandet kan placeras i brödtexten för en annan HTTP-begäran. I vårt fall kommer vi bara att använda kroppen som vårt budskap för att skicka till Event Hubs. Det finns en tolk i [Microsoft ASP.NET Web API 2.2-klientbibliotek](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) som kan tolka det här formatet `HttpRequestMessage` `HttpResponseMessage` och konvertera det till det infödda och objekten.

För att kunna skapa det här meddelandet måste vi dra nytta av C#-baserade [principuttryck](/azure/api-management/api-management-policy-expressions) i Azure API Management. Här är principen, som skickar ett HTTP-begäran meddelande till Azure Event Hubs.

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

### <a name="policy-declaration"></a>Policydeklaration
Det finns några särskilda saker som är värda att nämna om detta politiska uttryck. Principen log-to-eventhub har ett attribut som kallas logger-id, som refererar till namnet på loggern som har skapats inom API Management-tjänsten. Information om hur du konfigurerar en Event Hub-logger i API Management-tjänsten finns i dokumentet [Så här loggar du händelser till Azure Event Hubs i Azure API Management](api-management-howto-log-event-hubs.md). Det andra attributet är en valfri parameter som instruerar Event Hubs vilken partition som meddelandet ska lagras i. Event Hubs använder partitioner för att aktivera skalbarhet och kräver minst två. Den beställda leveransen av meddelanden garanteras endast inom en partition. Om vi inte instruerar Event Hub i vilken partition att placera meddelandet, använder den en round-robin algoritm för att distribuera belastningen. Det kan dock leda till att vissa av våra meddelanden bearbetas i oordning.

### <a name="partitions"></a>Partitioner
För att säkerställa att våra meddelanden levereras till konsumenter i ordning och dra nytta av belastningsfördelningsfunktionen för partitioner, valde jag att skicka HTTP-begäran meddelanden till en partition och HTTP-svarsmeddelanden till en andra partition. Detta säkerställer en jämn belastningsfördelning och vi kan garantera att alla förfrågningar kommer att förbrukas i ordning och alla svar förbrukas i ordning. Det är möjligt att ett svar förbrukas före motsvarande begäran, men eftersom det inte är ett problem eftersom vi har en annan mekanism för att korrelera förfrågningar till svar och vi vet att förfrågningar alltid kommer före svar.

### <a name="http-payloads"></a>HTTP-nyttolaster
Efter att `requestLine`ha byggt , vi kontrollerar för att se om begäran kroppen bör trunkeras. Förfrågningsorganet trunkeras till endast 1024. Detta kan ökas, men enskilda Event Hub-meddelanden är begränsade till 256 KB, så det är troligt att vissa HTTP-meddelandeorgan inte får plats i ett enda meddelande. När du gör loggning och analys kan en betydande mängd information härledas från bara HTTP-begäranden och rubrikerna. Dessutom begär många API:er bara tillbaka små kroppar och så förlusten av informationsvärde genom att trunkera stora kroppar är ganska minimal i jämförelse med minskningen av överförings-, bearbetnings- och lagringskostnader för att hålla allt kroppsinnehåll. En sista anmärkning om bearbetning av kroppen `true` är `As<string>()` att vi måste passera till metoden eftersom vi läser kroppsinnehållet, men var också ville backend API för att kunna läsa kroppen. Genom att gå i uppfyllelse för denna metod, orsakar vi kroppen att buffras så att den kan läsas en andra gång. Detta är viktigt att vara medveten om om du har ett API som gör uppladdning av stora filer eller använder långa avsökningar. I dessa fall skulle det vara bäst att undvika att läsa kroppen alls.

### <a name="http-headers"></a>HTTP-huvuden
HTTP-huvuden kan överföras till meddelandeformatet i ett enkelt nyckel-/värdeparformat. Vi har valt att ta bort vissa säkerhetskänsliga fält, för att undvika onödigt läckande autentiseringsuppgifter. Det är osannolikt att API-nycklar och andra autentiseringsuppgifter skulle användas för analysändamål. Om vi vill göra analyser på användaren och den produkt de använder, `context` då kan vi få det från objektet och lägga till det i meddelandet.

### <a name="message-metadata"></a>Metadata för meddelanden
När du skapar hela meddelandet som ska skickas till händelsehubben `application/http` är den första raden faktiskt inte en del av meddelandet. Den första raden är ytterligare metadata som består av om meddelandet är en begäran eller ett svarsmeddelande och ett meddelande-ID, som används för att korrelera begäranden till svar. Meddelande-ID:et skapas med hjälp av en annan princip som ser ut så här:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Vi kunde ha skapat meddelandet om begäran, lagrat det i en variabel tills svaret returnerades och sedan skickat begäran och svaret som ett enda meddelande. Men genom att skicka begäran och svar självständigt och använda ett meddelande-ID för att korrelera de två, får vi lite mer flexibilitet i meddelandestorleken, möjligheten att dra nytta av flera partitioner samtidigt som meddelandeordningen och begäran kommer att visas i vår loggningsinstrumentpanel tidigare. Det kan också finnas vissa scenarier där ett giltigt svar aldrig skickas till händelsehubben, eventuellt på grund av ett fel på en fatal begäran i API Management-tjänsten, men vi har fortfarande en registrering av begäran.

Principen för att skicka http-meddelandet för svar liknar begäran och därför ser den fullständiga principkonfigurationen ut så här:

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

Principen `set-variable` skapar ett värde som är `log-to-eventhub` tillgängligt för `<inbound>` både `<outbound>` principen i avsnittet och avsnittet.

## <a name="receiving-events-from-event-hubs"></a>Ta emot händelser från eventhubbar
Händelser från Azure Event Hub tas emot med [AMQP-protokollet](https://www.amqp.org/). Microsoft Service Bus-teamet har gjort klientbibliotek tillgängliga för att göra de tidskrävande händelserna enklare. Det finns två olika metoder som stöds, en är `EventProcessorHost` att vara en direkt *konsument* och den andra använder klassen. Exempel på dessa två metoder finns i [programmeringsguiden för eventhubbar](../event-hubs/event-hubs-programming-guide.md). Den korta versionen av `Direct Consumer` skillnaderna är, ger `EventProcessorHost` dig fullständig kontroll och gör några av VVS arbete för dig men gör vissa antaganden om hur du bearbetar dessa händelser.

### <a name="eventprocessorhost"></a>EventProcessorHost
I det här exemplet `EventProcessorHost` använder vi för enkelhetens skull, men det kanske inte är det bästa valet för det här scenariot. `EventProcessorHost`gör det hårda arbetet med att se till att du inte behöver oroa dig för trådning frågor inom en viss händelse processor klass. Men i vårt scenario konverterar vi helt enkelt meddelandet till ett annat format och skickar det vidare till en annan tjänst med hjälp av en asynkron metod. Det finns inget behov av att uppdatera delat tillstånd och därför ingen risk för gängningsproblem. För de `EventProcessorHost` flesta scenarier, är förmodligen det bästa valet och det är verkligen det enklare alternativet.

### <a name="ieventprocessor"></a>IEventProcessor
Det centrala konceptet `EventProcessorHost` när du använder `IEventProcessor` är att skapa en `ProcessEventAsync`implementering av gränssnittet, som innehåller metoden . Kärnan i denna metod visas här:

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

En lista över EventData-objekt skickas till metoden och vi itererar över den listan. Bytena för varje metod tolkas till ett HttpMessage-objekt och objektet skickas till en instans av IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage (på ett sätt)
Instansen `HttpMessage` innehåller tre datadelar:

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

Instansen `HttpMessage` innehåller `MessageId` ett GUID som tillåter oss att ansluta HTTP-begäran till motsvarande HTTP-svar och ett booleskt värde som identifierar om objektet innehåller en instans av en HttpRequestMessage och HttpResponseMessage. Genom att använda de inbyggda HTTP-klasserna från `System.Net.Http` `application/http` kunde jag dra nytta `System.Net.Http.Formatting`av tolkningskoden som ingår i .  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Instansen `HttpMessage` vidarebefordras sedan till `IHttpMessageProcessor`implementering av , vilket är ett gränssnitt som jag har skapat för att frikoppla mottagandet och tolkningen av händelsen från Azure Event Hub och den faktiska bearbetningen av den.

## <a name="forwarding-the-http-message"></a>Vidarebefordra HTTP-meddelandet
För detta exempel bestämde jag mig för att det skulle vara intressant att driva HTTP Begäran över till [Moesif API Analytics](https://www.moesif.com). Moesif är en molnbaserad tjänst som specialiserar sig på HTTP-analys och felsökning. De har en gratis nivå, så det är lätt att prova och det tillåter oss att se HTTP-förfrågningar i realtid flyter genom vår API Management-tjänst.

Implementeringen `IHttpMessageProcessor` ser ut så här,

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

Drar `MoesifHttpMessageProcessor` nytta av ett [C# API-bibliotek för Moesif](https://www.moesif.com/docs/api?csharp#events) som gör det enkelt att skicka HTTP-händelsedata till sin tjänst. För att kunna skicka HTTP-data till Api:et för Moesif Collector behöver du ett konto och ett program-ID. Du får en Moesif Ansökan ID genom att skapa ett konto på [Moesif webbplats](https://www.moesif.com) och sedan gå till _övre högra menyn_ -> _App Setup_.

## <a name="complete-sample"></a>Fullständigt urval
[Källkoden](https://github.com/dgilling/ApimEventProcessor) och testerna för exemplet finns på GitHub. Du behöver en [API Management Service,](get-started-create-service-instance.md) [en ansluten händelsehubb](api-management-howto-log-event-hubs.md)och ett [lagringskonto](../storage/common/storage-create-storage-account.md) för att köra exemplet själv.   

Exemplet är bara ett enkelt konsolprogram som lyssnar efter händelser som kommer från `EventRequestModel` `EventResponseModel` Event Hub, konverterar dem till en Moesif och objekt och sedan vidarebefordrar dem till Moesif Collector API.

I följande animerade bild kan du se en begäran som görs till ett API i utvecklarportalen, konsolprogrammet som visar meddelandet som tas emot, bearbetas och vidarebefordras och sedan begäran och svar som visas i händelseströmmen.

![Demonstration av begäran som vidarebefordras till Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Sammanfattning
Azure API Management-tjänsten är en idealisk plats för att fånga HTTP-trafik som reser till och från dina API:er. Azure Event Hubs är en mycket skalbar, billig lösning för att fånga in den trafiken och mata in den i sekundära bearbetningssystem för loggning, övervakning och andra sofistikerade analyser. Ansluta till tredje part trafikövervakningssystem som Moesif är så enkelt som ett par dussin rader kod.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Event Hubs
  * [Komma igång med Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Läs mer om integrering av API-hantering och händelsehubbar
  * [Så här loggar du händelser till Azure Event Hubs i Azure API Management](api-management-howto-log-event-hubs.md)
  * [Referens för Logger-enhet](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referens för log-to-eventhub-princip](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
