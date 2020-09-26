---
title: Översikt över SMS-klient bibliotek för Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Innehåller en översikt över klient biblioteket för SMS och dess erbjudanden.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4077bbe0a98f6b7788af9b6c44f73abbc936c6aa
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332247"
---
# <a name="sms-client-library-overview"></a>Översikt över klientbiblioteket för SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services SMS-klient bibliotek kan användas för att lägga till SMS-meddelanden i dina program.

## <a name="sms-client-library-capabilities"></a>Funktioner för SMS-klient bibliotek

I följande lista visas en uppsättning funktioner som för närvarande är tillgängliga i våra klient bibliotek.

| Grupp med funktioner | Kapacitet                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Kärn funktioner | Skicka och ta emot SMS-meddelanden </br> *Unicode-emojis stöds*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ta emot leverans rapporter för skickade meddelanden                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Alla teckenuppsättningar (språk-/Unicode-stöd)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Stöd för långa meddelanden (upp till 2048 tecken)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatisk sammanfogning av långa meddelanden                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Händelser            | Använd Event Grid för att konfigurera Webhooks för att ta emot inkommande meddelanden och leverans rapporter | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefonnummer      | Avgiftsfritt nummer                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regler        | Avanmäla hantering                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Övervakning        | Övervaka användning för skickade och mottagna meddelanden                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN-anrop      | Lägg till PSTN-anropande funktioner i ditt SMS-aktiverade avgiftsfria nummer (privat för hands version)                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att skicka SMS](../../quickstarts/telephony-sms/send.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med allmänna [SMS-koncept](../telephony-sms/concepts.md)
- Hämta ett SMS-kompatibelt [telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Planera SMS-lösningen](../telephony-sms/plan-solution.md)
