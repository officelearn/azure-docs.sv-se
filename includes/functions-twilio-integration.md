---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132540"
---
Det här exemplet är att använda den [Twilio](https://www.twilio.com/) -tjänsten för att skicka SMS-meddelanden till en mobiltelefon. Azure Functions redan har stöd för Twilio via den [Twilio-bindning](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), och i exemplet används funktionen.

Det första du behöver är ett Twilio-konto. Du kan skapa en kostnadsfri på https://www.twilio.com/try-twilio. När du har ett konto kan du lägga till följande tre **appinställningar** till din funktionsapp.

| App inställningsnamn | Värdebeskrivning |
| - | - |
| **TwilioAccountSid**  | SID för Twilio-konto |
| **TwilioAuthToken**   | Auth-token för ditt Twilio-konto |
| **TwilioPhoneNumber** | Telefonnummer som är associerat med ditt Twilio-konto. Detta används för att skicka SMS-meddelanden. |