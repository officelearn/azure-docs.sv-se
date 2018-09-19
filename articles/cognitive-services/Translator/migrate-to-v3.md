---
title: Migrera till V3 - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Lär dig hur du migrerar från V2 till V3 av Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: eaf65bef28110d73378c213ae4781a409b86e1bd
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128187"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API V2 till V3-migrering

Microsoft Translator-teamet har släppt Version 3 (V3) av Translator Text API. Den här versionen innehåller nya funktioner, föråldrad metoder och ett nytt format för att skicka till och ta emot data från tjänsten Microsoft Translator. Det här dokumentet innehåller information för att ändra programmen kan använda V3. V2 30 April 2018 kommer att bli inaktuella och kommer att upphöra den 30 April 2019.

Slutet av det här dokumentet innehåller användbara länkar du vill veta mer.

## <a name="summary-of-features"></a>Sammanfattning av funktioner

* Ingen spårning - i V3 Nej-spårning gäller för alla prisnivåer i Azure-portalen. Den här funktionen innebär att ingen text som skickas till V3-API kommer att sparas av Microsoft.
* JSON - XML ersätts av JSON. Alla data skickas till tjänsten och tas emot från tjänsten är i JSON-format.
* Flera språk för målet i en enskild begäran – The översätta metoden accepterar flera ”till”-språk för översättning i en enskild begäran. En enskild begäran kan exempelvis vara ”från” engelska och att tyska, spanska och japanska eller någon annan grupp av språk.
* Tvåspråkig ordlista – en tvåspråkig ordlista metod har lagts till API: et. Den här metoden innehåller 'lookup ”och” exempel ”.
* Transkribera – en transliterate metod har lagts till API: et. Den här metoden konverterar ord och meningar i ett skript (t.ex. Arabiska) till ett annat skript (t.ex. Latinsk).
* Språk – en ny metod som ”språk” levererar språkinformation, i JSON-format för användning med ”översätta', '-ordlista' och 'transkribera' metoder.
* Ny i Översätt - nya funktioner har lagts till metoden ”översätta” att ha stöd för vissa funktioner som fanns i V2-API som separata metoder. Ett exempel är TranslateArray.
* Tala metoden - Text till tal-funktioner stöds inte längre i Microsoft Translator-API. Text till tal-funktionen är tillgänglig i Azure Cognitive services API för Bing-taligenkänning.

Följande lista över V2 och V3 metoder identifierar V3-metoder och API: er som tillhandahåller de funktioner som medföljde V2.

| V2 API-metoden   | V3 API-kompatibilitet |
|:----------- |:-------------|
| Översätt     | Översätt          |
| TranslateArray      | Översätt          |
| GetLanguageNames      | Språk          |
| GetLanguagesForTranslate     | Språk        |
| GetLanguagesForSpeak      | Cognitive Services API för taligenkänning         |
| Tala     | Cognitive Services API för taligenkänning          |
| Upptäcka     | Upptäcka         |
| DetectArray     | Upptäcka         |
| AddTranslation     | Microsoft Translator HUB API         |
| AddTranslationArray    | Microsoft Translator HUB API          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | Funktionen stöds inte längre         |
| GetTranslationsArray      | Funktionen stöds inte längre         |

## <a name="move-to-json-format"></a>Flytta till JSON-format

Microsoft Translator Text Translation V2 accepterat och returnerade data i XML-format. I V3 är alla data som skickas och tas emot med hjälp av API: et i JSON-format. XML-kommer inte längre godkända eller returneras i V3. 

Den här ändringen påverkar flera aspekter av ett program som skrivits för V2 Text Translation-API. Som exempel: språk API returnerar språkinformation om för textöversättning, transkriberingsspråk och två ordlista-metoder. Du kan begära språkinformation för alla för alla metoder i ett anrop eller begär dem individuellt.

Metoden språk kräver inte autentisering; Du kan se alla språkinformation för V3 i JSON genom att klicka på följande länk:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, ordlista, transkriberingsspråk](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Autentiseringsnyckel

Den autentiseringsnyckel som du använder för V2 kommer accepteras för V3. Du behöver inte skaffa en ny prenumeration. Du kommer att kunna blanda V2 och V3 i dina appar under yearlong migreringen, vilket gör det enklare för dig att släppa nya versioner medan du fortfarande migrerar från V2-XML till V3-JSON.

## <a name="pricing-model"></a>Prismodell

Microsoft Translator V3 debiteras på samma sätt som V2 har prissätts; per tecken inklusive blanksteg. De nya funktionerna i V3 gör några ändringar i vilka tecken räknas för fakturering.

| V3-metod   | Tecken som räknas för fakturering |
|:----------- |:-------------|
| Språk     | Inga tecken som har skickats, ingen räknas, utan kostnad.          |
| Översätt     | Antal baseras på hur många tecken har skickats för översättning och hur många språk tecknen översätts till. 50 tecken har skickats och 5 språk som begärs blir 50 x 5.           |
| Transkribera     | Antalet tecken som skickats för transkriberingsspråk räknas.         |
| Ordlista lookup & exempel     | Antalet tecken som skickats för ordlista Sök- och räknas.         |
| BreakSentence     | Ingen extra kostnad.       |
| Upptäcka     | Ingen extra kostnad.      |

## <a name="v3-end-points"></a>V3-slutpunkter

Global

* API.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>V3 API text översättningar-metoder

[Språk](reference/v3-0-languages.md)

[Översätt](reference/v3-0-translate.md)

[Transkribera](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Identifiera](reference/v3-0-detect.md)

[Ordlista/sökning](reference/v3-0-dictionary-lookup.md)

[Ordlista/exempel](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Anpassning

Microsoft Translator V3 använder neural maskinöversättning som standard. Det kan därför inte användas med Microsoft Translator Hub. Translator-hubben har endast stöd för äldre statistiska maskinöversättning. Anpassning av neural översättning är nu tillgänglig med hjälp av anpassade Translator. [Läs mer om hur du anpassar neural maskinöversättning](customization.md)

Neural översättning med text API V3 stöder inte användning av standard kategorier (SMT, tal, text, generalnn).


## <a name="links"></a>Länkar

* [Sekretesspolicy för Microsoft](https://privacy.microsoft.com/privacystatement)
* [Juridisk Information om Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Villkoren för Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa V3.0 dokumentation](reference/v3-0-reference.md)
