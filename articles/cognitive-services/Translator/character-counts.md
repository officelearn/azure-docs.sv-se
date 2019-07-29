---
title: Antal bokstäver – Translator Text API
titleSuffix: Azure Cognitive Services
description: Hur Translator Text API räknar tecken.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e3a16d9272e75f9a94f5381c1681c036d177e0f6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595982"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hur Translator Text API räknar tecken

Translator Text API räknar varje Unicode-kodtyp för inmatad text som ett tecken. Varje översättning av en text till ett språk räknas som en separat översättning, även om begäran gjordes i ett enda API-anrop översätts till flera språk. Svars tiden spelar ingen roll.

Antalet är:

* Text som skickats till Translator Text API i bröd texten i begäran
   * `Text`När du använder Sök metoderna Översätt, translittererad och ord lista
   * `Text`och `Translation` när du använder exempel metoden för ord listan
* Alla markeringar: HTML-taggar, och så vidare i textfältet i begär ande texten. JSON-notation som används för att bygga begäran (för instans "text:") räknas inte.
* En enskild bokstav
* Skiljetecken
* Ett blank steg, en tabb, ett pålägg och en typ av blank stegs tecken
* Varje kod punkt som definierats i Unicode
* En upprepad översättning, även om du har översatt samma text tidigare

För skript som baseras på ideogram, till exempel kinesiska och japanska, kommer Translator Text API fortfarande räkna antalet Unicode-kodfragment, ett tecken per ideogram. Undantag: Unicode-surrogat räknas som två tecken.

Antalet begär Anden, ord, byte eller meningar är irrelevant i antal tecken.

Anrop till metoderna detect och BreakSentence räknas inte in i Character-förbrukningen. Vi förväntar sig dock att anropen till metoderna detektera och BreakSentence är i rimlig proportion till användningen av andra funktioner som räknas. Om antalet identifierings-eller BreakSentence-anrop du gör överstiger antalet andra räknade metoder med 100 gånger, förbehåller Microsoft sig rätten att begränsa din användning av metoderna identifiera och BreakSentence.


Mer information om antal bokstäver finns i [vanliga frågor och svar om Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
