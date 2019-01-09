---
title: Vanliga frågor och svar om Azure SignalR Service
description: Vanliga frågor och svar om Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 12/23/2018
ms.author: zhshang
ms.openlocfilehash: 45ceebef85254ff030d237f1af13f0750aaafeba
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812857"
---
# <a name="azure-signalr-service-faq"></a>Vanliga frågor och svar Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Är Azure SignalR Service redo för användning i produktion?

Ja.
Vårt tillkännagivande om allmän tillgänglighet finns på [Azure SignalR Service är nu allmänt tillgänglig](https://azure.microsoft.com/en-us/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/en-us/aspnet/core/signalr/introduction) stöds fullt ut.

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
Om det finns kvar i mer än en timme, [öppna ett ärende på GitHub](https://github.com/Azure/azure-signalr/issues/new) eller [skapa en supportbegäran i Azure](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request).

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
Du hittar källkoden [här](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Kan jag konfigurera transporter som är tillgängliga i SignalR Service som när jag konfigurerar på serversidan med ASP.NET Core SignalR? Till exempel inaktivera WebSocket-transport?

Nej.

Azure SignalR Service tillhandahåller alla tre transporter som ASP.NET Core SignalR stöder som standard. Den kan inte konfigureras. SignalR Service hanterar anslutningar och transporter för alla klientanslutningar.

Du kan konfigurera transporter på klientsidan enligt beskrivningen [här](https://docs.microsoft.com/en-us/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports).
