---
title: Meddelanden och anslutningar i Azure SignalR
description: En översikt över viktiga begrepp gällande meddelanden och anslutningar i Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812872"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Meddelande och anslutning i Azure SignalR Service

Azure SignalR Service har en faktureringsmodell baserad på antalet anslutningar och antalet meddelanden. Nedan förklaras hur meddelanden och anslutningar definieras och räknas i faktureringssyfte.

## <a name="message-formats-supported"></a>Meddelandeformat som stöds

Azure SignalR Service stöder samma format som ASP.NET Core SignalR har stöd för: [JSON](https://www.json.org/) och [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Meddelandestorlek

Azure SignalR Service har ingen storleksgräns för meddelandet.

I praktiken delas stora meddelanden upp i mindre meddelanden på högst 2 KB var och överförs som separata meddelanden. Delning och sammansättning av meddelanden hanteras av SDK:er. Inget utvecklararbete krävs.

Däremot har stora meddelanden en negativ inverkan på meddelandeprestandan. Använd mindre storlek på meddelandena när det är möjligt och testa dig fram till den optimala meddelandestorleken för varje användningsfall.

## <a name="how-to-count-messages-for-billing-purpose"></a>Hur räknas meddelanden i faktureringssyfte?

Vi räknar bara antalet utgående meddelanden från SignalR Service och ignorerar pingmeddelanden mellan klienter och servrar.

Meddelanden som är större än 2 KB räknas som flera meddelanden på 2 KB var. Diagrammet över antal meddelanden i Azure-portalen uppdateras vart 100:e meddelande för varje hubb.

Exempelvis kan en användare ha 3 klienter och 1 programserver. En klient skickar ett meddelande på 4 KB som servern ska skicka till alla klienter. Meddelandeantalet blir 8: 1 meddelande från tjänsten till programservern, 3 meddelanden från tjänsten till klienter och varje meddelande räknas som 2 meddelanden på 2 KB.

Antal meddelanden som visas i Azure-portalen är fortfarande 0, tills det överstiger 100.

## <a name="how-to-count-connections"></a>Hur räknas anslutningar?

Det finns serveranslutningar och klientanslutningar. Som standard har varje programserver 5 anslutningar per hubb med SignalR Service och varje klient har 1 klientanslutning med SignalR Service.

Antal anslutningar som visas i Azure-portalen omfattar både serveranslutningar och klientanslutningar.

Exempelvis kan en användare ha två programservrar och definiera 5 hubbar i koder. Antalet serveranslutningar som visas i Azure-portalen kommer att 2 appservrar * 5 hubbar * 5 anslutningar/hubb = 50 serveranslutningar.

## <a name="related-resources"></a>Relaterade resurser

- [ASP.NET Core SignalR-konfiguration](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)