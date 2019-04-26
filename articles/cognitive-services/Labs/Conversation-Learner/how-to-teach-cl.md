---
title: Hur de ska lära med Konversationsdeltagare - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig att lära med Konversationsdeltagare.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8c55bb27ce5a413c5ceae22371ad61a5acf47281
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322897"
---
# <a name="how-to-teach-with-conversation-learner"></a>Så här lär du ut med Conversation Learner 

Det här dokumentet beskriver vad signalerar Konversationsdeltagare är medveten om och beskriver hur den lär sig.  

Undervisning kan delas upp i två steg: entitetextrahering och val av åtgärd.

## <a name="entity-extraction"></a>Entitetextrahering

Under försättsbladen, Konversationsdeltagare använder [LUIS](https://www.luis.ai) för entitetextrahering.  Om du är bekant med LUIS upplevelse avser entitetextrahering i Konversationsdeltagare.

Entiteten extrahering modeller är medvetna om de *innehåll* och *kontext* inom en användare-uttryck.  Om ordet ”Seattle” har fått en etikett som en stad i ett uttryck som till exempel ”vad är vädret i Seattle”, entitetextrahering kan känna igen samma innehåll (”Seattle”) som en stad i ett annat uttryck, till exempel ”ifyllning av Seattle” även om den yttranden skiljer sig mycket.  Däremot om ”Carl” har blivit erkänt som ett namn i ”schema ett möte med Carl”, och sedan ett nytt namn som tidigare kan identifieras i en liknande kontext, t.ex. ”ställa in ett möte med Robin”.  Maskininlärning härleder när du ska delta i innehållet, kontext eller båda, baserat på utbildning exempel.

Entitetextrahering har för närvarande endast om innehållet i den aktuella uttryck.  Till skillnad från åtgärdsval (nedan) är det inte medveten om dialogrutan historik som tidigare system aktiverar, tidigare användare aktiverar eller tidigare identifierade enheter.  Därför är beteendet för entitetextrahering ”delade” över alla yttranden.  Till exempel om användaren-uttryck som ”jag vill Apple” har ”äpple” märkta som entitetstypen ”frukt” i ett uttryck för användaren, entiteten extrahering modellen kommer förväntar sig att den här uttryck (”jag vill Apple”) bör alltid ha ”äpple” märkta som ”frukt”.

Om entitetextrahering inte fungerar som förväntat, är här möjliga lösningar:

- Det första du ska testa är att lägga till fler utbildning-exempel – särskilt exempel som avslöjar vanliga entitet kontext (omgivande ord) eller undantag
- Överväg att lägga till en ”förväntades entitet”-egenskap till en åtgärd som, om det är lämpligt.  Se självstudien på förväntat entiteter finns mer information.
- Det är möjligt att lägga till manuell bearbetning till `EntityExtractionCallback` för att extrahera entiteter med hjälp av kod, detta är den minst rekommenderade metoden eftersom den inte kan dra nytta av förbättringar i maskininlärning när systemet utvecklas.

## <a name="action-selection"></a>Val av åtgärd

Val av åtgärd använder ett återkommande neurala nätverk som tar alla tidigare konversationer som indata.  Val av åtgärd är alltså en tillståndskänslig process som känner av föregående användare yttranden och entitetsvärden system yttranden.  

Vissa signaler naturligt föredrar learning processen.  Med andra ord om Konversationsdeltagare förklara beslut om en åtgärd med mer ”primära” signaler, kommer det att; Om den inte använder mindre ”primära” signaler.

Här är en tabell som visar alla signaler i Konversationsdeltagare och vilka som används av val av åtgärd.  Observera att word ordning i användaren yttranden ignoreras.

Signal | Inställningar (1 = mest föredragna) | Anteckningar
--- | --- | --- 
Systemåtgärd i föregående sin tur | 1 | 
Entiteter i aktuella aktivera | 1 | 
Om detta är den första tur. | 1 |
Exakt matchning av orden i den aktuella användaren uttryck | 2 | 
Ett liknande sätt betydelse orden i den aktuella användaren uttryck | 3 | 
Aktivera systemåtgärder före tidigare | 4 |
Entiteter i sin tur före aktuella aktivera | 4 | 
Aktivera användare yttranden före aktuell | 5 | 

> [!NOTE]
> Val av åtgärd tar inte innehållet i systemåtgärder--text, kortinnehåll eller API-namn eller beteende – endast identiteten för systemåtgärd.  Därför kan påverkar ändra innehållet i en åtgärd inte beteendet för åtgärden val av modellen.
>
> Dessutom används att innehållet/värdena för entiteter inte – endast sådana närvaro/saknas.

Om val av åtgärd inte fungerar som förväntat, är här möjliga lösningar:

- Lägg till mer träna dialogrutor, särskilt dialogrutor som illustrerar vilka signaler val av åtgärd bör var uppmärksam på.  Till exempel om val av åtgärd bör föredrar en signal framför en annan, ge exempel som visar önskade signalen i samma tillstånd och de andra signaler varierande.  Vissa sekvenser kan ta en handfull utbildning dialogrutorna för att lära dig.
- Lägg till ”obligatorisk” och ”diskvalificera” entiteter för att åtgärdsdefinitioner.  Den här gränser när åtgärder är tillgängliga och kan vara användbara för uttryckliga affärsregler och vissa sunt förnuft mönster. 

## <a name="updates-to-models"></a>Uppdateringar av modeller

Varje gång du lägger till eller redigera en entitet, åtgärd eller träna dialogrutan i Användargränssnittet för genererar detta en begäran om att omskola både entitet extrahering modellen och modellen för val av åtgärd.  Den här förfrågan är placerad i kö och återkommande utbildning utförs asynkront.  Om det finns en ny modell för den används från den tidpunkten och senare för extrahering och åtgärd av.  Nytt utbildning processen ofta tar cirka 5 sekunder, men det kan vara längre om modellen är komplexa, eller om belastningen på Utbildningstjänsten är hög.

Eftersom utbildning utförs asynkront, är det möjligt att du har gjort ändringar inte visas direkt.  Om utdrag eller åtgärd av inte fungerar som förväntat baserat på ändringar som du har gjort de senaste 5 – 10 sekunderna, kan detta vara orsaken.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Standardvärden och gränser](./cl-values-and-boundaries.md)
