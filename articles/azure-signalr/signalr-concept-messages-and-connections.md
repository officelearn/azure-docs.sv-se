---
title: Meddelanden och anslutningar i Azure SignalR Service
description: En översikt över viktiga begrepp gällande meddelanden och anslutningar i Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 2785d85db47ed3b214044e673566a2837b83e984
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285484"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Meddelanden och anslutningar i Azure SignalR Service

Faktureringsmodellen för Azure SignalR Service baseras på antalet anslutningar och antalet meddelanden. I den här artikeln förklaras hur meddelanden och anslutningar definieras och räknas.


## <a name="message-formats"></a>Meddelandeformat 

Azure SignalR service stöder samma format som ASP.NET Core Signalerare: [JSON](https://www.json.org/) och [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Meddelandestorlek

Azure SignalR Service har ingen storleksgräns för meddelanden.

Stora meddelanden delas upp i mindre meddelanden på högst 2 KB var som överförs separat. SDK:er hanterar delning och sammansättning av meddelanden. Inget utvecklararbete krävs.

Stora meddelanden påverkar meddelandeprestanda negativt. Använd mindre meddelanden när det är möjligt och testa dig fram till den optimala meddelandestorleken för varje användningsfall.

## <a name="how-messages-are-counted-for-billing"></a>Så beräknas meddelanden för fakturering

För fakturering räknas endast utgående meddelanden från Azure SignalR Service. Pingmeddelanden mellan klienter och servrar ignoreras.

Meddelanden som är större än 2 KB räknas som flera meddelanden på 2 KB vartdera. Diagrammet över antal meddelanden i Azure-portalen uppdateras vart 100:e meddelande för varje hubb.

Anta som exempel att du har tre klienter och en programserver. En klient skickar ett meddelande på 4 KB som servern ska skicka till alla klienter. Antalet meddelanden är åtta: ett meddelande från tjänsten att programservern och tre meddelanden från tjänsten till klienterna. Varje meddelande räknas som två meddelanden på 2 KB vartdera.

Det antal meddelanden som visas i Azure-portalen förblir 0 tills det överstiger 100.

## <a name="how-connections-are-counted"></a>Så räknas anslutningar

Det finns Server anslutningar och klient anslutningar med Azure SignalR-tjänsten. Som standard startar varje program server med fem första anslutningar per hubb och varje klient har en klient anslutning.

Det antal anslutningar som visas i Azure-portalen omfattar både serveranslutningar och klientanslutningar.

Anta som exempel att du har två programservrar och att du definierar fem hubbar i kod. Antalet Server anslutningar är 50:2 App-servrar * 5 hubbar * 5 anslutningar per hubb.

ASP.NET SignalR beräknar serveranslutningar på ett annat sätt. Det omfattar en standardhubb utöver de hubbar som du definierar. Som standard behöver varje program Server fem fler inledande Server anslutningar. Det första antalet anslutningar för standard navet förblir konsekvent med det andra hubbens.

Under program serverns livs längd behåller tjänsten och program servern synkroniseringens anslutnings status och gör justeringar till Server anslutningar för bättre prestanda och service stabilitet. Så du kan se ändringar av serverns anslutnings nummer från tid till tid.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Så räknas inkommande/utgående trafik

Skillnaden mellan inkommande och utgående trafik baseras på perspektivet för Azure SignalR Service. Trafik beräknas i byte. Liksom meddelandeantalet har även trafiken en samplingsfrekvens. Diagrammet för inkommande/utgående i Azure-portalen uppdateras varje 100 KB per hubb.

## <a name="related-resources"></a>Relaterade resurser

- [Sammansättningstyper i Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR-konfiguration](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
