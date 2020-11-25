---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027962"
---
Det här exemplet förutsätter att du använder [Twilio](https://www.twilio.com/) -tjänsten för att skicka SMS-meddelanden till en mobil telefon. Azure Functions har redan stöd för Twilio via [Twilio-bindningen](../articles/azure-functions/functions-bindings-twilio.md)och exemplet använder funktionen.

Det första du behöver är ett Twilio-konto. Du kan skapa en kostnads fri på https://www.twilio.com/try-twilio . När du har ett konto lägger du till följande tre **appinställningar** i din Function-app.

| Namn på App-inställning | Värde Beskrivning |
| - | - |
| **TwilioAccountSid**  | SID för ditt Twilio-konto |
| **TwilioAuthToken**   | Auth-token för ditt Twilio-konto |
| **TwilioPhoneNumber** | Telefonnumret som är kopplat till ditt Twilio-konto. Detta används för att skicka SMS-meddelanden. |