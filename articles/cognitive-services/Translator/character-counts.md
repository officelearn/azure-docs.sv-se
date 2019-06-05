---
title: Tecken antal - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Hur räknas Translator Text API tecken.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: eccd0c47c1bfc071f43a6329406cd14f704d019e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514295"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hur Translator Text API räknar tecken

Translator Text API räknas varje Unicode-kodpunkt av indatatexten som ett tecken. Varje översättning av en text till ett språk räknas som en separat översättning, även om en begäran har gjorts i en enda API anrop översattes till flera språk. Längden på svaret spelar ingen roll.

Vad räknas är:

* Texten som skickas till Translator Text API i brödtexten i begäran
   * `Text` När du använder metoderna Översätt, Transliterate och ordlista sökning
   * `Text` och `Translation` när du använder metoden ordlista exempel
* Alla markeringar: HTML-, XML-taggar, o.s.v. i textfältet i begärandetexten. JSON-format som används för att skapa begäran (till exempel ”texten”:) räknas inte.
* En bokstav
* Skiljetecken
* Ett blanksteg, fliken, markup och alla slags tecken för tomt utrymme
* Varje kodpunkten som definierats i Unicode
* En upprepad översättning, även om du tidigare har översatt samma text

För skript utifrån ideogram, till exempel kinesiska och japanska Kanji, kommer Translator Text API fortfarande räkna antalet Unicode-koderna, ett tecken per ideogram. Undantag: Unicode surrogat antal som två tecken.

Antalet begäranden, ord, byte eller meningar är inte relevant i antal tecken.

Anrop till metoder för identifiering och BreakSentence räknas inte tecknet förbrukningen. Men förväntar vi sig att anropen för att identifiera och BreakSentence metoder är i en rimlig proportion till användningen av andra funktioner som räknas. Om antalet identifiera eller BreakSentence anrop du gör överskrider antalet andra räknade metoder av 100 gånger Microsoft förbehåller sig att begränsa användningen av identifierings- och BreakSentence metoder.


Mer information om antalet tecken som tillhör den [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
