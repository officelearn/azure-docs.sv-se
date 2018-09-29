---
title: Stöd för språk - bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Ta reda på vilka länder/regioner och språk som stöds av den bildsökning i Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: db9e1acec881a182b111cb1c913da607e4f8311a
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435576"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Språk och din region stöd för sökning i Bing

Sökning i Bing stöder mer än tre dussin länder/regioner, många med flera olika språk. Ange land/region med en fråga används främst för att begränsa sökresultaten baserat på intresse i landet/regionen. Dessutom resultaten kan innehålla länkar till Bing och dessa länkar kan lokalisera Bing användarupplevelsen enligt den angivna länder/regioner eller språk.

Ange land/region och språk genom att ange den `mkt` (marknaden)-frågeparameter som en kod från den **marknader** tabellen nedan. Marknaden anger både ett land/region och språk. Om du föredrar att se visa text i ett annat språk genom att ange `setLang` frågeparameter i koden för språket.

Du kan också ange land/region med hjälp av den `cc` frågeparameter. Om du anger ett land/region, måste du även ange en eller flera språkkoder med hjälp av den `Accept-Language` HTTP-huvud. Språk som stöds varierar beroende på land/region de tilldelas för varje land/region i tabellen marknader.

> [!NOTE]
> API: et för populära bilder stöder för närvarande endast följande marknader:
> - en-US (engelska, USA)
> - en – USA (engelska, Kanada)
> - en AU (engelska, Australien)
> - zh-CN (kinesiska, Kina)

## <a name="countries"></a>Länder/regioner

|Land/region|Kod|
|-------|----|
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
|Kina|CN|
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


## <a name="markets"></a>Marknader

|Land/region|Språk|Marknaden kod|
|-------|--------|-----------|
|Argentina|Spanska|ES AR|
|Australien|Svenska|SV-Australien|
|Österrike|Tyska|Tyskland-AT|
|Belgien|Nederländska|NL-vara|
|Belgien|Franska|fr-vara|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Svenska|en CA: N|
|Kanada|Franska|fr-CA|
|Chile|Spanska|ES – CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Hongkong SAR|Traditionell kinesiska|zh-HK|
|Indien|Svenska|en Indien|
|Indonesien|Svenska|SV-ID|
|Italien|Italienska|IT-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreanska|ko-KR|
|Malaysia|Svenska|en min|
|Mexiko|Spanska|es-MX|
|Nederländerna|Nederländska|NL-NL|
|Nya Zeeland|Svenska|en NZ|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|PT-PT|
|Filippinerna|Svenska|en f|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Svenska|en ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|SV-SE|
|Schweiz|Franska|fr CH|
|Schweiz|Tyska|Tyskland – CH|
|Taiwan SAR|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|TR-TR|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|USA|Spanska|ES-USA|

## <a name="next-steps"></a>Nästa steg
Läs mer om Sök på Bing News-slutpunkter, [nyheter Image Search API v7 referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
