---
title: Textförskjutningar i API:et för textanalys
titleSuffix: Azure Cognitive Services
description: Läs mer om förskjutningar som orsakas av flerspråkiga och emoji-kodningar.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219240"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Textförskjutningar i API-utdata för Text Analytics

Flerspråkig support och emoji-stöd har lett till Unicode-kodningar som använder mer än en [kodpunkt](https://wikipedia.org/wiki/Code_point) för att representera ett enda tecken som visas, kallat grapheme. Emojis gillar till exempel 🌷 👍 och kan använda flera tecken för att komponera formen med ytterligare tecken för visuella attribut, till exempel hudton. På samma sätt är `अनुच्छेद` hindi ordet kodas som fem bokstäver och tre kombinera märken.

På grund av de olika längderna på möjliga flerspråkiga och emoji-kodningar kan API:et för textanalys returnera förskjutningar i svaret.

## <a name="offsets-in-the-api-response"></a>Förskjutningar i API-svaret. 

När förskjutningar returneras kom API-svaret, till exempel [Namngiven entitetsigenkänning](../how-tos/text-analytics-how-to-entity-linking.md) eller [Sentimentanalys,](../how-tos/text-analytics-how-to-sentiment-analysis.md)följande:

* Element i svaret kan vara specifika för slutpunkten som anropades. 
* HTTP POST/GET-nyttolaster kodas i [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), vilket kanske eller kanske inte är standardteckenkodningen på kompilatorn eller operativsystemet på klientsidan.
* Förskjutningar refererar till diagramantal baserat på [Unicode 8.0.0-standarden,](https://unicode.org/versions/Unicode8.0.0) inte teckenantal.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extrahera delsträngar från text med förskjutningar

Förskjutningar kan orsaka problem när du använder teckenbaserade delsträngsmetoder, till exempel metoden .NET [substring().](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) Ett problem är att en förskjutning kan orsaka att en delsträngsmetod slutar mitt i en diagramkodning med flera tecken i stället för slutet.

I .NET kan du använda klassen [StringInfo,](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) som gör att du kan arbeta med en sträng som en serie textelement i stället för enskilda teckenobjekt. Du kan också leta efter grapheme splitter bibliotek i önskad programmiljö. 

Api:et för textanalys returnerar även dessa textelement för enkelhetens skull.

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Sentimentanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Erkännande av entitet](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språkigenkänning](../how-tos/text-analytics-how-to-language-detection.md)
