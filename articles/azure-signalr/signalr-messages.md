---
title: Meddelanden och anslutningar i Azure SignalR
description: En översikt över viktiga begrepp gällande meddelanden och anslutningar i Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352605"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Meddelande och anslutning i Azure SignalR Service

Azure SignalR Service har en faktureringsmodell baserad på antalet anslutningar och antalet meddelanden. Nedan förklaras hur meddelanden och anslutningar definieras och räknas i faktureringssyfte.

## <a name="message-formats-supported"></a>Meddelandeformat som stöds

Azure SignalR Service stöder samma format som ASP.NET Core SignalR har stöd för: [JSON](https://www.json.org/) och [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Meddelandestorlek

Azure SignalR Service har ingen storleksgräns för meddelandet.

I praktiken delas stora meddelanden upp i mindre meddelanden på högst 2 KB var och överförs som separata meddelanden. SDK:er hanterar delning och sammansättning av meddelanden. Inget utvecklararbete krävs.

Däremot har stora meddelanden en negativ inverkan på meddelandeprestandan. Använd mindre storlek på meddelandena när det är möjligt och testa dig fram till den optimala meddelandestorleken för varje användningsfall.

## <a name="how-to-count-messages-for-billing-purpose"></a>Hur räknas meddelanden i faktureringssyfte?

Vi räknar bara antalet utgående meddelanden från SignalR Service och ignorerar pingmeddelanden mellan klienter och servrar.

Meddelanden som är större än 2 KB räknas som flera meddelanden på 2 KB var. Diagrammet över antal meddelanden i Azure-portalen uppdateras vart 100:e meddelande för varje hubb.

Anta som exempel att du har tre klienter och en programserver. En klient skickar ett meddelande på 4 KB som servern ska skicka till alla klienter. Meddelandeantalet är 8: Ett meddelande från tjänsten till programservern, tre meddelanden från tjänsten till klienter och varje meddelande räknas som två meddelanden på 2 KB.

Antal meddelanden som visas i Azure-portalen är fortfarande 0, tills det överstiger 100.

## <a name="how-to-count-connections"></a>Hur räknas anslutningar?

Det finns serveranslutningar och klientanslutningar. Som standard har varje programserver fem anslutningar per hubb med SignalR Service, och varje klient har en klientanslutning med SignalR Service.

Antal anslutningar som visas i Azure-portalen omfattar både serveranslutningar och klientanslutningar.

Anta som exempel att du har två programservrar och definierar fem hubbar i koder. Antalet serveranslutningar är 50: 2 programservrar * 5 hubbar * 5 anslutningar/hubb.

ASP.NET SignalR skiljer sig vid beräkning av serveranslutningar. Den har en standardhubb utöver kunddefinierade hubbar. Varje programserver behöver 5 fler serveranslutningar som standard. Antalet anslutningar för standardhubben hålls konsekvent med andra hubbar.

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>Så räknar du inkommande trafik/utgående trafik

Inkommande/utgående är från perspektivet för SignalR Service. Trafiken beräknas i byte. Liksom meddelandeantalet har även trafiken en samplingsfrekvens. Diagrammet för inkommande/utgående i Azure-portalen uppdateras varje 100 KB per hubb.

## <a name="related-resources"></a>Relaterade resurser

- [Sammansättningstyp i Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR-konfiguration](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)