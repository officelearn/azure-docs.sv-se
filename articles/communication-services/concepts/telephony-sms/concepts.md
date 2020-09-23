---
title: SMS-koncept i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om SMS-koncept för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947921"
---
# <a name="sms-concepts"></a>SMS-begrepp

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Med Azure Communication Services kan du skicka och ta emot SMS-textmeddelanden med hjälp av kommunikations tjänsterna SMS-klientcertifikat. Dessa klient bibliotek kan användas för att stödja kund tjänst scenarier, påminnelser för avtalade tider, tvåfaktorautentisering och andra real tids kommunikations behov. Med kommunikations tjänster i SMS kan du på ett tillförlitligt sätt skicka meddelanden samtidigt som du kan exponera leverans och svars takt insikter kring dina kampanjer.

Viktiga funktioner i Azure Communication Services SMS-klient bibliotek är:

-  **Enkel** installations miljö för att lägga till SMS-funktioner i dina program.
- **Hög hastighets** meddelande support över avgiftsfria nummer för A2P (program till person) i USA.
- **Dubbelriktade** konversationer som stöder scenarier som kund support, aviseringar och påminnelser om avtalade tider.
- **Tillförlitlig leverans** med rapporter om leverans i real tid för meddelanden som skickas från ditt program.
- **Analys** för att spåra användnings mönster och kund engagemang.
- Hanterings stöd för att automatiskt identifiera och respektera opt- **out** för avgiftsfritt nummer. Kommunikations tjänsterna identifierar stopp-och START meddelanden och skickar följande standard svar till slutanvändarna: 
  - STOP- *"du har avbrutit prenumerationen på meddelanden från det här numret. Svara börjar prenumerera på prenumerationen. "*
  - START- *"du har prenumererat på meddelanden igen från det här numret. Svara Avbryt om du vill avbryta prenumerationen. "*
  - STOPPA och starta-meddelanden kommer att vidarebefordras tillbaka till dig. Azure Communication Services uppmuntrar dig att övervaka och implementera de här alternativen för att se till att inga ytterligare meddelanden skickas till mottagare som har valt ut ur din kommunikation.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med att skicka SMS](../../quickstarts/telephony-sms/send.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med SMS- [klient biblioteket](../telephony-sms/sdk-features.md)
- Hämta ett SMS-kompatibelt [telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Planera SMS-lösningen](../telephony-sms/plan-solution.md)
