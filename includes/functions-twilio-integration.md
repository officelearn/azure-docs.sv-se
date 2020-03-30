---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187144"
---
Det här exemplet innebär att du använder [Tjänsten Twilio](https://www.twilio.com/) för att skicka SMS-meddelanden till en mobiltelefon. Azure Functions har redan stöd för Twilio via [Twilio-bindningen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)och exemplet använder den funktionen.

Det första du behöver är ett Twilio-konto. Du kan skapa https://www.twilio.com/try-twilioen gratis på . När du har ett konto lägger du till följande tre **appinställningar** i funktionsappen.

| Namn på appinställning | Beskrivning av värde |
| - | - |
| **TwilioAccountSid**  | SID för ditt Twilio-konto |
| **TwilioAuthToken (svenska)**   | Auth-token för ditt Twilio-konto |
| **TwilioPhoneAntal** | Telefonnumret som är kopplat till ditt Twilio-konto. Detta används för att skicka SMS-meddelanden. |