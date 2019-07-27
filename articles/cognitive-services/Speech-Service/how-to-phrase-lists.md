---
title: Fras listor – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig att tillhandahålla tal tjänsterna med en fras lista med hjälp `PhraseListGrammar` av objektet för att förbättra tal-till-text-igenkännings resultatet.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562780"
---
# <a name="phrase-lists-for-speech-to-text"></a>Fras listor för tal till text

Genom att tillhandahålla tal tjänster med en fras lista kan du förbättra tal igenkänningens precision. Fras listor används för att identifiera kända fraser i ljuddata, t. ex. en persons namn eller en viss plats.

Om du till exempel har ett kommando "flytta till" och ett möjligt mål för "inåt" som kan bli talade kan du lägga till en post för "flytta till inåt". Om du lägger till en fras ökar sannolikheten att när ljudet identifieras som "flytta till inåt" identifieras i stället för "flytta till".

Enstaka ord eller fullständiga fraser kan läggas till i en fras lista. Under igenkänning används en post i en fras lista om en exakt matchning ingår i ljudet. Skapa i föregående exempel, om fras listan innehåller "flytta till inåt", och frasen som fångats är "övergår mot långsamt", blir igenkännings resultatet "flytta till inåt långsamt".

>[!Note]
> För närvarande stöder fras listor bara engelska för tal till text.

## <a name="how-to-use-phrase-lists"></a>Använda fras listor

Exemplen nedan visar hur du skapar en fras lista med hjälp `PhraseListGrammar` av objektet.

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
> Det maximala antalet fras listor som tal tjänsten använder för att matcha tal är 1024-fraser.

Du kan också ta bort fraserna som är `PhraseListGrammar` kopplade till genom att anropa Clear ().

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
> Ändringar i ett `PhraseListGrammar` objekt börjar gälla vid nästa igenkänning eller efter en åter anslutning till tal tjänsterna.

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om tal SDK-referens](speech-sdk.md)
