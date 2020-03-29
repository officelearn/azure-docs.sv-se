---
title: Språkstöd - API för sökning av Bing-nyheter
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API:et för sökning i Bing.A list of natural languages, countries and regions that are supported by the Bing News Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220197"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Språk- och regionstöd för API:et för sökning av Bing-nyheter

Api:et för sökning i Bing stöder många länder/regioner, många med mer än ett språk. Om du anger ett land/en region med en fråga används i första hand för att förfina sökresultaten baserat på intressen i det landet/regionen. Dessutom kan resultaten innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användarupplevelsen enligt det angivna landet/regionen eller språket.

Du kan ange ett land/en region med frågeparametern. `cc` Om du anger ett land/en region måste du också `Accept-Language` ange en eller flera språkkoder med HTTP-huvudet. Språken som stöds varierar beroende på countr/region. de anges för varje land/region i tabellen Marknader.

Du kan också ange marknaden `mkt` med frågeparametern och en kod från tabellen **Marknader.** Om du anger en marknad samtidigt anges ett land/en region och ett önskat språk. Frågeparametern `setLang` kan ställas in på en språkkod i det här fallet. vanligtvis är detta samma språk `mkt` som anges av om inte användaren föredrar att se Bing på ett annat språk.

## <a name="supported-markets-for-news-search-endpoint"></a>Marknader som stöds för slutpunkt för nyhetssökning

För `/news/search` slutpunkten visas följande tabell de marknadskodvärden som du `mkt` kan använda för att ange frågeparametern. Bing returnerar innehåll för endast dessa marknader. Listan kan komma att ändras.  

En lista över lands-/regionkoder som `cc` du kan ange i frågeparametern finns i [Landskoder](#countrycodes).  

|Land/region|Språk|Marknadskod|  
|---------------------|--------------|-----------------|
|Danmark|Danska|da-DK|
|Österrike|Tyska|av-AT|
|Schweiz|Tyska|de-CH (olika)|
|Tyskland|Tyska|de-DE|
|Australien|Svenska|sv-AU|
|Kanada|Svenska|sv-CA|
|Storbritannien|Svenska|en-GB|
|Indonesien|Svenska|sv-ID|
|Irland|Svenska|sv-IE|
|Indien|Svenska|sv-IN|
|Malaysia|Svenska|sv-MIN|
|Nya Zeeland|Svenska|sv-NZ|
|Filippinerna|Svenska|sv-PH|
|Singapore|Svenska|sv-SG|
|USA|Svenska|sv-SE|
|Svenska|allmänt|sv-WW|
|Svenska|allmänt|sv-XA|
|Sydafrika|Svenska|sv-ZA|
|Argentina|Spanska|es-AR|
|Chile|Spanska|es-CL|
|Spanien|Spanska|es-ES|
|Mexiko|Spanska|es-MX|
|USA|Spanska|es-USA|
|Spanska|allmänt|es-XL|
|Finland|Finska|fi-FI|  
|Frankrike|Franska|fr-BE|
|Kanada|Franska|fr-CA|
|Belgien|Nederländska|nl-BE|
|Schweiz|Franska|fr-CH|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|it-IT|
|Hongkong|Traditionell kinesiska|zh-HK|  
|Taiwan|Traditionell kinesiska|zh-TW|
|Japan|Japanska|ja-JP|  
|Korea|Koreansk|ko-KR|  
|Nederländerna|Nederländska|nl-NL|  
|Folkrepubliken Kina|Kinesiska|zh-CN|  
|Brasilien|Portugisiska|pt-BR|
|Ryssland|Ryska|ru-RU|  
|Sverige|Svenska|sv-SE|  
|Turkiet|Turkiska|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Marknader som stöds för slutpunkten för nyheter
För `/news` slutpunkten visas följande tabell de marknadskodvärden som du `mkt` kan använda för att ange frågeparametern. Bing returnerar innehåll för endast dessa marknader. Listan kan komma att ändras.  

En lista över lands-/regionkoder som `cc` du kan ange i frågeparametern finns i [Landskoder](#countrycodes).  

|Land/region|Språk|Marknadskod|  
|---------------------|--------------|-----------------|
|Danmark|Danska|da-DK|
|Tyskland|Tyska|de-DE|
|Australien|Svenska|sv-AU|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|Svenska|allmänt|sv-WW|
|Chile|Spanska|es-CL|
|Mexiko|Spanska|es-MX|
|USA|Spanska|es-USA|
|Finland|Finska|fi-FI|  
|Kanada|Franska|fr-CA|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|it-IT|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Marknader som stöds för slutpunkt för nyhetstrender
För `/news/trendingtopics` slutpunkten visas följande tabell de marknadskodvärden som du `mkt` kan använda för att ange frågeparametern. Bing returnerar innehåll för endast dessa marknader. Listan kan komma att ändras.  

En lista över lands-/regionkoder som `cc` du kan ange i frågeparametern finns i [Landskoder](#countrycodes).  

|Land/region|Språk|Marknadskod|  
|---------------------|--------------|-----------------|
|Tyskland|Tyska|de-DE|
|Australien|Svenska|sv-AU|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|Kanada|Svenska|sv-CA|
|Indien|Svenska|sv-IN|
|Frankrike|Franska|fr-FR|
|Kanada|Franska|fr-CA|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Landskoder  

Följande är de lands-/regionkoder som `cc` du kan ange i frågeparametern. Listan kan komma att ändras.  

|Land/region|Landskod|  
|---------------------|------------------|  
|Argentina|AR|  
|Australien|AU|  
|Österrike|AT|  
|Belgien|BE|  
|Brasilien|BR|  
|Kanada|CA|  
|Chile|CL|  
|Danmark|DK|  
|Finland|FI|  
|Frankrike|FR|  
|Tyskland|DE|  
|Hongkong|HK|  
|Indien|IN|  
|Indonesien|ID|  
|Italien|IT|  
|Japan|JP|  
|Korea|KR|  
|Malaysia|MY|  
|Mexiko|MX|  
|Nederländerna|NL|  
|Nya Zeeland|NZ|  
|Norge|NO|  
|Folkrepubliken Kina|CN|  
|Polen|PL|  
|Portugal|PT|  
|Filippinerna|PH|  
|Ryssland|RU|  
|Saudiarabien|SA|  
|Sydafrika|ZA|  
|Spanien|ES|  
|Sverige|SE|  
|Schweiz|CH|  
|Taiwan|TW|  
|Turkiet|TR|  
|Storbritannien|GB|  
|USA|USA|

## <a name="next-steps"></a>Nästa steg
Mer information om slutpunkterna För Bing-nyheter finns i [API v7-referens för nyhetssökning](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).
