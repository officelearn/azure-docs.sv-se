---
title: Länder/regioner och språk som stöds för nyhetssökning i Bing på Azure | Microsoft Docs
description: Ta reda på vilka länder/regioner och språk som stöds av den bildsökning i Bing.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 12/20/2017
ms.author: v-gedod
ms.openlocfilehash: 1cdacc82b680407814ff2d88e8ed43deacfb17d0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001668"
---
# <a name="bing-news-search-countriesregions-and-languages"></a>Sök på Bing News länder/regioner och språk

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
|Australien|Svenska|SV-Australien|
|Kanada|Svenska|en CA: N|
|Storbritannien|Svenska|en-GB|
|Indonesien|Svenska|SV-ID|
|Irland|Svenska|en IE|
|Indien|Svenska|en Indien|
|Malaysia|Svenska|en min|
|Nya Zeeland|Svenska|en NZ|
|Filippinerna|Svenska|en f|
|Singapore|Svenska|en SG|
|USA|Svenska|sv-SE|
|Svenska|Allmänt|en WW|
|Svenska|Allmänt|en XA|
|Sydafrika|Svenska|en ZA|
|Argentina|Spanska|ES AR|
|Chile|Spanska|ES – CL|
|Spanien|Spanska|es-ES|
|Mexiko|Spanska|es-MX|
|USA|Spanska|ES-USA| 
|Spanska|Allmänt|ES-XL|
|Finland|Finska|fi-FI|  
|Frankrike|Franska|fr-vara|
|Kanada|Franska|fr-CA| 
|Belgien|Nederländska|NL-vara|
|Schweiz|Franska|fr CH|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|IT-IT| 
|Hongkong SAR|Traditionell kinesiska|zh-HK|  
|Taiwan SAR|Traditionell kinesiska|zh-TW|
|Japan|Japanska|ja-JP|  
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
|Australien|Svenska|SV-Australien|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|Svenska|Allmänt|en WW|
|Chile|Spanska|ES – CL|
|Mexiko|Spanska|es-MX|
|USA|Spanska|ES-USA| 
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
|Australien|Svenska|SV-Australien|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|Kanada|Svenska|en CA: N|
|Indien|Svenska|en Indien|
|Frankrike|Franska|fr-FR|
|Kanada|Franska|fr-CA|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Landskoder  

Följande är de koder för land/region som du kan ange i den `cc` frågeparameter. Listan kan ändras.  
  
|Land/region|Landskod|  
|---------------------|------------------|  
|Argentina|KUNDRESKONTRA|  
|Australien|AUSTRALIEN|  
|Österrike|AT|  
|Belgien|VARA|  
|Brasilien|BR|  
|Kanada|CA|  
|Chile|CL|  
|Danmark|DK|  
|Finland|FI|  
|Frankrike|FR|  
|Tyskland|TYSKLAND|  
|Hongkong SAR|HK|  
|Indien|INDIEN|  
|Indonesien|ID|  
|Italien|IT|  
|Japan|JP|  
|Korea|KOREA|  
|Malaysia|MIN|  
|Mexiko|MX|  
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
|Taiwan SAR|TW|  
|Turkiet|TR|  
|Storbritannien|GB|  
|USA|USA|

## <a name="next-steps"></a>Nästa steg
Läs mer om Sök på Bing News-slutpunkter, [nyheter Search API-referens för v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).