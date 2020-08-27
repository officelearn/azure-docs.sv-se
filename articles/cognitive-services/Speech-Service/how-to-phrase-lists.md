---
title: Fras listor – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig att tillhandahålla tal tjänsten med en fras lista med hjälp av `PhraseListGrammar` objektet för att förbättra tal-till-text-igenkännings resultatet.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: a71b0ba459d9363f6f479fd88ef10c8209eaf47c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918731"
---
# <a name="phrase-lists-for-speech-to-text"></a>Fras listor för tal till text

Genom att tillhandahålla röst tjänsten med en lista över fraser kan du förbättra tal igenkänningens precision. Fras listor används för att identifiera kända fraser i ljuddata, t. ex. en persons namn eller en viss plats.

Om du till exempel har ett kommando "flytta till" och ett möjligt mål för "inåt" som kan bli talade kan du lägga till en post för "flytta till inåt". Om du lägger till en fras ökar sannolikheten att när ljudet identifieras som "flytta till inåt" identifieras i stället för "flytta till".

Enstaka ord eller fullständiga fraser kan läggas till i en fras lista. Under igenkänning används en post i en fras lista om en exakt matchning för hela frasen ingår i ljudet som en separat fras. Om det inte går att hitta en exakt matchning till frasen går det inte att hitta igenkänning.

>[!Note]
> För närvarande stöder fras listor bara engelska för tal till text.

## <a name="how-to-use-phrase-lists"></a>Använda fras listor

Exemplen nedan visar hur du skapar en fras lista med hjälp av `PhraseListGrammar` objektet.

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

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Det maximala antalet fras listor som tal tjänsten använder för att matcha tal är 1024-fraser.

Du kan också ta bort fraserna som är kopplade till `PhraseListGrammar` genom att anropa Clear ().

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

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Ändringar av ett `PhraseListGrammar` objekt börjar gälla nästa igenkänning eller efter en åter anslutning till tal-tjänsten.

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om tal SDK-referens](speech-sdk.md)
