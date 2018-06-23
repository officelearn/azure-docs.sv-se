---
title: Länder och språk som stöds för Bing News Sök API i Azure | Microsoft Docs
description: Ta reda på vilka länder och språk som stöds av Bing avbildningen Sök-API.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 12/20/2017
ms.author: v-gedod
ms.openlocfilehash: 80326d66e509b64efd5d386fe793bc9942b29ae3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351900"
---
# <a name="bing-news-search-countries-and-languages"></a>Bing News Search länder och språk

Bing News Sök API stöder flera länder många med flera olika språk. Ange ett land med en fråga används huvudsakligen för att förfina sökresultat som bygger på verkliga intressen i det landet. Dessutom resultaten kan innehålla länkar till Bing och dessa länkar kan lokalisera Bing användarupplevelsen enligt angivna land eller språk.

Du kan ange ett land som använder den `cc` Frågeparametern. Om du anger ett land, måste du också ange en eller flera språkkoder som använder den `Accept-Language` HTTP-huvud. Språk som stöds varierar beroende på land. de ges för varje land i tabellen marknader.

Du kan också ange marknaden med hjälp av den `mkt` fråga parametern och en kod från de **marknader** tabell. Ange en marknad samtidigt anger ett land och önskat språk. Den `setLang` frågeparameter kan vara en språkkod i det här fallet; oftast är detta samma språk som anges av `mkt` om inte användaren föredrar att visa Bing på ett annat språk.

## <a name="supported-markets-for-news-search-endpoint"></a>Marknader för Nyheter Sök slutpunkt som stöds

För den `/news/search` slutpunkt i följande tabell visas marknaden code-värden som du kan använda för att ange den `mkt` Frågeparametern. Bing returnerar innehåll för endast dessa marknader. Listan kan ändras.  
  
För en lista över land koder som du kan ange i den `cc` Frågeparametern, se [landskoder](#countrycodes).  
  
|Land/region|Språk|Marknaden kod|  
|---------------------|--------------|-----------------| 
|Danmark|Danska|da-DK|
|Österrike|Tyska|Tyskland AT| 
|Schweiz|Tyska|Tyskland CH|
|Tyskland|Tyska|de-DE|
|Australien|Svenska|SV Australien|
|Kanada|Svenska|en Certifikatutfärdare|
|Storbritannien|Svenska|en-GB|
|Indonesien|Svenska|SV-ID|
|Irland|Svenska|SV IE|
|Indien|Svenska|SV IN|
|Malaysia|Svenska|en Mina|
|Nya Zeeland|Svenska|SV NZ|
|Filippinerna|Svenska|SV PH|
|Singapore|Svenska|en SG|
|USA|Svenska|sv-SE|
|Svenska|Allmänt|SV WW|
|Svenska|Allmänt|en XA|
|Sydafrika|Svenska|SV-ÖA|
|Argentina|Spanska|ES AR|
|Chile|Spanska|ES-CL|
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

## <a name="supported-markets-for-news-endpoint"></a>Stöds marknader för Nyheter slutpunkt
För den `/news` slutpunkt i följande tabell visas marknaden code-värden som du kan använda för att ange den `mkt` Frågeparametern. Bing returnerar innehåll för endast dessa marknader. Listan kan ändras.  
  
För en lista över land koder som du kan ange i den `cc` Frågeparametern, se [landskoder](#countrycodes).  
  
|Land/region|Språk|Marknaden kod|  
|---------------------|--------------|-----------------| 
|Danmark|Danska|da-DK|
|Tyskland|Tyska|de-DE|
|Australien|Svenska|SV Australien|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|Svenska|Allmänt|SV WW|
|Chile|Spanska|ES-CL|
|Mexiko|Spanska|es-MX|
|USA|Spanska|ES-USA| 
|Finland|Finska|fi-FI|  
|Kanada|Franska|fr-CA|
|Frankrike|Franska|fr-FR|  
|Italien|Italienska|IT-IT| 
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Marknader för Nyheter trender slutpunkt som stöds
För den `/news/trendingtopics` slutpunkt i följande tabell visas marknaden code-värden som du kan använda för att ange den `mkt` Frågeparametern. Bing returnerar innehåll för endast dessa marknader. Listan kan ändras.  
  
För en lista över land koder som du kan ange i den `cc` Frågeparametern, se [landskoder](#countrycodes).  
  
|Land/region|Språk|Marknaden kod|  
|---------------------|--------------|-----------------| 
|Tyskland|Tyska|de-DE|
|Australien|Svenska|SV Australien|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|Kanada|Svenska|en Certifikatutfärdare|
|Indien|Svenska|SV IN|
|Frankrike|Franska|fr-FR|
|Kanada|Franska|fr-CA|
|Brasilien|Portugisiska|pt-BR|
|Folkrepubliken Kina|Kinesiska|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Landskoder  

Följande är landskoden som du kan ange i den `cc` Frågeparametern. Listan kan ändras.  
  
|Land/region|Landskod|  
|---------------------|------------------|  
|Argentina|AR|  
|Australien|AUSTRALIEN|  
|Österrike|AT|  
|Belgien|ATT|  
|Brasilien|BRASILIEN|  
|Kanada|CA|  
|Chile|CL|  
|Danmark|DK|  
|Finland|FI|  
|Frankrike|FR|  
|Tyskland|TYSKLAND|  
|Hongkong SAR|HK|  
|Indien|I|  
|Indonesien|ID|  
|Italien|IT|  
|Japan|JP|  
|Korea|KR|  
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
|Sydafrika|ÖA|  
|Spanien|ES|  
|Sverige|SE|  
|Schweiz|CH|  
|Taiwan SAR|TW|  
|Turkiet|TR|  
|Storbritannien|GB|  
|USA|USA|

## <a name="next-steps"></a>Nästa steg
Mer information om Bing News Search-slutpunkter finns [nyheter Sök API-referens för v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).