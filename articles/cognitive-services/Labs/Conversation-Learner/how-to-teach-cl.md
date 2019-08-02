---
title: Lär dig att lära med Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig att lära med Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: a18d4c31da4ffeefebd4bda9aa441fdfec062be9
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705267"
---
# <a name="how-to-teach-with-conversation-learner"></a>Så här lär du ut med Conversation Learner 

Det här dokumentet beskriver vilka signaler Conversation Learner är medvetna om och beskriver hur det lär sig.  

Undervisningen kan delas upp i två olika steg: enhets extrahering och val av åtgärder.

## <a name="entity-extraction"></a>Enhets extrahering

Under Conversation Learner använder [Luis](https://www.luis.ai) för enhets extrahering.  Om du är bekant med LUIS gäller den upplevelsen för enhets extrahering i Conversation Learner.

Organisationens extraherings modeller är medvetna om *innehållet* och *kontexten* i en användares uttryck.  Exempel: om ordet "Seattle" har märkts som en stad i en uttryck, till exempel "Vad är väder i Seattle", kan enhets extraheringen identifiera samma innehåll ("Seattle") som en stad i en annan uttryck, till exempel "population av Seattle", även om yttranden är väldigt olika.  Om t. ex. "Francis" har identifierats som ett namn i "schemalägga ett möte med Francis", kan ett nytt tidigare osett-namn identifieras i ett liknande sammanhang, som "Konfigurera ett möte med Robin".  Machine Learning härleds när man ska delta i innehåll, kontext eller både och utifrån utbildnings exempel.

För närvarande är enhets extrahering endast medveten om innehållet i den aktuella uttryck.  Till skillnad från val av åtgärd (nedan) är det inte medvetet om dialog historiken, till exempel tidigare system som är aktive rad, tidigare användare aktiverar eller tidigare identifierade entiteter.  Resultatet av enhets extraheringen är därför "delad" för alla yttranden.  Om t. ex. användaren uttryck "Jag vill att Apple" har "äpple" som märkts som entitetstyp "frukt" i en användares uttryck, kommer modellen för enhets extrahering att förvänta sig att denna uttryck ("Jag vill ha Apple") alltid ska ha "Apple" märkt som "frukt".

Om extrahering av enheter inte fungerar som förväntat, så är det möjligt att göra följande:

- Det första du behöver göra är att lägga till fler inlärnings exempel – särskilt exempel som avslöjar typiska entitets sammanhang (omgivande ord) eller undantag
- Överväg att lägga till en "förväntad entitet"-egenskap till en åtgärd, om det är lämpligt.  Mer information finns i självstudier om förväntade entiteter.
- Även om det är möjligt att lägga till manuell `EntityExtractionCallback` bearbetning för att extrahera entiteter med hjälp av kod, är det här den minst rekommenderade metoden eftersom den inte har nytta av förbättringar i Machine Learning som systemet är vuxen.

## <a name="action-selection"></a>Val av åtgärd

Åtgärds urvalet använder ett återkommande neurala-nätverk, som tar inmatat alla konversations historik.  Det innebär att valet av åtgärd är en tillstånds känslig process som är medveten om tidigare användar yttranden, entitetsfält och system yttranden.  

Vissa signaler prioriteras naturligt av inlärnings processen.  Om Conversation Learner kan förklara ett beslut om val av åtgärder med hjälp av "fler prioriterade" signaler, är det med andra ord. om den inte gör det använder den "mindre prioriterade" signaler.

Här är en tabell som visar alla signaler i Conversation Learner och vilka som används av åtgärds urvalet.  Observera att Word-ordningen i User yttranden ignoreras.

Signal | Preferens (1 = mest prioriterad) | Anteckningar
--- | --- | --- 
System åtgärd i föregående turn | 1 | 
Entiteter som finns i aktuell tur | 1 | 
Om det här är första turn | 1 |
Exakt matchning av ord i den aktuella användarens uttryck | 2 | 
Liknande ord i aktuell användar uttryck | 3 | 
System åtgärder före föregående aktivering | 4 |
Entiteter som finns i svänger före aktuell turn | 4 | 
Användarens yttranden före aktuell turn | 5 | 

> [!NOTE]
> Val av åtgärd tar inte innehållet i system åtgärder, text, kort innehåll eller API-namn eller beteende – endast systemets identitet.  Det innebär att om du ändrar innehållet i en åtgärd ändras inte beteendet för åtgärds markerings modellen.
>
> Vidare, att innehållet/värdena för entiteter inte används – endast deras närvaro/frånvaro.

Om åtgärds urvalet inte fungerar som förväntat, så är det möjligt att göra följande:

- Lägg till fler träna-dialog rutor, särskilt dialog rutor som illustrerar vilka signaler som åtgärds valet ska betala till.  Om åtgärds valet till exempel bör föredra en signal över en annan, ska du ge exempel som visar den önskade signalen vara i samma tillstånd och den andra signalerar.  Vissa sekvenser kan vara ett fåtal för att lära dig mer.
- Lägg till "obligatoriska" och "diskvalificerande" entiteter i åtgärds definitioner.  Detta begränsar när åtgärder är tillgängliga och kan vara användbara för att uttrycka affärs regler och några vanliga menings mönster. 

## <a name="updates-to-models"></a>Uppdateringar av modeller

När du lägger till eller redigerar dialog rutan entitet, åtgärd eller träna i användar gränssnittet genererar detta en begäran om att träna om både hierarkin för enhets extrahering och åtgärds urvals modell.  Den här begäran placeras i en kö och omträningen görs asynkront.  När en ny modell är tillgänglig används den från och med den här punkten för extrahering av enheter och val av åtgärder.  Den här omskolningen tar ofta cirka 5 sekunder, men kan vara längre om modellen är komplex eller om belastningen på utbildnings tjänsten är hög.

Eftersom utbildning görs asynkront, är det möjligt att redigeringar som du har gjort inte återspeglas direkt.  Om extrahering av enheter eller åtgärds val inte fungerar som förväntat baserat på de ändringar du har gjort under de senaste 5-10 sekunderna kan detta vara orsaken.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Standardvärden och gränser](./cl-values-and-boundaries.md)
