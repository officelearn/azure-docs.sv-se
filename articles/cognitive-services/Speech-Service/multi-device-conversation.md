---
title: Konversation med flera enheter (förhandsgranskning) – taltjänst
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: b3802e66b0ba5a68c898e69ec64b01edce1541c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371366"
---
# <a name="what-is-multi-device-conversation-preview"></a>Vad är konversation med flera enheter (förhandsgranskning)?

**Konversation med flera enheter** gör det enkelt att skapa en tal- eller textkonversation mellan flera klienter och samordna de meddelanden som skickas mellan dem.

Med **konversation med flera enheter**kan du:

- Anslut flera klienter till samma konversation och hantera sändning och mottagning av meddelanden mellan dem.
- Transkribera enkelt ljud från varje klient och skicka transkriptionen till de andra, med valfri översättning.
- Skicka enkelt textmeddelanden mellan klienter, med valfri översättning.

Du kan skapa en funktion eller lösning som fungerar på en rad enheter. Varje enhet kan självständigt skicka meddelanden (antingen transkriptioner av ljud eller snabbmeddelanden) till alla andra enheter.

Medan [**konversationsavskription**](conversation-transcription.md) fungerar på en enda enhet med en flerkanalig mikrofonmatris, är **konversation med flera enheter** lämplig för scenarier med flera enheter, var och en med en enda mikrofon.

>[!IMPORTANT]
> Konversationer med flera enheter stöder **inte** att skicka ljudfiler mellan klienter: endast transkription och/eller översättning.

## <a name="key-features"></a>Huvudfunktioner

- **Transkription** i realtid – Alla får en utskrift av konversationen, så att de kan följa med texten i realtid eller spara den till senare.
- **Översättning i realtid** – Med fler än 60 [språk som stöds](language-support.md#text-languages) för textöversättning kan användare översätta konversationen till önskade språk.
- **Läsbara transkriptioner** – Transkriptionen och översättningen är lätta att följa, med skiljetecken och meningsbrytningar.
- **Röst- eller textinmatning** – Varje användare kan tala eller skriva på sin egen enhet, beroende på vilka språkstödsfunktioner som är aktiverade för deltagarens valda språk. Se [Språkstöd](language-support.md#speech-to-text).
- **Meddelanderelä** - Konversationstjänsten för flera enheter distribuerar meddelanden som skickas av en klient till alla andra på det eller de språk som de själva väljer.
- **Meddelandeidentifiering** – Alla meddelanden som användarna får i konversationen kommer att taggas med smeknamnet på den användare som skickade det.

## <a name="use-cases"></a>Användningsfall

### <a name="lightweight-conversations"></a>Lätta konversationer

Det är enkelt att skapa och delta i en konversation. En användare kommer att fungera som "värd" och skapa en konversation, som genererar en slumpmässig fem bokstäver konversation kod och en QR-kod. Alla andra användare kan delta i konversationen genom att skriva in konversationskoden eller skanna QR-koden. 

Eftersom användare går med via konversationskoden och inte behöver dela kontaktinformation är det enkelt att skapa snabba konversationer på plats.

### <a name="inclusive-meetings"></a>Inkluderande möten

Transkription och översättning i realtid kan bidra till att göra konversationer tillgängliga för personer som talar olika språk och/eller är döva eller hörselskadade. Varje person kan också delta aktivt i konversationen genom att tala sitt språk eller skicka snabbmeddelanden.

### <a name="presentations"></a>Presentationer

Du kan också tillhandahålla bildtexter för presentationer och föreläsningar både på skärmen och på åhörarnas egna enheter. När publiken har anslutit sig till konversationskoden kan de se avskriften på sitt önskade språk på sin egen enhet.

> [!NOTE]
> Om du vill se ett exempel kan du läsa [Presentationsöversättare](https://www.microsoft.com/translator/apps/presentation-translator/), ett PowerPoint-tillägg som använder konversationstjänsten för flera enheter. Du kan ladda ned den [här](https://www.microsoft.com/download/details.aspx?id=55024).

## <a name="how-it-works"></a>Hur det fungerar

Alla klienter använder Tal-SDK för att skapa eller delta i en konversation. Tal-SDK interagerar med konversationstjänsten för flera enheter, som hanterar konversationens livstid, inklusive listan över deltagare, varje klients valda språk och meddelanden som skickas.  

Varje klient kan skicka ljud eller snabbmeddelanden. Tjänsten använder taligenkänning för att konvertera ljud till text och skicka snabbmeddelanden som de är. Om klienter väljer olika språk översätter tjänsten alla meddelanden till det angivna språket för varje klient.

![Översiktsdiagram för konversationer med flera enheter](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Översikt över konversation, värd och deltagare

En **konversation** är en session som en användare startar för de andra deltagande användarna att gå med i. Alla klienter ansluter till konversationen med hjälp av **konversationskoden**med fem bokstäver .

Varje konversation skapar metadata som innehåller:
-    Tidsstämplar för när konversationen startade och avslutades
-    Lista över alla deltagare i konversationen, som innehåller varje användares valda smeknamn och primära språk för tal- eller textinmatning.


Det finns två typer av användare i en konversation: **värd** och **deltagare**.

Värden **host** är den användare som startar en konversation och som fungerar som administratör för den konversationen.
- Varje konversation kan bara ha en värd
- Värden måste vara ansluten till konversationen under hela konversationen. Om värden lämnar konversationen avslutas konversationen för alla andra deltagare.
- Värden har några extra kontroller för att hantera konversationen: 
    - Lås konversationen – förhindra att ytterligare deltagare går med
    - Stäng av ljudet för alla deltagare – hindra andra deltagare från att skicka meddelanden till konversationen, oavsett om de transkriberas från tal eller snabbmeddelanden
    - Stänga av ljudet för enskilda deltagare
    - Slå på ljudet för alla deltagare
    - Slå på ljudet för enskilda deltagare

En **deltagare** är en användare som ansluter till en konversation.
- En deltagare kan när som helst lämna och återansluta samma konversation, utan att avsluta konversationen för andra deltagare.
- Deltagarna kan inte låsa konversationen eller stänga av/slå på ljudet för andra

> [!NOTE]
> Varje konversation kan ha upp till 100 deltagare, varav 10 kan tala samtidigt vid varje given tidpunkt.

## <a name="language-support"></a>Stöd för språk

När du skapar eller går med i en konversation måste varje användare välja ett **primärt språk:** det språk som de ska tala och skicka snabbmeddelanden på, och även det språk de kommer att se andra användares meddelanden.

Det finns två typer av språk: **tal-till-text** och **endast text:**
- Om användaren väljer ett **tal-till-text-språk** som sitt primära språk kan han eller hon använda både tal- och textinmatning i konversationen.

- Om användaren väljer ett **språk med endast text** kan han eller hon bara använda textinmatning och skicka snabbmeddelanden i konversationen. Språk med enbart text är de språk som stöds för textöversättning, men inte tal-till-text. Du kan se tillgängliga språk på [språkstödsidan.](supported-languages.md)

Förutom sitt primära språk kan varje deltagare också ange ytterligare språk för att översätta konversationen.

Nedan följer en sammanfattning av vad användaren kommer att kunna göra i en konversation med flera enheter, baserat på deras valda primära språk.


| Vad användaren kan göra i konversationen | Tal till text | Endast text |
|-----------------------------------|----------------|------|
| Använda talinmatning | ✔️ | ❌ |
| Skicka snabbmeddelanden | ✔️ | ✔️ |
| Översätt konversationen | ✔️ | ✔️ |

> [!NOTE]
> Listor över tillgängliga tal-till-text- och textöversättningsspråk finns [i språk som stöds](supported-languages.md).



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översätt konversationer i realtid](quickstarts/multi-device-conversation.md)
