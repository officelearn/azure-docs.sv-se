---
title: Migrera till V3 - Translator Text API
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller stegen för att hjälpa dig att migrera från V2 till V3 i Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: eb43d549d3e0cd449c865d533fc8701c4c3912fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837316"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Översättare Text API V2 till V3 Migrering

> [!NOTE]
> V2 inaktuella den 30 april 2018. Migrera dina program till V3 för att dra nytta av nya funktioner som är tillgängliga exklusivt i V3.
> 
> Microsoft Translator Hub dras tillbaka den 17 maj 2019. [Visa viktig migreringsinformation och viktiga datum](https://www.microsoft.com/translator/business/hub/).  

Microsoft Translator-teamet har släppt version 3 (V3) av Translator Text API. Den här versionen innehåller nya funktioner, föråldrade metoder och ett nytt format för att skicka till och ta emot data från Microsoft Translator Service. Det här dokumentet innehåller information om hur du ändrar program för att använda V3. 

Slutet av det här dokumentet innehåller användbara länkar som du kan lära dig mer om.

## <a name="summary-of-features"></a>Sammanfattning av funktioner

* Ingen spårning - I V3 No-Trace gäller för alla prisnivåer i Azure-portalen. Den här funktionen innebär att ingen text som skickas till V3 API, kommer att sparas av Microsoft.
* JSON - XML ersätts av JSON. Alla data som skickas till tjänsten och tas emot från tjänsten är i JSON-format.
* Flera målspråk i en enda begäran - Metoden Translate accepterar flera "till"-språk för översättning i en enda begäran. En enda begäran kan till exempel vara "från" engelska och "till" tyska, spanska och japanska, eller någon annan grupp av språk.
* Tvåspråkig ordlista - En tvåspråkig ordboksmetod har lagts till i API:et. Den här metoden innehåller "uppslag" och "exempel".
* Translitterat - En translitterat metod har lagts till i API. Den här metoden konverterar ord och meningar i ett skript (t.ex. arabiska) till ett annat skript (t.ex. Latin).
* Språk - En ny "språkmetod" ger språkinformation, i JSON-format, för användning med metoderna "översätta", "ordbok" och "translitterera".
* Ny att översätta - Nya funktioner har lagts till metoden "översätt" för att stödja några av de funktioner som fanns i V2 API som separata metoder. Ett exempel är TranslateArray.
* Läs metod - Funktioner för text till tal stöds inte längre i Microsoft Translator API. Text till tal finns i [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Följande lista över V2- och V3-metoder identifierar V3-metoder och API:er som tillhandahåller de funktioner som medföljde V2.

| V2 API-metod   | V3 API-kompatibilitet |
|:----------- |:-------------|
| `Translate`     | [Översätta](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Översätta](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Språk](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Språk](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft-taltjänst](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft-taltjänst](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Upptäcka](reference/v3-0-detect.md)         |
| `DetectArray`     | [Upptäcka](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funktionen stöds inte längre       |
| `AddTranslationArray`    | Funktionen stöds inte längre          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funktionen stöds inte längre         |
| `GetTranslationsArray`      | Funktionen stöds inte längre         |

## <a name="move-to-json-format"></a>Flytta till JSON-format

Microsoft Translator Text Translation V2 accepterade och returnerade data i XML-format. I V3 är alla data som skickas och tas emot med API i JSON-format. XML accepteras eller returneras inte längre i V3.

Den här ändringen påverkar flera aspekter av ett program som är skrivet för V2 Text Translation API. Som ett exempel: API:et språk returnerar språkinformation för textöversättning, translitterering och de två ordlistemetoderna. Du kan begära all språkinformation för alla metoder i ett samtal eller begära dem individuellt.

Språkmetoden kräver ingen autentisering. genom att klicka på följande länk kan du se all språkinformation för V3 i JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,ordbok,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Autentiseringsnyckel

Autentiseringsnyckeln som du använder för V2 accepteras för V3. Du behöver inte skaffa en ny prenumeration. Du kommer att kunna blanda V2 och V3 i dina appar under den årslånga migreringsperioden, vilket gör det enklare för dig att släppa nya versioner medan du fortfarande migrerar från V2-XML till V3-JSON.

## <a name="pricing-model"></a>Prismodell

Microsoft Translator V3 är prissatt på samma sätt V2 prissatts; per tecken, inklusive blanksteg. De nya funktionerna i V3 gör vissa ändringar i vilka tecken som räknas för fakturering.

| V3-metod   | Tecken som räknas för fakturering |
|:----------- |:-------------|
| `Languages`     | Inga tecken har skickats, inga räknade, ingen avgift.          |
| `Translate`     | Antalet baseras på hur många tecken som skickas in för översättning och hur många språk tecknen översätts till. 50 inlämnade tecken och 5 språk som begärs kommer att vara 50x5.           |
| `Transliterate`     | Antal tecken som skickats för translitterering räknas.         |
| `Dictionary lookup & example`     | Antal tecken som skickas för dictionary-sökning och exempel räknas.         |
| `BreakSentence`     | Ingen kostnad.       |
| `Detect`     | Ingen kostnad.      |

## <a name="v3-end-points"></a>V3 slutpunkter

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Metoder för V3 API-textöversättningar

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilitet och anpassning

> [!NOTE]
> 
> Microsoft Translator Hub dras tillbaka den 17 maj 2019. [Visa viktig migreringsinformation och viktiga datum](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 använder som standard neural maskinöversättning. Den kan därför inte användas med Microsoft Translator Hub. Translator Hub stöder endast äldre statistisk maskinöversättning. Anpassning för neural översättning är nu tillgänglig med hjälp av Custom Translator. [Läs mer om att anpassa översättningen av neurala maskiner](custom-translator/overview.md)

Neural översättning med V3 text API stöder inte användningen av standardkategorier (SMT, tal, tech, generalnn).

| |Slutpunkt|    GDPR-processorefterlevnad|  Använda Translator Hub| Använd anpassad översättare (förhandsgranskning)|
|:-----|:-----|:-----|:-----|:-----|
|Översättning Text API Version 2| api.microsofttranslator.com|    Inga  |Ja    |Inga|
|Api för översättare API version 3| api.cognitive.microsofttranslator.com|  Ja|    Inga| Ja|

**Api för översättare API version 3**
* Är allmänt tillgänglig och fullt stöd.
* Är GDPR-kompatibel som processor och uppfyller alla ISO 20001 och 20018 samt SOC 3-certifieringskrav. 
* Gör att du kan anropa de översättningssystem för neurala nätverk som du har anpassat med Custom Translator (Preview), den nya anpassningsfunktionen Translator NMT. 
* Ger inte åtkomst till anpassade översättningssystem som skapats med Hjälp av Microsoft Translator Hub.

Du använder version 3 av Translator Text API Om du använder api.cognitive.microsofttranslator.com slutpunkten.

**Översättning Text API Version 2**
* Uppfyller inte alla ISO 20001,20018- och SOC 3-certifieringskrav. 
* Tillåter inte att du anropar de översättningssystem för neurala nätverk som du har anpassat med anpassningsfunktionen Translator.
* Ger åtkomst till anpassade översättningssystem som skapats med Hjälp av Microsoft Translator Hub.
* Du använder version 2 av Translator Text API Om du använder api.microsofttranslator.com slutpunkten.

Ingen version av Translator API skapar en post av dina översättningar. Dina översättningar delas aldrig med någon. Mer information finns på translator [no-trace-webbsidan.](https://www.aka.ms/NoTrace)

## <a name="links"></a>Länkar

* [Microsoft sekretesspolicy](https://privacy.microsoft.com/privacystatement)
* [Juridisk information om Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Villkor för onlinetjänster](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa V3.0-dokumentation](reference/v3-0-reference.md)
