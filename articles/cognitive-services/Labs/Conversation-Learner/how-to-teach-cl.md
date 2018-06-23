---
title: Hur de ska lära med konversation deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om att utbilda med konversation deltagaren.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 639fea64fc8eeb2c1f6e6240c4eb26efc68febbd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353970"
---
# <a name="how-to-teach-with-conversation-learner"></a>Hur de ska lära med konversation deltagaren 

Det här dokumentet förklarar vad signalerar konversation deltagaren är medveten om och beskriver hur den lär sig.  

Lärare kan delas upp i två steg: entitet extrahering och val av åtgärd.

## <a name="entity-extraction"></a>Entiteten extrahering

Under försättsbladen, konversation deltagaren använder [THOMAS](https://www.luis.ai) för extrahering av entiteten.  Om du är bekant med THOMAS som uppleva gäller entitet extrahering i konversationen deltagaren.

Entiteten extrahering modeller är medveten om den *innehåll* och *kontexten* inom en utterance för användaren.  Om ordet ”Seattle” har tagits med etiketten som en ort i en utterance som till exempel ”vad är väder i Seattle” är entitet extrahering kan identifiera samma innehåll (”Seattle”) som en ort i en annan utterance, till exempel ”ifyllning av Seattle”, även om den utterances är mycket annat.  Däremot om ”Carl” har tagits tolkas som ett namn i ”schemat möte med Carl” och sedan ett nytt tidigare överblivna namn kan identifieras i en liknande kontext som ”ställa in ett möte med Robin”.  Maskininlärning härleder när du ska delta i innehåll, kontext eller både och, baserat på utbildning exempel.

Entiteten extraheringen är för närvarande bara medveten om innehållet i den aktuella utterance.  Till skillnad från åtgärden markeringen (nedan) är det inte medveten om dialogrutan historik som tidigare system aktiverar, föregående användare aktiverar eller tidigare identifierade enheter.  Därför är beteendet för extrahering av entiteten ”delad” över alla utterances.  Till exempel om användaren-utterance ”jag vill Apple” finns ”Apple” märkta som entitetstypen ”frukter” i en användare utterance entitet extrahering modellen förväntar sig att den här utterance (”jag vill Apple”) bör alltid ha ”Apple” med etiketten ”frukter”.

Om entiteten extrahering inte uppför som förväntat, är här möjliga lösningar:

- Det första du försök är att lägga till fler utbildning exempel--särskilt exempel som avslöjar vanliga entitet kontext (omgivande ord) eller undantag
- Överväg att lägga till en ”förväntades” entitetsegenskap till en åtgärd om det är lämpligt.  Se kursen på förväntat entiteter för mer information.
- Även om det är möjligt att lägga till manuell bearbetning till `EntityExtractionCallback` om du vill extrahera enheter med hjälp av koden, detta är den minsta rekommenderade metoden eftersom den inte drar nytta av förbättringar i machine learning då systemet utvecklas.

## <a name="action-selection"></a>Val av åtgärd

Val av åtgärd använder ett återkommande neurala nätverk, vilket tar alla konversationen som indata.  Val av åtgärd är alltså en tillståndskänslig process som är medveten om föregående användare utterances, entiteten värden och utterances system.  

Vissa signaler naturligt föredrar learning-processen.  Med andra ord om konversationen deltagaren förklaras beslut om en åtgärd med mer ”prioriterade” signaler, kommer det; Om den kan inte används mindre ”prioriterade” signaler.

Här är en tabell som visar alla signaler i konversationen deltagaren och vilka som används av val av åtgärd.  Observera att word ordning i användaren utterances ignoreras.

Signal | Inställningar (1 = mest föredragna) | Anteckningar
--- | --- | --- 
Åtgärd i föregående Stäng | 1 | 
Enheter som finns i aktuella Stäng | 1 | 
Om detta är den första Stäng | 1 |
Exakt matchning av orden i den aktuella användaren utterance | 2 | 
Liknar betydelse ord i den aktuella användaren utterance | 3 | 
Aktivera systemåtgärder innan tidigare | 4 |
Enheter som finns i sin tur före aktuell Stäng | 4 | 
Aktivera användare utterances före aktuell | 5 | 

Observera att val av åtgärd innehållet i systemåtgärder--text, kort innehåll eller API-namnet eller beteende--endast identiteten för systemåtgärd.  Därför kan påverkar ändra innehållet i en åtgärd inte beteendet för åtgärden val av modellen.

Dessutom används Observera att innehållet/värdena för enheter som inte--bara sådana förekomst/saknas.

Om val av åtgärd inte fungerar som förväntat, är här möjliga lösningar:

- Lägg till mer train dialogrutor, särskilt dialogrutor som visar vilka signalerar val av åtgärd bör med hänsyn till.  Till exempel om val av åtgärd bör föredrar en signal över en annan, ger exempel på önskade signalen att den finns i samma tillstånd och de andra signaler variera.  Vissa sekvenser kan ta en handfull utbildning dialogrutorna för att lära dig.
- Lägg till ”krävs” och ”diskvalificera” entiteter åtgärd definitioner.  Den här gränser när åtgärder är tillgängliga och kan vara användbara för snabb affärsregler och vissa vanliga meningsfullt mönster. 

## <a name="updates-to-models"></a>Uppdateringar av modeller

När du lägger till eller redigera en entitet, åtgärder eller train-dialogrutan i Användargränssnittet för genererar detta en begäran om att träna nytt både entitet extrahering modell och åtgärden val av modellen.  Denna begäran har placerats i en kö och utbildning igen görs asynkront.  När det finns en ny modell används den från den tidpunkten och senare för entiteten extrahering och åtgärden val.  Nytt utbildning processen ofta tar cirka 5 sekunder, men det kan vara längre om modellen är komplex, eller om belastningen på Utbildningstjänsten är hög.

Eftersom utbildning görs asynkront, är det möjligt att du har gjort ändringar inte visas direkt.  Om extrahering eller åtgärd av inte uppför som förväntat baserat på ändringar som du har gjort i de senaste 5-10 sekunderna, kan detta bero på.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Standardvärden och gränser](./cl-values-and-boundaries.md)
