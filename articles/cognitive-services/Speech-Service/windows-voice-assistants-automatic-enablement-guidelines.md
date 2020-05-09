---
title: Sekretess rikt linjer för röst assistenter i Windows
titleSuffix: Azure Cognitive Services
description: Anvisningarna för att aktivera röst aktivering för en röst agent som standard.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997517"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Sekretess rikt linjer för röst assistenter i Windows

Det är viktigt att användarna får tydlig information om hur deras röst data samlas in och används och är viktiga att de får kontroll över om och hur den här samlingen sker. De här kärn ansiktena för sekretess – *avslöjande* och *medgivande* – är särskilt viktiga för röst assistenter som är integrerade i Windows och som alltid lyssnar på användningen.

Utvecklare som skapar röst assistenter i Windows måste ta med tydliga användar gränssnitts element i sina program som återspeglar lyssnar funktioner i assistenten.

> [!NOTE]
> Underlåtenhet att lämna ut korrekt information och medgivande för ett assistent program, inklusive efter program uppdateringar, kan leda till att assistenten blir otillgänglig för röst aktivering tills integritets problem har lösts. 

## <a name="minimum-requirements-for-feature-inclusion"></a>Minimi krav för funktions inkludering

Windows-användare kan se och kontrol lera tillgängligheten för sina assistent **`Settings > Privacy > Voice activation`** program i.

 > [!div class="mx-imgBorder"]
 > [![Sekretess – app-List](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "En sekretess inställnings post för Windows Voice Activation för ett assistent program")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

För att bli berättigad till att inkluderas i den här listan måste ett program:

1. Berätta tydligt om sina användare att de lyssnar efter ett nyckelord, även om programmet inte körs och vad nyckelordet är
1. Ta med en beskrivning av hur en användares röst data ska användas, inklusive en länk eller hänvisning till relevanta sekretess principer
1. Informera användare som, förutom alla inställningar i appar, kan visa och ändra sina sekretess alternativ i, om **`Settings > Privacy > Voice activation`** du vill, inklusive en protokoll länk till `ms-settings:privacy-voiceactivation` för direkt åtkomst

Efter att ha följt dessa krav och godkänt godkännande från Microsoft visas ett assistent program i listan över röst aktiverings appar när den har registrerats `Windows.ApplicationModel.ConversationalAgent` med API: erna och användarna kommer att kunna bevilja medgivande till programmet för nyckelords aktivering. Som standard är `Off` båda dessa inställningar och kräver att användaren manuellt besöker sidan Inställningar för att aktivera.

> [!NOTE]
> I samtliga fall kräver röst aktiverings behörigheten mikrofon behörighet. Om ett assistent program inte har åtkomst till mikrofonen är det inte berättigat till röst aktivering och kommer att visas i sekretess inställningarna för röst aktivering i inaktiverat tillstånd.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Ytterligare krav för att inkludera i mikrofonen

Assistent författare som vill göra det enklare och smidigare för användarna att välja röst aktivering kan göra detta genom att uppfylla ytterligare krav för dem ovan. Efter att ha uppfyllt dessa är en assistents program standard inställningen för enhets låst röst `On` aktivering som standard en gång (och bara en gång) ger programmet åtkomst. Detta eliminerar behovet av en extra resa till inställningarna innan röst aktiveringen av en assistent aktive ras.

De ytterligare kraven är att ett assistent program måste:

1. **Innan** du tillfrågas om mikrofon godkännande (till exempel med `AppCapability.RequestAccessAsync` hjälp av API: et) ger du en synlig indikation på användaren att assistent programmet vill lyssna på en användares röst för ett nyckelord, även om programmet inte körs och vill att användarens samtycke
2. Ta med all relevant information om principer för data användning och sekretess **innan** du begär åtkomst till mikrofonen eller via `Windows.ApplicationModel.ConversationalAgent` API: erna
3. Undvik alla direktiv eller inledande orda lydelse (till exempel "Klicka på Ja i följande fråga") i upplevelse flödet, Stäng av ljud tagnings beteende och begära behörighet

När dessa krav är uppfyllda visas ett berättigat assistent program i listan över program som är berättigade till röst aktivering i ett `enabled` tillstånd när mikrofon åtkomst beviljas.

> [!NOTE]
> Röst aktivering över lås är inte kvalificerad för automatisk aktivering med mikrofon åtkomst och kommer fortfarande att kräva att en användare besöker sidan för röst aktiverings sekretess för att aktivera åtkomst över lås till en assistent.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om metod tips för röst assistenter i Windows](windows-voice-assistants-best-practices.md)