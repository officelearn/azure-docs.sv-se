---
title: Vad är Azure Relay och varför ska jag använda det – översikt | Microsoft Docs
description: Översikt över Azure Relay
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: dc616f18033014a5dcc9e5d15434497978484bc1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43695973"
---
# <a name="what-is-azure-relay"></a>Vad är Azure Relay?

Tjänsten Azure Relay hjälper dig att använda hybridprogram genom att på ett säkert sätt exponera tjänster som finns i ett företagsnätverk mot det offentliga molnet, utan att du behöver öppna en brandväggsanslutning eller göra störande ändringar i företagets nätverksinfrastruktur. Relay stöder en mängd olika transportprotokoll och webbtjänststandarder.

Relay-tjänsten stöder traditionell enkelriktad trafik för förfrågningar/svar och peer-to-peer-trafik. Den stöder även händelsedistribution på Internet-skala för att möjliggöra publicerings- och prenumerationsscenarier och dubbelriktad socketkommunikation för ökad effektivitet punkt till punkt.

I överföringsmönstret för vidarebefordrade data ansluter en lokal tjänst till den vidarebefordrande tjänsten via en utgående port och skapar en dubbelriktad socket för kommunikation som är kopplad till en viss rendezvous-adress. Klienten kan sedan kommunicera med den lokala tjänsten genom att skicka trafik till den vidarebefordrande tjänsten med rendezvous-adressen som mål. Relay-tjänsten "vidarebefordrar" sedan data till den lokala tjänsten via en dubbelriktad socket för varje klient. Klienten behöver ingen direkt anslutning till den lokala tjänsten och behöver inte heller veta var den finns. Den lokala tjänsten behöver inte ha några öppna ingående portar i brandväggen.

De viktigaste funktionerna som tillhandahålls av Relay är dubbelriktad, obuffrad kommunikation över nätverksgränser med TCP-liknande begränsning, slutpunktsidentifiering, anslutningsstatus och överlappande slutspunktssäkerhet.

Funktionerna i Relay skiljer sig från integreringstekniker på nätverksnivå som VPN. Till exempel kan Relay begränsas till en enskild programslutpunkt på en enskild dator, till skillnad mot VPN-teknik som är mer inkräktande eftersom den kräver ändringar i nätverksmiljön.

Azure Relay erbjuder två funktioner:

1. [Hybridanslutningar](#hybrid-connections) – Använder den öppna standarden Web Sockets som ger stöd för scenarier med flera plattformar.
2. [WCF-reläer](#wcf-relays) – Använder Windows Communication Foundation (WCF) för att aktivera RPC-anrop. WCF Relay är den äldre Relay-tjänsten som många kunder redan använder med sina WCF-programmeringsmodeller.

Både hybridanslutningar och WCF-reläer tillhandahåller säker anslutning till resurser som finns i ett företagsnätverk. Användning av det ena eller andra beror på dina specifika behov, baserat på informationen i följande tabell:

|  | WCF-relä | Hybridanslutningar |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/NodeJS** | |x |
| **Standardbaserat öppet protokoll** | |x |
| **Flera RPC-programmeringsmodeller** | |x |

## <a name="hybrid-connections"></a>Hybridanslutningar

Funktionen Hybridanslutningar i Azure Relay är en säker vidareutveckling med öppet protokoll av de befintliga Relay-funktionerna och kan implementeras på valfri plattform och på valfritt språk. Hybridanslutningar kan vidarebefordra WebSockets samt HTTP(S)-begäranden och svar. Dessa funktioner är kompatibla med WebSocket API i vanliga webbläsare. Hybridanslutningar baseras på HTTP och WebSockets.

Protokollet är fullständigt dokumenterat i guiden om [Hybridanslutningsprotokoll](relay-hybrid-connections-protocol.md), vilket tillåter användning av hybridanslutningsreläer med praktiskt taget alla Websockets-bibliotek för alla körningar och språk.

### <a name="service-history"></a>Tjänsthistorik

Hybridanslutningar ersätter den tidigare funktionen ”BizTalk Services” som baserades på Azure Service Bus WCF Relay. Den nya funktionen Hybridanslutningar kompletterar befintliga WCF Relay och båda dessa två tjänstfunktioner kommer att finnas sida vid sida i Azure Relay-tjänsten. De delar en gemensam gateway, men är i övrigt olika implementeringar.

## <a name="wcf-relay"></a>WCF-relä

WCF Relay har stöd för hela .NET Framework (NETFX) och för WCF. Du upprättar anslutningen mellan din lokala tjänst och den vidarebefordrande tjänsten med hjälp av en uppsättning ”vidarebefordrande” WCF-bindningar. I bakgrunden mappas vidarebefordringsbindningarna till nya transportbindningselement som är utformade för att skapa WCF-kanalkomponenter som integreras med Service Bus i molnet. Mer information finns i [Komma igång med WCF Relay](relay-wcf-dotnet-get-started.md).

## <a name="architecture-processing-of-incoming-relay-requests"></a>Arkitektur: Bearbetning av inkommande vidarebefordrade begäranden

När en klient skickar en begäran till tjänsten [Azure Relay](/azure/service-bus-relay/) skickar Azure-lastbalanseraren den vidare till någon av gateway-noderna. Om det handlar om en begäran om att lyssna, skapas en ny vidarebefordran av gateway-noden. Om det handlar om en begäran om anslutning till en specifik vidarebefordran skickar gateway-noden begäran vidare till gateway-noden som äger vidarebefordran. Gateway-noden som äger vidarebefordran skickar en rendezvous-begäran till den lyssnande klienten och ber lyssnaren skapa en tillfällig kanal till gateway-noden som tog emot anslutningsbegäran.

Klienterna kan utbyta meddelanden via gateway-noden som används för rendezvous när anslutningen med vidarebefordran har upprättats.

![Bearbetning av inkommande WCF Relay-begäranden](./media/relay-what-is-it/ic690645.png)

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Komma igång med .NET Websockets](relay-hybrid-connections-dotnet-get-started.md)
* [Komma igång med .NET HTTP-begäranden](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Komma igång med Node Websockets](relay-hybrid-connections-node-get-started.md)
* [Komma igång med Node HTTP-begäranden](relay-hybrid-connections-http-requests-node-get-started.md)

