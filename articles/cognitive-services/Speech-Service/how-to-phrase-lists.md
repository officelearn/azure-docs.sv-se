---
title: Fras listor – Speech Services
titlesuffix: Azure Cognitive Services
description: Lär dig hur du förmedling tal med en fras listan med hjälp av den `PhraseListGrammar` objekt för att förbättra resultat tal till text.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: 576d3c4a70c8870a31bc352b9f7723d2c2e69854
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025811"
---
# <a name="phrase-lists-for-speech-to-text"></a>Fras listor för tal till text

Genom att tillhandahålla Taltjänster med en fras-lista, kan du förbättra taligenkänning. Fras listor för att identifiera kända fraser i ljuddata, t.ex. ett namn eller en specifik plats.

Till exempel om du har ett kommando ”flytta till” och ”Ward” som kan läsas som möjligt mål du kan lägga till en post för ”Flytta till Ward”. Att lägga till en fras ökar sannolikheten som när ljudet känns att ”flytta till Ward” tolkas i stället för ”steg mot”.

Enstaka ord eller fraser som fullständig kan läggas till i en lista med frasen. Under erkännande används en post i en lista med frasen om en exakt matchning som ingår i ljuduppspelningen. Utifrån föregående exempel om listan frasen innehåller ”flytta till Ward” och frasen avbildas är ”flytta mot långsamt” så att igenkänningsresultatet kommer att vara ”flytta till Ward långsamt”.

## <a name="how-to-use-phrase-lists"></a>Hur du använder frasen visar en lista över

I exemplen nedan visar hur du skapar en fras listan med hjälp av den `PhraseListGrammar` objekt.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Det maximala antalet frasen listor som Speech-tjänsten använder för att matcha tal är 1024.

Du kan även radera fraser som är associerade med den `PhraseListGrammar` genom att anropa Rensa().

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> Ändringar i en `PhraseListGrammar` objekt gör påverkar om nästa erkännande eller efter återanslutning till Speech Services.

## <a name="next-steps"></a>Nästa steg

* [Tal SDK referensdokumentation](speech-sdk.md)
