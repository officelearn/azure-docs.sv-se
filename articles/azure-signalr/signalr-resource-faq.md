---
title: Vanliga frågor och svar om Azure SignalR Service
description: Få svar på vanliga frågor om Azure SignalR-tjänsten, inklusive fel sökning och vanliga användnings scenarier.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489569"
---
# <a name="azure-signalr-service-faq"></a>Vanliga frågor och svar Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Är Azure SignalR Service redo för användning i produktion?

Ja.
Meddelande om allmän tillgänglighet finns [nu allmänt tillgänglig i Azure SignalR-tjänsten](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) stöds fullt ut.

Stöd för ASP.NET-Signalerare finns fortfarande i en *offentlig för hands version*. [Här är ett kod exempel](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Klient anslutningen stängs med fel meddelandet "ingen server är tillgänglig". Vad betyder det?

Det här felet uppstår bara när klienter skickar meddelanden till Azure SignalR-tjänsten.

Om du inte har någon program Server och bara använder Azure SignalR-tjänsten REST API *, är detta avsiktligt*.
I Server lös arkitektur är klient anslutningar i *lyssnings* läge och skickar inga meddelanden till Azure SignalR-tjänsten.
Läs [mer om REST API](./signalr-quickstart-rest-api.md).

Om du har program servrar innebär det här fel meddelandet att ingen program Server är ansluten till din Azure SignalR-tjänstinstans.

Möjliga orsaker är:
- Ingen program Server är ansluten med Azure SignalR-tjänsten. Kontrollera programserverloggarna för möjliga anslutningsfel. Det här fallet är sällsynt i en hög tillgänglighets inställning som har mer än en program Server.
- Det finns anslutnings problem med Azure SignalR service instances. Det här problemet är tillfälligt och instanserna återställs automatiskt.
Om det finns kvar i mer än en timme, [öppna ett ärende på GitHub](https://github.com/Azure/azure-signalr/issues/new) eller [skapa en supportbegäran i Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>När det finns flera programservrar, skickas klientmeddelanden till alla servrar eller bara en av dem?

Det finns en en-till-en-mappning mellan en klient och en program Server. Meddelanden från en klient skickas alltid till samma programserver.

Mappningen upprätthålls tills klienten eller program servern kopplas från.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Om en av mina programservrar inte är tillgänglig, hur kan jag hitta den och bli meddelad?

Azure SignalR-tjänsten övervakar pulsslag från program servrar.
Om pulsslag inte tas emot för en angiven tidsperiod, anses programservern vara offline. Alla klientanslutningar som mappats till den här programservern kommer att kopplas från.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Varför genererar mitt eget `IUserIdProvider` undantag när jag byter från ASP.net Core SignalR SDK till Azure SignalR service SDK?

Parametern `HubConnectionContext context` skiljer sig mellan ASP.net Core signalerare SDK och Azure SignalR service SDK när `IUserIdProvider` anropas.

I ASP.NET Core SignalR är `HubConnectionContext context` kontexten från den fysiska klientanslutningen med giltiga värden för alla egenskaper.

I Azure SignalR service SDK `HubConnectionContext context` är kontexten från den logiska klient anslutningen. Den fysiska klienten är ansluten till Azure SignalR service-instansen, så endast ett begränsat antal egenskaper tillhandahålls.

För tillfället är endast `HubConnectionContext.GetHttpContext()` och `HubConnectionContext.User` tillgängliga för åtkomst.
Du kan [kontrol lera käll koden](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Kan jag konfigurera transporter som är tillgängliga i Azure SignalR-tjänsten på Server sidan med ASP.NET Core Signalerare? Kan jag till exempel inaktivera WebSocket-transport?

Nej.

Azure SignalR Service tillhandahåller alla tre transporter som ASP.NET Core SignalR stöder som standard. Det går inte att konfigurera. Azure SignalR service hanterar anslutningar och transporter för alla klient anslutningar.

Du kan konfigurera transporter på klient sidan som dokumenterade i [ASP.net Core signaler-konfiguration](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Vad är syftet med mått som antal meddelanden eller antal anslutningar som visas i Azure Portal? Vilken typ av agg regerings typ ska jag välja?

Du hittar information om hur vi beräknar dessa mått i [meddelanden och anslutningar i Azure SignalR-tjänsten](signalr-concept-messages-and-connections.md).

I översikts fönstret för Azure SignalR service-resurser har vi redan valt rätt agg regerings typ åt dig. Om du går till fönstret mått kan du ta sammansättnings typen till [meddelanden och anslutningar i Azure SignalR-tjänsten](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) som en referens.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Vad är syftet med `Default` `Serverless` `Classic` tjänst lägena, och. Hur kan jag välja?

Här är information om lägena:
* `Default` läget *kräver* en NAV Server. I det här läget dirigerar Azure SignalR-tjänsten klient trafiken till anslutna NAV Server-anslutningar. Azure SignalR service söker efter en ansluten NAV Server. Om tjänsten inte kan hitta en ansluten NAV Server avvisar den inkommande klient anslutningarna. Du kan också använda *hanterings-API: et* i det här läget för att hantera anslutna klienter direkt via Azure SignalR-tjänsten.
* `Serverless` läget tillåter *inte* någon server anslutning. Det innebär att alla Server anslutningar nekas. Alla klienter måste vara i ett Server fritt läge. Klienterna ansluter till Azure SignalR-tjänsten och användarna använder vanligt vis Server lös teknik som *Azure Functions* för att hantera Hubbs logiker. [Se ett enkelt exempel](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) som använder läget utan server i Azure SignalR-tjänsten.
* `Classic` läge är en blandad status. När en hubb har en server anslutning kommer den nya klienten att dirigeras till en NAV Server. Om inte, kommer klienten att övergå i ett läge utan server. 

  Detta kan orsaka ett problem. Om till exempel alla Server anslutningar går förlorade för tillfället, kommer vissa klienter att övergå i ett läge utan server i stället för att dirigeras till en NAV Server.

Här följer några rikt linjer för att välja ett läge:
- Om det inte finns någon NAV-Server väljer du `Serverless` .
- Om alla nav har nav-servrar väljer du `Default` .
- Om vissa hubbar har Hubbs servrar men andra inte kan du välja `Classic` , men det kan orsaka problem. Det bästa sättet är att skapa två instanser: en är `Serverless` och den andra är `Default` .

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Finns det några funktions skillnader i att använda Azure SignalR service med ASP.NET-Signalerare?
När du använder Azure SignalR-tjänsten stöds inte vissa API: er och funktioner i ASP.NET-signaler:
- Möjligheten att skicka godtyckligt tillstånd mellan klienter och hubben (kallas ofta `HubState` ) stöds inte.
- `PersistentConnection`Klassen stöds inte.
- För *alltid-Frame-transport* stöds inte.
- Azure SignalR-tjänsten spelar inte längre upp meddelanden som skickas till klienten när klienten är offline.
- När du använder Azure SignalR-tjänsten dirigeras trafiken för en klient anslutning alltid (kallas även *trög*) till en app Server-instans under anslutningens varaktighet.

Stöd för ASP.NET-signaler är inriktad på kompatibilitet, så alla nya funktioner från ASP.NET Core-Signalerare stöds inte. Till exempel är *MessagePack* och *streaming* endast tillgängligt för ASP.net Core signalerare-program.

Du kan konfigurera Azure SignalR service för olika tjänst lägen: `Classic` , `Default` och `Serverless` . `Serverless`Läget stöds inte för ASP.net. Data Plans REST API stöds inte heller.

## <a name="where-does-my-data-reside"></a>Var finns mina data?

Azure SignalR service fungerar som en data behandlings tjänst. Det kommer inte att lagra något kund innehåll och data placering ingår i design. Om du använder Azure SignalR-tjänsten tillsammans med andra Azure-tjänster, t. ex. Azure Storage för diagnostik, se [den här White Paper](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) för vägledning om hur du håller data placering i Azure-regioner.
