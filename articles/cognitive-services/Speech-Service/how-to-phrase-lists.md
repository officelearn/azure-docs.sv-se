---
title: Fraslistor - Taltjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anger taltjänsten `PhraseListGrammar` med en fraslista med hjälp av objektet för att förbättra tal-till-text-igenkänningsresultaten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5a21358edae4c61f35993770c22634da9ac83633
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401971"
---
# <a name="phrase-lists-for-speech-to-text"></a>Fraslistor för tal till text

Genom att ge taltjänsten en lista med fraser kan du förbättra taligenkänningens noggrannhet. Fraslistor används för att identifiera kända fraser i ljuddata, till exempel en persons namn eller en viss plats.

Om du till exempel har kommandot "Flytta till" och ett möjligt mål för "Ward" som kan talas kan du lägga till en post i "Flytta till församling". Om du lägger till en fras ökar sannolikheten för att när ljudet känns igen känns det igen att "Flytta till Ward" känns igen i stället för "Flytta mot".

Enstaka ord eller fullständiga fraser kan läggas till i en fraslista. Under igenkänningen används en post i en fraslista om en exakt matchning för hela frasen inkluderas i ljudet som en separat fras. Om det inte går att hitta en exakt matchning till frasen assisteras inte igenkänningen.

>[!Note]
> För närvarande stöder fraslistor endast engelska för tal-till-text.

## <a name="how-to-use-phrase-lists"></a>Så här använder du fraslistor

Exemplen nedan visar hur du skapar `PhraseListGrammar` en fraslista med objektet.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Det maximala antalet fraslistor som taltjänsten ska använda för att matcha tal är 1024 fraser.

Du kan också ta bort `PhraseListGrammar` de fraser som är associerade med genom att anropa clear().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Ändringar i `PhraseListGrammar` ett objekt börjar gälla på nästa igenkänning eller efter en återanslutning till taltjänsten.

## <a name="next-steps"></a>Nästa steg

* [Referensdokumentation för Tal SDK](speech-sdk.md)
