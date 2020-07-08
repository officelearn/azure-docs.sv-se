---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187144"
---
Det här exemplet förutsätter att du använder [Twilio](https://www.twilio.com/) -tjänsten för att skicka SMS-meddelanden till en mobil telefon. Azure Functions har redan stöd för Twilio via [Twilio-bindningen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)och exemplet använder funktionen.

Det första du behöver är ett Twilio-konto. Du kan skapa en kostnads fri på https://www.twilio.com/try-twilio . När du har ett konto lägger du till följande tre **appinställningar** i din Function-app.

| Namn på App-inställning | Värde Beskrivning |
| - | - |
| **TwilioAccountSid**  | SID för ditt Twilio-konto |
| **TwilioAuthToken**   | Auth-token för ditt Twilio-konto |
| **TwilioPhoneNumber** | Telefonnumret som är kopplat till ditt Twilio-konto. Detta används för att skicka SMS-meddelanden. |