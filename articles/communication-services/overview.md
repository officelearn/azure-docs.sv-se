---
title: Vad är Azure Communication Services?
description: Lär dig hur Azure Communication Services hjälper dig att utveckla omfattande användar upplevelser med real tids kommunikation.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a006ece278e4ec750040bde5bd4b7a6144c9e720
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948566"
---
# <a name="what-is-azure-communication-services"></a>Vad är Azure Communication Services?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Med Azure Communication Services kan du enkelt lägga till funktioner för multimedia, video och telefoni för kommunikation i real tid till dina program. Klient biblioteken för kommunikations tjänster låter dig också lägga till chatt-och SMS-funktioner i kommunikations lösningarna.

Du kan använda kommunikations tjänster för röst-, video-, text-och data kommunikation i olika scenarier:

- Webb läsar-till-webbläsare, webb läsar-till-app och kommunikation mellan appar
- Användare som interagerar med robotar eller andra tjänster
- Användare och robotar som interagerar över det offentliga växlade telefoni nätverket

Blandade scenarier stöds. Ett kommunikations tjänst program kan till exempel ha användare som talar från webbläsare och traditionella telefoni enheter samtidigt. Kommunikations tjänsterna kan också kombineras med Azure Bot Service för att bygga bot-drivna interaktiva Voice Response-system (IVR).

## <a name="common-scenarios"></a>Vanliga scenarier

Följande resurser är en bra plats för att börja om du är nybörjare på Azure Communication Services:
<br>

| Resurs                               |Beskrivning                           |
|---                                    |---                                   |
|**[Skapa en kommunikations tjänst resurs](./quickstarts/create-communication-resource.md)**|Du kan börja använda Azure Communication Services med hjälp av klient biblioteket Azure Portal eller kommunikations tjänst administration för att etablera din första kommunikations tjänst resurs. När du har resurs anslutnings strängen för kommunikations tjänster kan du etablera dina första åtkomsttoken för användare.|
|**[Skapa din första åtkomsttoken för användare](./quickstarts/access-tokens.md)**|Token för användar åtkomst används för att autentisera dina tjänster mot Azure Communication Services-resursen. Dessa tokens tillhandahålls och återutfärdas med administrations klient biblioteket för kommunikations tjänster.|
|**[Hämta ett telefonnummer](./quickstarts/telephony-sms/get-phone-number.md)**|Du kan använda Azure Communication Services för att etablera och frigöra telefonnummer. Dessa telefonnummer kan användas för att initiera utgående samtal och bygga SMS-kommunikations lösningar.|
|**[Skicka ett SMS från din app](./quickstarts/telephony-sms/send.md)**|Med klient biblioteket för Azure Communication Services SMS kan du skicka och ta emot SMS-meddelanden från .NET-och JavaScript-program.|
|**[Kom igång med röst-och video samtal](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Med Azure Communication Services kan du lägga till röst-och video samtal till dina appar med hjälp av det anropande klient biblioteket. Det här biblioteket drivs av WebRTC och gör att du kan upprätta peer-to-peer, multimedia, real tids kommunikation i dina program.|
|**[Kom igång med chatt](./quickstarts/chat/get-started.md)**|Klient biblioteket för Azure Communication Services Chat kan användas för att integrera chatt i real tid i dina program.|


## <a name="samples"></a>Exempel

I följande exempel demonstreras slut punkt till slut punkt för klient biblioteken för Azure Communication Services. Du kan använda dessa exempel för att starta dina egna kommunikations tjänst lösningar.
<br>

| Exempel namn                               | Beskrivning                           |
|---                                    |---                                   |
|**[Gruppen som anropar ett hjälte exempel](./samples/calling-hero-sample.md)**|Se hur kommunikations tjänstens klient bibliotek kan användas för att bygga en grupp som anropar en funktion.|
|**[Hjälte-exemplet för grupp Chat](./samples/chat-hero-sample.md)**|Se hur kommunikations tjänstens klient bibliotek kan användas för att bygga en grupps Chat-upplevelse.|


## <a name="platforms-and-client-libraries"></a>Plattformar och klient bibliotek

Följande resurser hjälper dig att lära dig mer om klient biblioteken för Azure Communication Services:

| Resurs                               | Beskrivning                           |
|---                                    |---                                   |
|**[Klient bibliotek och REST-API: er](./concepts/sdk-options.md)**|Azure Communication Services-funktionerna organiseras konceptuellt i sex områden som representeras av ett klient bibliotek. Du kan bestämma vilka klient bibliotek som ska användas utifrån dina kommunikations behov i real tid.|
|**[Översikt över samtals klient bibliotek](./concepts/voice-video-calling/calling-sdk-features.md)**|Granska översikt över kommunikations tjänsterna som anropar klient biblioteket.|
|**[Översikt över Chat-klient bibliotek](./concepts/chat/sdk-features.md)**|Läs översikt över kommunikations tjänstens Chat-klient bibliotek.|
|**[Översikt över SMS-klient bibliotek](./concepts/telephony-sms/sdk-features.md)**|Läs översikt över kommunikations tjänstens SMS-klientcertifikat.|

## <a name="compare-azure-communication-services"></a>Jämför Azure Communication Services

Det finns två andra Microsoft-kommunikationsprodukter som du kan överväga att inte direkt samverka med kommunikations tjänster för tillfället:

 - [Microsoft Graph API: er för moln kommunikation](https://docs.microsoft.com/graph/cloud-communications-concept-overview) kan organisationer bygga kommunikations upplevelser som är kopplade till Azure Active Directory användare med M365-licenser. Detta är perfekt för program som är knutna till Azure Active Directory eller där du vill utöka produktivitets upplevelser i Microsoft Teams. Det finns också API: er för att bygga program och anpassningar i [Teams upplevelsen.](https://docs.microsoft.com/microsoftteams/platform/?view=msteams-client-js-latest&preserve-view=true)

 - [Azure PlayFab-parten](https://docs.microsoft.com/gaming/playfab/features/multiplayer/networking/) fören klar att lägga till samtal med låg latens och data kommunikation i spel. Även om du kan spela spel och nätverks system med kommunikations tjänster, är PlayFab ett skräddarsyt alternativ och kostnads fritt på Xbox.


## <a name="next-steps"></a>Efterföljande moment

 - [Skapa en kommunikations tjänst resurs](./quickstarts/create-communication-resource.md)
