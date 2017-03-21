---
title: "Vad är Azure Relay och varför ska jag använda det – översikt | Microsoft Docs"
description: "Översikt över Azure Relay"
services: service-bus-relay
documentationcenter: .net
author: banisadr
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 03/09/2017
ms.author: babanisa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9fd40892c77630bd4f0b7abf5c3458a6dc200402
ms.lasthandoff: 03/09/2017


---
# <a name="what-is-azure-relay"></a>Vad är Azure Relay?
Tjänsten Azure Relay hjälper dig att använda hybridprogram genom att på ett säkert sätt exponera tjänster som finns i ett företagsnätverk mot det offentliga molnet, utan att du behöver öppna en brandväggsanslutning eller göra störande ändringar i företagets nätverksinfrastruktur. Relay stöder en mängd olika transportprotokoll och webbtjänststandarder.

Relay-tjänsten stöder traditionell enkelriktad trafik för förfrågningar/svar och peer-to-peer-trafik. Den stöder även händelsedistribution på Internet-skala för att möjliggöra publicerings- och prenumerationsscenarier och dubbelriktad socketkommunikation för ökad effektivitet punkt till punkt. 

I överföringsmönstret för vidarebefordrade data ansluter en lokal tjänst till den vidarebefordrande tjänsten via en utgående port och skapar en dubbelriktad socket för kommunikation som är kopplad till en viss rendezvous-adress. Klienten kan sedan kommunicera med den lokala tjänsten genom att skicka trafik till den vidarebefordrande tjänsten med rendezvous-adressen som mål. Relay-tjänsten "vidarebefordrar" sedan data till den lokala tjänsten via en dubbelriktad socket för varje klient. Klienten behöver ingen direkt anslutning till den lokala tjänsten och behöver inte heller veta var den finns. Den lokala tjänsten behöver inte ha några öppna ingående portar i brandväggen.

De viktigaste funktionerna som tillhandahålls av Relay är dubbelriktad, obuffrad kommunikation över nätverksgränser med TCP-liknande begränsning, slutpunktsidentifiering, anslutningsstatus och överlappande slutspunktssäkerhet. Funktionerna i Relay skiljer sig från integreringstekniker på nätverksnivå som VPN. Till exempel kan Relay begränsas till en enskild programslutpunkt på en enskild dator, till skillnad mot VPN-teknik som är mer inkräktande eftersom den kräver ändringar i nätverksmiljön.

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
Funktionen [Hybridanslutningar i Azure Relay](relay-hybrid-connections-protocol.md) är en säker vidareutveckling med öppet protokoll av de befintliga Relay-funktionerna och kan implementeras på valfri plattform och på valfritt språk som har en grundläggande WebSocket-funktion, vilket bland annat omfattar WebSocket-API:et i vanliga webbläsare. Hybridanslutningar baseras på HTTP och WebSockets.

## <a name="wcf-relays"></a>WCF-reläer
WCF Relay har stöd för hela .NET Framework (NETFX) och för WCF. Du upprättar anslutningen mellan din lokala tjänst och den vidarebefordrande tjänsten med hjälp av en uppsättning ”vidarebefordrande” WCF-bindningar. I bakgrunden mappas vidarebefordringsbindningarna till nya transportbindningselement som är utformade för att skapa WCF-kanalkomponenter som integreras med Service Bus i molnet.

## <a name="service-history"></a>Tjänsthistorik
Hybridanslutningar ersätter den tidigare funktionen ”BizTalk Services” som baserades på Azure Service Bus WCF Relay. Den nya funktionen för hybridanslutningar kompletterar befintliga WCF Relay och båda dessa två tjänstfunktioner kommer att finnas sida vid sida i Relay-tjänsten under överskådlig tid. De delar en gemensam gateway, men är i övrigt olika implementeringar.

## <a name="next-steps"></a>Nästa steg:
* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)


