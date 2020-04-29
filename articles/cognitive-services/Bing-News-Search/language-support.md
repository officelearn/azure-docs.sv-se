---
title: Språk stöd – API för nyhetssökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API för nyhetssökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220197"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Språk-och region stöd för API för nyhetssökning i Bing

API för nyhetssökning i Bing stöder flera länder/regioner, många med fler än ett språk. Om du anger ett land/en region med en fråga, är det främst att förfina Sök resultat baserat på intressen i landet/regionen. Dessutom kan resultaten innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användargränssnittet enligt angivet land/region eller språk.

Du kan ange ett land/en region med `cc` hjälp av Frågeparametern. Om du anger ett land/en region måste du också ange en eller flera språk koder med `Accept-Language` HTTP-huvudet. Vilka språk som stöds varierar beroende på regioner/region; de anges för varje land/region i tabellen marknad.

Alternativt kan du ange marknaden med hjälp av `mkt` Frågeparametern och en kod från tabellen **marknader** . Att ange en marknad samtidigt anger ett land/en region och ett önskat språk. `setLang` Frågeparametern kan anges till en språkkod i det här fallet. Detta är vanligt vis samma språk som anges `mkt` av om inte användaren föredrar att se Bing på ett annat språk.

## <a name="supported-markets-for-news-search-endpoint"></a>Marknader som stöds för nyhets sökningens slut punkt

I följande `/news/search` tabell visas de marknads kods värden som du kan använda för att ange `mkt` Frågeparametern för slut punkten. Bing returnerar innehåll enbart för dessa marknader. Listan kan komma att ändras.  

För en lista över lands-/region koder som du kan ange i `cc` Frågeparametern, se [lands koder](#countrycodes).  

|Land/region|Språk|Marknads kod|  
|---------------------|--------------|-----------------|
|Danmark|Danska|da-DK|
|Österrike|Tyska|avinstallation|
|Schweiz|Tyska|de-CH|
|Tyskland|Tyska|de-DE|
|Australien|Svenska|en – AU|
|Kanada|Svenska|en-CA|
|Storbritannien|Svenska|en-GB|
|Indonesien|Svenska|en-ID|
|Irland|Svenska|en-IE|
|Indien|Svenska|en-IN|
|Malaysia|Svenska|en-MY|
|Nya Zeeland|Svenska|en-NZ|
|Filippinerna|Svenska|en-PH|
|Singapore|Svenska|en-SG|
|USA|Svenska|sv-SE|
|Svenska|allmänt|en-WW|
|Svenska|allmänt|en-XA|
|Sydafrika|Svenska|en-ZA|
|Argentina|Spanska|ES-AR|
|Chile|Spanska|ES-CL|
|Spanien|Spanska|es-ES|
|Mexiko|Spanska|ES – MX|
|USA|Spanska|ES – USA|
|Spanska|allmänt|ES – XL|
|Finland|Finska|fi-FI|  
|Frankrike|Franska|fr-vara|
|Kanada|Franska|fr-CA|
|Belgien|Nederländska|NL-vara|
|Schweiz|Franska|Frankrike-CH|
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

## <a name="supported-markets-for-news-endpoint"></a>Marknader som stöds för diskussions grupps slut punkt
I följande `/news` tabell visas de marknads kods värden som du kan använda för att ange `mkt` Frågeparametern för slut punkten. Bing returnerar innehåll enbart för dessa marknader. Listan kan komma att ändras.  

För en lista över lands-/region koder som du kan ange i `cc` Frågeparametern, se [lands koder](#countrycodes).  

|Land/region|Språk|Marknads kod|  
|---------------------|--------------|-----------------|
|Danmark|Danska|da-DK|
|Tyskland|Tyska|de-DE|
|Australien|Svenska|en – AU|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|Svenska|allmänt|en-WW|
|Chile|Spanska|ES-CL|
|Mexiko|Spanska|ES – MX|
|USA|Spanska|ES – USA|
|Finland|Finska|fi-FI|  
|Kanada|Franska|fr-CA|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|it-IT|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Marknader som stöds för nyhets Trends slut punkt
I följande `/news/trendingtopics` tabell visas de marknads kods värden som du kan använda för att ange `mkt` Frågeparametern för slut punkten. Bing returnerar innehåll enbart för dessa marknader. Listan kan komma att ändras.  

För en lista över lands-/region koder som du kan ange i `cc` Frågeparametern, se [lands koder](#countrycodes).  

|Land/region|Språk|Marknads kod|  
|---------------------|--------------|-----------------|
|Tyskland|Tyska|de-DE|
|Australien|Svenska|en – AU|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|Kanada|Svenska|en-CA|
|Indien|Svenska|en-IN|
|Frankrike|Franska|fr-FR|
|Kanada|Franska|fr-CA|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Lands koder  

Följande är de lands-/regions koder som du kan ange i `cc` Frågeparametern. Listan kan komma att ändras.  

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
Mer information om Nyhetssökning i Bing slut punkter finns i [NYHETSSÖKNING API v7-referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).
