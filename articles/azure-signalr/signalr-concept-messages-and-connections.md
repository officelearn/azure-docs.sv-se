---
title: Meddelanden och anslutningar i Azure SignalR Service
description: En översikt över viktiga begrepp gällande meddelanden och anslutningar i Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e82ce8f5c97aed7e2cb832d8e808ff84691f7c9e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555306"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Meddelanden och anslutningar i Azure SignalR Service

Faktureringsmodellen för Azure SignalR Service baseras på antalet anslutningar och antalet meddelanden. I den här artikeln förklaras hur meddelanden och anslutningar definieras och räknas.


## <a name="message-formats"></a>Meddelandeformat 

Azure SignalR Service stöder samma format som ASP.NET Core SignalR: [JSON](https://www.json.org/) och [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

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

Det finns serveranslutningar och klientanslutningar. Som standard har varje programserver fem anslutningar per hubb med Azure SignalR Service, och varje klient har en klientanslutning med Azure SignalR Service.

Det antal anslutningar som visas i Azure-portalen omfattar både serveranslutningar och klientanslutningar.

Anta som exempel att du har två programservrar och att du definierar fem hubbar i kod. Antalet serveranslutningar blir då 50: 2 appservrar * 5 hubbar * 5 anslutningar per hubb.

ASP.NET SignalR beräknar serveranslutningar på ett annat sätt. Det omfattar en standardhubb utöver de hubbar som du definierar. Som standard behöver varje programserver fem fler serveranslutningar. Antalet anslutningar för standardhubben hålls konsekvent med antalet för de andra hubbarna.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Så räknas inkommande/utgående trafik

Skillnaden mellan inkommande och utgående trafik baseras på perspektivet för Azure SignalR Service. Trafik beräknas i byte. Liksom meddelandeantalet har även trafiken en samplingsfrekvens. Diagrammet för inkommande/utgående i Azure-portalen uppdateras varje 100 KB per hubb.

## <a name="related-resources"></a>Relaterade resurser

- [Sammansättningstyper i Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR-konfiguration](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)