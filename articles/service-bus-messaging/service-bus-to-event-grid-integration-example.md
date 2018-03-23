---
title: Integrationsexempel på Azure Service Bus till Event Grid | Microsoft Docs
description: Exempel på Service Bus-meddelanden och Event Grid-integration
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 3819a274696762861fbe76a9684b8495f1724f6a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Exempel på Azure Service Bus till Azure Event Grid

I det här dokumentet lär du dig hur du konfigurerar Azure-funktioner och en logikapp som tar emot meddelanden baserat på när en händelse från Event Grid tas emot. I exemplet förutsätter vi ett Service Bus-ämne med två prenumerationer och en Event Grid-prenumeration har skapats för att kunna skicka händelser till enbart en Service Bus-prenumeration. Därefter skickar du meddelanden till Service Bus-ämnet och kontrollerar att händelsen genereras för Service Bus-prenumerationen, samt att funktionen eller logikappen får meddelandena från Service Bus-prenumerationen och slutför den.

* Kontrollera att du har alla [förutsättningar](#prerequisites) innan du börjar.
* Skapa ett [enkelt Azure-funktionstest](#test-function-setup) för felsökning och för att se det första flödet av händelser från Event Grid.  **Det här steget måste göras oavsett om 3. eller 4. körs.**
* Skapa en [Azure-funktion som tar emot och bearbetar Service Bus-meddelanden](#receive-messages-using-azure-function) baserat på Event Grid-händelser.
* Använda [Logic Apps](#receive-messages-using-azure-logic-app).

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="service-bus-namespace"></a>Service Bus-namnområde

Skapa ett namnområde för Service Bus Premium. Skapa ett Service Bus-ämne med två prenumerationer.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Kod för att skicka meddelande till Service Bus-ämnet

Du kan använda andra metoder för att skicka ett meddelande till ditt Service Bus-ämne. Du kan också använda exemplet nedan. Exempelkoden förutsätter att du använder Visual Studio 2017.

Klona [den här GitHub-lagringsplatsen](https://github.com/Azure/azure-service-bus/).

Gå till följande mapp:

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration och öppna filen: SBEventGridIntegration.sln.

Gå sedan till projektet MessageSender och öppna Program.cs.

![8][]

Fyll i avsnittets namn och anslutningssträng och kör konsolprogrammet:

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>Konfiguration av testfunktion

Innan du går igenom hela slutpunkt till slutpunkt-scenariot kan det vara bra att ha minst en liten testfunktion, där du kan felsöka och se vilka händelser som finns i flödet.

Skapa ett nytt Azure-funktionsprogram i portalen. Följer denna [länk](https://docs.microsoft.com/en-us/azure/azure-functions/) för att se grunderna i Azure Functions.

Klicka på plustecknet om du vill lägga till en HTTP-utlösare i din nya funktion:

![2][]

![3][]

Kopiera sedan följande kod till funktionen:

```CSharp
#r "Newtonsoft.Json"
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    // parse query parameter
    var content = req.Content;

    string jsonContent = await content.ReadAsStringAsync(); 
    log.Info($"Received Event with payload: {jsonContent}");

IEnumerable<string> headerValues;
if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
{
var validationHeaderValue = headerValues.FirstOrDefault();
if(validationHeaderValue == "SubscriptionValidation")
{
var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
     var code = events[0].Data["validationCode"];
     return req.CreateResponse(HttpStatusCode.OK,
     new { validationResponse = code });
}
}

    return jsonContent == null
    ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
    : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
}

public class GridEvent
{
    public string Id { get; set; }
    public string EventType { get; set; }
    public string Subject { get; set; }
    public DateTime EventTime { get; set; }
    public Dictionary<string, string> Data { get; set; }
    public string Topic { get; set; }
}
```

Klicka på Spara och kör.

## <a name="connect-function-and-namespace-via-event-grid"></a>Anslut funktionen och namnområdet via Event Grid

Nästa steg är att koppla ihop funktionen och Service Bus-namnområdet. I det här exemplet använder du Azure-portalen. På sidan med [koncept](service-bus-to-event-grid-integration-concept.md) finns en beskrivning av hur du använder PowerShell eller Azure CLI till att göra samma sak.

Gå till namnområdet i Azure-portalen för att skapa en ny Azure Event Grid-prenumeration och välj bladet Event Grid. Klicka på ”+ Händelseprenumeration”.

Följande skärmbild med ett namnområde som redan har några Event Grid-prenumerationer visas.

![20][]

Följande skärmbild visar hur du prenumererar på en Azure-funktion eller en webhook utan någon specifik filtrering. Kom ihåg att lägga till filtret för din Service Bus-prenumeration som ett ”Suffixfilter”:

![21][]

Skicka ett meddelande till ditt Service Bus-ämne enligt förutsättningarna och kontrollera att händelser flödar in via övervakningen i Azure-funktionen.

![9][]

### <a name="receive-messages-using-azure-function"></a>Ta emot meddelanden med Azure-funktionen

I det föregående avsnittet visade vi ett enkelt test och felsökningsscenario där du kunde se att händelser flödar in. I den här delen av dokumentationen ska vi titta på hur du tar emot och bearbetar meddelanden när du en händelse skickas till dig.

Orsaken till att du lägger till en Azure-funktion på följande sätt är att Service Bus-funktionerna i Azure Functions inte stöder den nya Event Grid-integrationen än.

Välj ReceiveMessagesOnEvent.cs i samma Visual Studio-lösning som du öppnade i förutsättningarna. Ange din anslutningssträng i koden:

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Gå sedan till Azure-portalen och ladda ner publiceringsprofilen för den Azure-funktion som du skapade innan i [2. Konfiguration av testfunktion](#2-test-function-setup).

![11][]

Högerklicka sedan på SBEventGridIntegration i Visual Studio och välj Publicera. Använd publiceringsprofilen som du laddade ner innan du väljer importprofil och klicka på Publicera.

![12][]

När du har publicerat den nya Azure-funktionen skapar du en ny Azure Event Grid-prenumeration som pekar på den nya Azure-funktionen. Kontrollera att du använder rätt ”Slutar med”-filter, vilket ska vara ditt Service Bus-prenumerationsnamn.

Skicka sedan ett meddelande till det Azure Service Bus-ämne som du skapade innan. Kontrollera i Azure-funktionsloggen i portalen att händelser flödar in och att meddelanden tas emot.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Ta emot meddelanden med Azure-logikappen

I följande anvisningar visas hur du ansluter en Azure-logikapp tillsammans med Azure Service Bus och Azure Event Grid:

Först skapar du en ny logikapp i Azure-portalen och väljer Event Grid som startåtgärd.

![13][]

Startvyn i Logic Apps-designern ska se ut som nedanstående skärmbild, där du ersätter ”Resursnamn” med ditt eget namn för namnområdet. Se också till att visa de avancerade alternativen och lägga till suffixfiltret för din prenumeration:

![14][]

Lägg sedan till en Service Bus-mottagningsåtgärd för att kunna ta emot från en ämnesprenumeration. Den slutliga åtgärden bör likna följande skärmbild.

![15][]

Lägg sedan till en fullständig händelse, som bör se ut så här.

![16][]

Spara logikappen och skicka ett meddelande till ditt Service Bus-ämne enligt förutsättningarna. Iaktta sedan Logic Apps-körningen. Om du klickar på ”Översikt” och det finns ”Körningshistorik”, kan du få mer information om körningen.

![17][]

![18][]

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Läs mer om [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Läs mer om [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/).
* Läs mer om [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
