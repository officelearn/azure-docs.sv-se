---
title: Antal bokstäver – Translator
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklaras hur Azure Cognitive Services Translator räknar tecken, så att du kan förstå hur den matar in innehåll.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 0219940a4ac60e4a6187d13802c36e8bff1925b3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587331"
---
# <a name="how-the-translator-counts-characters"></a>Hur Translator räknar tecken

Translator räknar varje Unicode-kodtyp för inmatad text som ett tecken. Varje översättning av en text till ett språk räknas som en separat översättning, även om begäran gjordes i ett enda API-anrop översätts till flera språk. Svars tiden spelar ingen roll.

Antalet är:

* Text som skickats till Translator i bröd texten i begäran
   * `Text`När du använder Sök metoderna Översätt, translittererad och ord lista
   * `Text`och `Translation` när du använder exempel metoden för ord listan
* All markering: HTML, XML-taggar osv. inom textfältet i begär ande texten. JSON-notation som används för att bygga begäran (för instans "text:") räknas inte.
* En enskild bokstav
* Skiljetecken
* Ett blank steg, en tabb, ett pålägg och en typ av blank stegs tecken
* Varje kod punkt som definierats i Unicode
* En upprepad översättning, även om du har översatt samma text tidigare

För skript som baseras på ideogram, till exempel kinesiska och japanska, räknas översättnings tjänsten fortfarande antalet Unicode-kodfragment, ett tecken per ideogram. Undantag: Unicode-surrogat räknas som två tecken.

Antalet begär Anden, ord, byte eller meningar är irrelevant i antal tecken.

Anrop till metoderna detect och BreakSentence räknas inte in i Character-förbrukningen. Vi förväntar sig dock att anropen till metoderna detektera och BreakSentence är i rimlig proportion till användningen av andra funktioner som räknas. Om antalet identifierings-eller BreakSentence-anrop du gör överstiger antalet andra räknade metoder med 100 gånger, förbehåller Microsoft sig rätten att begränsa din användning av metoderna identifiera och BreakSentence.

Mer information om antal bokstäver finns i [vanliga frågor och svar om Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
