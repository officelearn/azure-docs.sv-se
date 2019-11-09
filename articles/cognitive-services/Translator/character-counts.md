---
title: Antal bokstäver – Translator Text API
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklaras hur Azure Cognitive Services Translator Text API räknar tecken, så att du kan förstå hur det matas in på innehållet.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888147"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hur Translator Text API räknar tecken

Translator Text API räknar varje Unicode-kodtyp för inmatad text som ett tecken. Varje översättning av en text till ett språk räknas som en separat översättning, även om begäran gjordes i ett enda API-anrop översätts till flera språk. Svars tiden spelar ingen roll.

Antalet är:

* Text som skickats till Translator Text API i bröd texten i begäran
   * `Text` när du använder Sök metoderna Översätt, translittererad och ord lista
   * `Text` och `Translation` när du använder exempel metoden för ord listan
* All markering: HTML, XML-taggar osv. inom textfältet i begär ande texten. JSON-notation som används för att bygga begäran (för instans "text:") räknas inte.
* En enskild bokstav
* skiljetecken
* Ett blank steg, en tabb, ett pålägg och en typ av blank stegs tecken
* Varje kod punkt som definierats i Unicode
* En upprepad översättning, även om du har översatt samma text tidigare

För skript som baseras på ideogram, till exempel kinesiska och japanska, kommer Translator Text API fortfarande räkna antalet Unicode-kodfragment, ett tecken per ideogram. Undantag: Unicode-surrogat räknas som två tecken.

Antalet begär Anden, ord, byte eller meningar är irrelevant i antal tecken.

Anrop till metoderna detect och BreakSentence räknas inte in i Character-förbrukningen. Vi förväntar sig dock att anropen till metoderna detektera och BreakSentence är i rimlig proportion till användningen av andra funktioner som räknas. Om antalet identifierings-eller BreakSentence-anrop du gör överstiger antalet andra räknade metoder med 100 gånger, förbehåller Microsoft sig rätten att begränsa din användning av metoderna identifiera och BreakSentence.


Mer information om antal bokstäver finns i [vanliga frågor och svar om Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
