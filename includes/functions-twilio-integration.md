---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "67187144"
---
Det här exemplet förutsätter att du använder [Twilio](https://www.twilio.com/) -tjänsten för att skicka SMS-meddelanden till en mobil telefon. Azure Functions har redan stöd för Twilio via [Twilio-bindningen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)och exemplet använder funktionen.

Det första du behöver är ett Twilio-konto. Du kan skapa en kostnads fri https://www.twilio.com/try-twiliopå. När du har ett konto lägger du till följande tre **appinställningar** i din Function-app.

| Namn på App-inställning | Värde Beskrivning |
| - | - |
| **TwilioAccountSid**  | SID för ditt Twilio-konto |
| **TwilioAuthToken**   | Auth-token för ditt Twilio-konto |
| **TwilioPhoneNumber** | Telefonnumret som är kopplat till ditt Twilio-konto. Detta används för att skicka SMS-meddelanden. |