---
title: Text förskjutningar i API för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig mer om förskjutningar som orsakas av flerspråkiga och emoji-kodningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219240"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Text förskjutningar i API för textanalys utdata

Stöd för flerspråkig och emoji har lett till Unicode-kodningar som använder mer än en [kod punkt](https://wikipedia.org/wiki/Code_point) för att representera ett enda visat tecken, som kallas en Grapheme. Till exempel kan emojis som 🌷 och 👍 använda flera tecken för att skapa formen med ytterligare tecken för visuella attribut, till exempel hudton. På samma sätt kodas Word-`अनुच्छेद` för hindi som fem bokstäver och tre kombinations märken.

På grund av olika längd på möjliga flerspråkiga och emoji-kodningar kan API för textanalys returnera förskjutningar i svaret.

## <a name="offsets-in-the-api-response"></a>Förskjutningar i API-svaret. 

Tänk på följande när offsets returnerade API-svar, till exempel [igenkänning av namngivna enheter](../how-tos/text-analytics-how-to-entity-linking.md) eller [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md):

* Element i svaret kan vara särskilt för den slut punkt som anropades. 
* HTTP POST/Hämta nytto laster kodas i [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), vilket kan vara standard tecken kodningen på klient sidans kompilator eller operativ system.
* Offsets avser antalet Grapheme som baseras på [Unicode-8.0.0](https://unicode.org/versions/Unicode8.0.0) standard, inte tecken antal.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extrahera del strängar från text med förskjutningar

Förskjutningar kan orsaka problem när du använder teckenbaserade under sträng metoder, till exempel metoden .NET [substring ()](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) . Ett problem är att en förskjutning kan leda till att en del Strängs metod avslutas i mitten av en Grapheme kodning i flera tecken i stället för slutet.

I .NET bör du överväga att använda klassen [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) , som gör att du kan arbeta med en sträng som en serie text element, i stället för enskilda tecken objekt. Du kan också söka efter Grapheme delarer bibliotek i din önskade program miljö. 

API för textanalys returnerar även dessa text element, för bekvämlighet.

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Sentiment-analys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Enhets igenkänning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språk igenkänning](../how-tos/text-analytics-how-to-language-detection.md)
