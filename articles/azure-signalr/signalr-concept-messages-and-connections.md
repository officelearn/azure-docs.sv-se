---
title: Meddelanden och anslutningar i Azure SignalR Service
description: En översikt över viktiga begrepp gällande meddelanden och anslutningar i Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75392817"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Meddelanden och anslutningar i Azure SignalR Service

Faktureringsmodellen för Azure SignalR Service baseras på antalet anslutningar och antalet meddelanden. I den här artikeln förklaras hur meddelanden och anslutningar definieras och räknas.


## <a name="message-formats"></a>Meddelandeformat 

Azure SignalR-tjänsten stöder samma format som ASP.NET Core SignalR: [JSON](https://www.json.org/) och [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Meddelandestorlek

Azure SignalR Service har ingen storleksgräns för meddelanden.

Stora meddelanden delas upp i mindre meddelanden på högst 2 KB var som överförs separat. SDK:er hanterar delning och sammansättning av meddelanden. Inget utvecklararbete krävs.

Stora meddelanden påverkar meddelandeprestanda negativt. Använd mindre meddelanden när det är möjligt och testa dig fram till den optimala meddelandestorleken för varje användningsfall.

## <a name="how-messages-are-counted-for-billing"></a>Så beräknas meddelanden för fakturering

För fakturering räknas endast utgående meddelanden från Azure SignalR Service. Pingmeddelanden mellan klienter och servrar ignoreras.

Meddelanden som är större än 2 KB räknas som flera meddelanden på 2 KB vartdera. Diagrammet över antal meddelanden i Azure-portalen uppdateras vart 100:e meddelande för varje hubb.

Anta som exempel att du har tre klienter och en programserver. En klient skickar ett meddelande på 4 KB som servern ska skicka till alla klienter. Antalet meddelanden är åtta: ett meddelande från tjänsten att programservern och tre meddelanden från tjänsten till klienterna. Varje meddelande räknas som två meddelanden på 2 KB vartdera.

## <a name="how-connections-are-counted"></a>Så räknas anslutningar

Det finns serveranslutningar och klientanslutningar med Azure SignalR Service. Som standard börjar varje programserver med fem inledande anslutningar per hubb och varje klient har en klientanslutning.

Det antal anslutningar som visas i Azure-portalen omfattar både serveranslutningar och klientanslutningar.

Anta som exempel att du har två programservrar och att du definierar fem hubbar i kod. Antalet servrar för serveranslutningen blir 50: 2 appservrar * 5 hubbar * 5 anslutningar per hubb.

ASP.NET SignalR beräknar serveranslutningar på ett annat sätt. Det omfattar en standardhubb utöver de hubbar som du definierar. Som standard behöver varje programserver ytterligare fem inledande serveranslutningar. Det ursprungliga anslutningsantalet för standardhubben förblir förenligt med det andra navet.

Under programserverns livstid behåller tjänsten och programservern synkroniseringsanslutningsstatus och gör justering av serveranslutningar för bättre prestanda och servicestabilitet. Så du kan se ändringar serveranslutning nummer från tid till annan.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Så räknas inkommande/utgående trafik

Skillnaden mellan inkommande och utgående trafik baseras på perspektivet för Azure SignalR Service. Trafik beräknas i byte.

## <a name="related-resources"></a>Relaterade resurser

- [Sammansättningstyper i Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR-konfiguration](/aspnet/core/signalr/configuration)
- [Json](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
