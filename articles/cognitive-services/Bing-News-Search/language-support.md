---
title: Stöd för språk - nyhetssökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturligt språk, länder och regioner som stöds av den nyhetssökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: e8a7f6d20ed36c4b3e900602fb52e31dceefae88
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799333"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Stöd för språk och din region för den nyhetssökning i Bing

Den nyhetssökning i Bing har stöd för flera länder/regioner, och många med flera olika språk. Ange land/region med en fråga används främst för att begränsa sökresultaten baserat på intresse i landet/regionen. Dessutom resultaten kan innehålla länkar till Bing och dessa länkar kan lokalisera Bing användarupplevelsen enligt den angivna land/region eller språk.

Du kan ange ett land/region med hjälp av den `cc` frågeparameter. Om du anger ett land/region, måste du även ange en eller flera språkkoder med hjälp av den `Accept-Language` HTTP-huvud. Språk som stöds varierar beroende på countr/region. de tilldelas för varje land/region i tabellen marknader.

Du kan också ange en marknad med hjälp av den `mkt` parameter och en kod från den **marknader** tabell. Ange en marknad samtidigt anger land/region och ett önskat språk. Den `setLang` Frågeparametern kan anges till en språkkod i det här fallet; detta är vanligtvis samma språk som anges av `mkt` såvida inte du föredrar att se Bing på ett annat språk.

## <a name="supported-markets-for-news-search-endpoint"></a>Marknader för Nyheter search slutpunkten som stöds

För den `/news/search` slutpunkt i följande tabell visas de marknad kodvärden som du kan använda för att ange den `mkt` frågeparameter. Bing returnerar innehåll för endast dessa marknader. Listan kan ändras.  

För en lista över land/region koder som du kan ange i den `cc` frågeparameter, se [landskoder](#countrycodes).  

|Land/region|Språk|Marknaden kod|  
|---------------------|--------------|-----------------|
|Danmark|Danska|da-DK|
|Österrike|Tyska|Tyskland-AT|
|Schweiz|Tyska|Tyskland – CH|
|Tyskland|Tyska|de-DE|
|Australien|Engelska|SV-Australien|
|Kanada|Engelska|en CA: N|
|Storbritannien och Nordirland|Engelska|en-GB|
|Indonesien|Engelska|en-ID|
|Irland|Engelska|en IE|
|Indien|Engelska|en Indien|
|Malaysia|Engelska|en-MY|
|Nya Zeeland|Engelska|en NZ|
|Filippinerna|Engelska|en-PH|
|Singapore|Engelska|en-SG|
|USA|Engelska|en-US|
|Engelska|Allmänt|en WW|
|Engelska|Allmänt|en-XA|
|Sydafrika|Engelska|en ZA|
|Argentina|Spanska|es-AR|
|Chile|Spanska|es-CL|
|Spanien|Spanska|es-ES|
|Mexico|Spanska|es-MX|
|USA|Spanska|es-US|
|Spanska|Allmänt|es-XL|
|Finland|Finska|fi-FI|  
|Frankrike|Franska|fr-BE|
|Kanada|Franska|fr-CA|
|Belgien|Nederländska|nl-BE|
|Schweiz|Franska|fr CH|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|IT-IT|
|Hongkong SAR|Traditionell kinesiska|zh-HK|  
|Taiwan|Traditionell kinesiska|zh-TW|
|Japan|japanska|ja-JP|  
|Korea|Koreanska|ko-KR|  
|Nederländerna|Nederländska|NL-NL|  
|Folkrepubliken Kina|Kinesiska|zh-CN|  
|Brasilien|Portugisiska|pt-BR|
|Ryssland|Ryska|ru-RU|  
|Sverige|Svenska|SV-SE|  
|Turkiet|Turkiska|TR-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Stöds marknader för Nyheter slutpunkten
För den `/news` slutpunkt i följande tabell visas de marknad kodvärden som du kan använda för att ange den `mkt` frågeparameter. Bing returnerar innehåll för endast dessa marknader. Listan kan ändras.  

För en lista över land/region koder som du kan ange i den `cc` frågeparameter, se [landskoder](#countrycodes).  

|Land/region|Språk|Marknaden kod|  
|---------------------|--------------|-----------------|
|Danmark|Danska|da-DK|
|Tyskland|Tyska|de-DE|
|Australien|Engelska|SV-Australien|
|Storbritannien och Nordirland|Engelska|en-GB|
|USA|Engelska|en-US|
|Engelska|Allmänt|en WW|
|Chile|Spanska|es-CL|
|Mexico|Spanska|es-MX|
|USA|Spanska|es-US|
|Finland|Finska|fi-FI|  
|Kanada|Franska|fr-CA|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|IT-IT|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Marknader för populära nyheter-slutpunkten som stöds
För den `/news/trendingtopics` slutpunkt i följande tabell visas de marknad kodvärden som du kan använda för att ange den `mkt` frågeparameter. Bing returnerar innehåll för endast dessa marknader. Listan kan ändras.  

För en lista över land/region koder som du kan ange i den `cc` frågeparameter, se [landskoder](#countrycodes).  

|Land/region|Språk|Marknaden kod|  
|---------------------|--------------|-----------------|
|Tyskland|Tyska|de-DE|
|Australien|Engelska|SV-Australien|
|Storbritannien och Nordirland|Engelska|en-GB|
|USA|Engelska|en-US|
|Kanada|Engelska|en CA: N|
|Indien|Engelska|en Indien|
|Frankrike|Franska|fr-FR|
|Kanada|Franska|fr-CA|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Landskoder  

Följande är de koder för land/region som du kan ange i den `cc` frågeparameter. Listan kan ändras.  

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
|Hongkong SAR|HK|  
|Indien|IN|  
|Indonesien|ID|  
|Italien|it|  
|Japan|JP|  
|Korea|KR|  
|Malaysia|MY|  
|Mexico|MX|  
|Nederländerna|NL|  
|Nya Zeeland|NZ|  
|Norge|NEJ|  
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
|Storbritannien och Nordirland|GB|  
|USA|USA|

## <a name="next-steps"></a>Nästa steg
Läs mer om Sök på Bing News-slutpunkter, [nyheter Search API-referens för v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).
