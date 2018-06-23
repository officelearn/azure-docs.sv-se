---
title: Microsoft Translator Text API – migrera till V3 | Microsoft Docs
description: Lär dig hur du migrerar från V2 v3 översättare Text API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: translator
ms.topic: article
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 16fec351af5b5b3875657ee244c18f305311d965
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354984"
---
# <a name="microsoft-translator-text-api-v2-to-v3-migration"></a>Microsoft Translator Text API V2 till V3-migrering

Microsoft Translator-teamet har släppt Version 3 (V3) av Text översättning API. Den här versionen innehåller nya funktioner, föråldrad metoder och ett nytt format för att skicka till och ta emot data från tjänsten Microsoft översättare. Det här dokumentet innehåller information för att ändra program att använda V3. V2 att bli inaktuell på 30 April 2018 och kommer att upphöra på 30 April 2019.

Slutet av det här dokumentet innehåller praktiska länkar som du vill veta mer.

## <a name="summary-of-features"></a>Sammanfattning av funktioner

* Inga Trace - V3 Nej-Trace gäller för alla prisnivåer i Azure-portalen. Detta innebär att ingen text som skickats till V3-API, kommer att sparas av Microsoft.
* JSON - XML ersätts av JSON. Alla data skickas till tjänsten och tas emot från tjänsten är i JSON-format.
* Flera språk för målet i en enskild begäran - det översätta metoden accepterar flera ”till”-språk för översättning i en enskild begäran. En enskild begäran kan exempelvis vara från-engelska och till tyska, spanska och japanska eller någon annan grupp av språk.
* En ordlista - en metod i en ordlista har lagts till API: et. Den här metoden innehåller 'sökning' och 'exempel'.
* Transliterate - en transliterate metod har lagts till API: et. Den här metoden konverterar ord och meningar i ett skript (t.ex. Arabiska) till ett annat skript (t.ex. Latinsk).
* Språk - en ny metod 'språk' levererar språkinformation i JSON-format för användning med 'översätta', '-ordlista' och 'transliterate' metoder.
* Nya Översätt - nya funktioner har lagts till metoden 'översätta' som stöder vissa funktioner som fanns i V2-API som separata metoder. Ett exempel är TranslateArray.
* Tala metoden - Text till tal-funktionen stöds inte längre i Microsoft översättare API. Text till tal-funktionen är tillgänglig i Microsoft Azure kognitiva Bing tal-API-tjänster.

Följande lista över V2 och V3 metoder identifierar de API: er som ger funktioner som medföljde V2 och V3 metoder.

| V2 API-metoden   | V3 API-kompatibilitet |
|:----------- |:-------------|
| Översätta     | Översätta          |
| TranslateArray      | Översätta          |
| GetLanguageNames      | Språk          |
| GetLanguagesForTranslate     | Språk        |
| GetLanguagesForSpeak      | Kognitiva Services tal API         |
| Tala     | Kognitiva Services tal API          |
| Upptäcka     | Upptäcka         |
| DetectArray     | Upptäcka         |
| AddTranslation     | Microsoft Translator hubb API         |
| AddTranslationArray    | Microsoft Translator hubb API          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | Funktionen stöds inte längre         |
| GetTranslationsArray      | Funktionen stöds inte längre         |

## <a name="move-to-json-format"></a>Flytta till JSON-format

Microsoft översättare Text översättning V2 accepteras och returnerade data i XML-format. I V3 är alla data som skickas och tas emot med API i JSON-format. XML-kommer inte längre accepteras eller returneras i V3. 

Den här ändringen påverkar flera aspekter av ett program som är skrivna för V2 Text översättning API. Exempel: språk API: N returnerar språkinformation om för textöversättning av, transliteration och två ordlista metoder. Du kan begära språkinformation om alla för alla metoder i ett anrop eller begär dem individuellt.

Metoden språk kräver inte autentisering. Du kan se alla språkinformation för V3 i JSON genom att klicka på följande länk:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, ordlista, transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Autentiseringsnyckel

Autentiseringsnyckeln som du använder för V2 accepteras för V3. Du behöver inte skaffa en ny prenumeration. Du kommer att kunna blanda V2 och V3 i dina appar under yearlong migreringen, vilket gör det lättare för dig att frigöra nya versioner när du migrerar från V2-XML-fortfarande till V3-JSON.

## <a name="pricing-model"></a>Prismodell

Microsoft översättare V3 prissätts på samma sätt som V2 har prissatta; per tecken inklusive blanksteg. De nya funktionerna i V3 göra några ändringar i vilka tecken som ska räknas för fakturering.

| V3-metoden   | Tecken räknas för fakturering |
|:----------- |:-------------|
| Språk     | Inga tecken som skickats ingen räknas, utan kostnad.          |
| Översätta     | Antalet baseras på hur många tecken som skickas för översättning och hur många språk tecknen översätts till. 50 tecken har skickats och 5 språk begärt blir 50 x 5.           |
| Transliterate     | Antalet tecken som skickats för transliteration räknas.         |
| Ordlistan sökning & exempel     | Antalet tecken som skickats för ordlistan sökning och exempel räknas.         |
| BreakSentence     | Utan kostnad.       |
| Upptäcka     | Utan kostnad.      |

## <a name="v3-end-points"></a>V3-slutpunkter

Global

* API.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>V3-API-metoder för översättningar av text

[Språk](reference/v3-0-languages.md)

[Översätta](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Identifiera](reference/v3-0-detect.md)

[Ordlistan-sökning](reference/v3-0-dictionary-lookup.md)

[Ordlistan/exempel](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Anpassning

Microsoft översättare V3 använder neural maskinöversättning som standard. Det kan därför inte användas med Microsoft översättare Hub som bara stöder äldre statistiska maskinöversättning. Anpassning av neural översättning är nu tillgängliga med hjälp av den anpassade konverteraren. [Lär dig mer om hur du anpassar neural maskinöversättning](customization.md)

Neural översättning med V3 texten API stöder inte användning av standard kategorier (smt, tal, text, generalnn).


## <a name="links"></a>Länkar

* [Sekretesspolicy för Microsoft](https://privacy.microsoft.com/privacystatement)
* [Juridisk Information för Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Villkor för onlinetjänster](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa V3.0 dokumentation](reference/v3-0-reference.md)
