---
title: Teckenantal - Translator Text API
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur Azure Cognitive Services Translator Text API räknar tecken så att du kan förstå hur det matar in innehåll.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888147"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Så här räknar Translator Text API tecken

Translator Text API räknar varje Unicode-kodpunkt för indatatext som ett tecken. Varje översättning av en text till ett språk räknas som en separat översättning, även om begäran gjordes i ett enda API-anrop som översätter till flera språk. Längden på svaret spelar ingen roll.

Det som räknas är:

* Text som skickas till translatortext-API:et i brödtexten i begäran
   * `Text`när du använder metoderna Översätt, Translitterera och Ordlista
   * `Text`och `Translation` när du använder metoden Ordlisteexempel
* Alla markeringar: HTML, XML-taggar etc. i textfältet i begäran brödtexten. JSON-notation som används för att skapa begäran (till exempel "Text:") räknas inte.
* Ett enskilt brev
* Skiljetecken
* Ett blanksteg, en flik, ett markeringstecken och ett valfritt blanksteg
* Varje kodpunkt som definieras i Unicode
* En upprepad översättning, även om du har översatt samma text tidigare

För skript baserade på ideogram som kinesiska och japanska Kanji räknar Translator Text API fortfarande antalet Unicode-kodpunkter, ett tecken per ideogram. Undantag: Unicode-surrogat räknas som två tecken.

Antalet begäranden, ord, byte eller meningar är irrelevant i teckenantalet.

Anropar metoderna Identifiera och BreakSentence räknas inte i teckenförbrukningen. Vi förväntar oss dock att anropen till metoderna Detect och BreakSentence står i rimlig proportion till användningen av andra funktioner som räknas. Om antalet Detect- eller BreakSentence-anrop som du ringer överskrider antalet andra räknade metoder med 100 gånger, förbehåller sig Microsoft rätten att begränsa din användning av metoderna Detect och BreakSentence.


Mer information om teckenantal finns i vanliga frågor och [svar om Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
