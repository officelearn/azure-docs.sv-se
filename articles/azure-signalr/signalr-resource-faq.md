---
title: Vanliga frågor och svar om Azure SignalR Service
description: Få snabb åtkomst till vanliga frågor om Azure SignalR-tjänsten, om fel sökning och typiska användnings scenarier.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 6d104e41a0cae906c346e81a26617a9d29795fb3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853275"
---
# <a name="azure-signalr-service-faq"></a>Vanliga frågor och svar Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Är Azure SignalR Service redo för användning i produktion?

Ja.
Vårt tillkännagivande om allmän tillgänglighet finns på [Azure SignalR Service är nu allmänt tillgänglig](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) stöds fullt ut.

Stöd för ASP.NET SignalR är fortfarande i *offentlig förhandsversion*. Här är ett [kodexempel](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Klientanslutningen avslutas med felmeddelandet Ingen server tillgänglig. Vad betyder det?

Det här felet inträffar bara när klienter skickar meddelanden till SignalR Service.

Om du inte har någon programserver och bara använder SignalR Service REST API så är det här beteendet **avsiktligt**.
I en serverlös arkitektur så är klientanslutningar i **lyssna**-läge och kommer inte skicka meddelanden till SignalR Service.
Läs mer om [REST API](./signalr-quickstart-rest-api.md).

Om du har programservrar så kan det här felmeddelandet betyda att ingen programserver är ansluten till din SignalR Service-instans.

Möjliga orsaker är:
- Ingen programserver är ansluten med SignalR Service. Kontrollera programserverloggarna för möjliga anslutningsfel. Det här fallet är sällsynt i inställningen för hög tillgänglighet med mer än en programserver.
- Det finns anslutningsproblem med SignalR Service-instanser. Det här problemet är tillfälligt och återställs automatiskt.
Om det finns kvar i mer än en timme, [öppna ett ärende på GitHub](https://github.com/Azure/azure-signalr/issues/new) eller [skapa en supportbegäran i Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>När det finns flera programservrar, skickas klientmeddelanden till alla servrar eller bara en av dem?

Det finns en en-till-en-mappningen mellan klient och programserver. Meddelanden från en klient skickas alltid till samma programserver.

Mappningen mellan klient och programserver bibehålls tills klienten eller programservern kopplar från.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Om en av mina programservrar inte är tillgänglig, hur kan jag hitta den och bli meddelad?

SignalR Service övervakar pulsslag från programservrar.
Om pulsslag inte tas emot för en angiven tidsperiod, anses programservern vara offline. Alla klientanslutningar som mappats till den här programservern kommer att kopplas från.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Varför utlöser min anpassade `IUserIdProvider` undantag när jag växlar från ASP.NET Core SignalR SDK till Azure SignalR Service SDK?

Parametern `HubConnectionContext context` skiljer sig mellan ASP.NET Core SignalR SDK och Azure SignalR Service SDK när `IUserIdProvider` anropas.

I ASP.NET Core SignalR är `HubConnectionContext context` kontexten från den fysiska klientanslutningen med giltiga värden för alla egenskaper.

I Azure SignalR Service SDK är `HubConnectionContext context` kontexten från den logiska klientanslutningen. Den fysiska klientanslutningen är ansluten till SignalR Service-instansen så enbart ett begränsat antal egenskaper anges.

För tillfället är endast `HubConnectionContext.GetHttpContext()` och `HubConnectionContext.User` tillgängliga för åtkomst.
Du hittar källkoden [här](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Kan jag konfigurera transporter som är tillgängliga i SignalR Service som när jag konfigurerar på serversidan med ASP.NET Core SignalR? Till exempel inaktivera WebSocket-transport?

Nej.

Azure SignalR Service tillhandahåller alla tre transporter som ASP.NET Core SignalR stöder som standard. Den kan inte konfigureras. SignalR Service hanterar anslutningar och transporter för alla klientanslutningar.

Du kan konfigurera transporter på klientsidan enligt beskrivningen [här](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Vad är syftet med mått som antal meddelanden eller antal anslutningar som visas i Azure Portal? Vilken typ av agg regerings typ ska jag välja?

Du hittar information om hur gör vi för att beräkna dessa mått [här](signalr-concept-messages-and-connections.md).

I översikts bladet för Azure SignalR service-resurser har vi redan valt rätt agg regerings typ åt dig. Och om du går till bladet mått kan du använda sammansättnings typen [här](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) som en referens.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Vad är tjänst lägets betydelse `Default` / `Serverless` / `Classic` ? Hur kan jag välja?

Lägen
* `Default` läget *kräver* NAV Server. I det här läget dirigerar Azure-signaler klient trafiken till anslutna NAV Server-anslutningar. Azure-SignalR söker efter en ansluten NAV Server. Om en ansluten NAV Server inte hittas avvisar Azure-Signaleraren inkommande klient anslutningar. Du kan också använda **hanterings-API** i det här läget för att hantera de anslutna klienterna direkt via Azure SignalR.
* `Serverless` läget tillåter *inte* någon server anslutning, d.v.s. alla Server anslutningar kommer att nekas. Alla klienter måste vara i ett Server fritt läge. Klienterna ansluter till Azure-Signaleraren och användarna använder vanligt vis Server lös teknik, till exempel **Azure Function** , för att hantera Hubbs logik. Se ett [enkelt exempel](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) som använder Azure SignalR-läge utan server.
* `Classic` läge är en blandad status. När en hubb har server anslutning dirigeras den nya klienten till NAV Server, om inte, kommer klienten att övergå i ett läge utan server.

  Detta kan orsaka vissa problem, till exempel om alla Server anslutningar går förlorade under en viss tid, kommer vissa klienter att övergå i ett läge utan server, i stället för att dirigera till NAV Server.

Prestanda
1. Ingen NAV Server, Välj `Serverless` .
1. Alla hubbar har nav servrar, Välj `Default` .
1. Vissa hubbar har hubb servrar, andra inte, Välj `Classic` , men det kan orsaka vissa problem. det bästa sättet är att skapa två instanser, en `Serverless` annan är `Default` .

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Eventuella funktions skillnader när du använder Azure SignalR för ASP.NET-Signalerare?
När du använder Azure-Signalerare stöds inte längre vissa API: er och funktioner i ASP.NET-signaler:
- Möjligheten att skicka godtyckligt tillstånd mellan klienter och hubben (kallas ofta `HubState` ) stöds inte när Azure-signalerare används
- `PersistentConnection` klassen stöds ännu inte när Azure-Signaleraren används
- För **alltid-Frame-transport** stöds inte när Azure-signalerare används
- Azure SignalR spelar inte längre upp meddelanden som skickas till klienten när klienten är offline
- När du använder Azure-signaler dirigeras trafiken för en klient anslutning alltid (aka. **trög**) till en app Server-instans under anslutningens varaktighet

Stöd för ASP.NET-signaler är inriktad på kompatibilitet, så alla nya funktioner från ASP.NET Core SignalR stöds inte. Till exempel, **MessagePack**, **streaming**osv., är bara tillgängliga för ASP.net Core signalerare-program.

SignalR-tjänsten kan konfigureras för olika tjänst lägen: `Classic` / `Default` / `Serverles` s. I det här ASP.NET-stödet `Serverless` stöds inte läget. Data Plans REST API stöds inte heller.

## <a name="where-do-my-data-reside"></a>Var finns mina data?

Azure SignalR service fungerar som en data processor tjänst. Det kommer inte att lagra något kund innehåll och data placering har utlovats av design. Om du använder Azure SignalR-tjänsten tillsammans med andra Azure-tjänster, t. ex. Azure Storage för diagnostik, kan du [läsa mer om](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) hur du håller data placering i Azure-regioner.
