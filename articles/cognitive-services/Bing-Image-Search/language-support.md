---
title: Språkstöd - API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Ta reda på vilka länder/regioner och språk som stöds av API:et för bildsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881925"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Språk- och regionstöd för API:et för bildsökning i Bing

API:et för bildsökning på Bing stöder mer än tre dussin länder/regioner, många med mer än ett språk. Om du anger ett land/en region med en fråga används i första hand för att förfina sökresultaten baserat på intressen i det landet/regionen. Dessutom kan resultaten innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användarupplevelsen enligt det angivna landet/regionerna eller språket.

Om du vill ange land/region `mkt` och språk anger du frågeparametern (marknad) till en kod i tabellen **Marknader** nedan. Marknaden anger både ett land/en region och ett språk. Om användaren föredrar att visa visningstext på `setLang` ett annat språk anger du frågeparametern till lämplig språkkod.

Du kan också ange land/region `cc` med frågeparametern. Om du anger ett land/en region måste du också `Accept-Language` ange en eller flera språkkoder med HTTP-huvudet. Språken som stöds varierar mellan olika länder och regioner. de anges för varje land/region i tabellen Marknader.

> [!NOTE]
> Api:et för populära bilder stöder för närvarande endast följande marknader:
> - sv-US (engelska, USA)
> - sv-CA (Engelska, Kanada)
> - sv-AU (engelska, Australien)
> - zh-CN (kinesiska, Kina)

## <a name="countriesregions"></a>Länder/regioner

|Land/region|Kod|
|-------|----|
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
|Taiwan|TW|
|Turkiet|TR|
|Storbritannien|GB|
|USA|USA|


## <a name="markets"></a>Marknader

|Land/region|Språk|Marknadskod|
|-------|--------|-----------|
|Argentina|Spanska|es-AR|
|Australien|Svenska|sv-AU|
|Österrike|Tyska|av-AT|
|Belgien|Nederländska|nl-BE|
|Belgien|Franska|fr-BE|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Svenska|sv-CA|
|Kanada|Franska|fr-CA|
|Chile|Spanska|es-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Hongkong|Traditionell kinesiska|zh-HK|
|Indien|Svenska|sv-IN|
|Indonesien|Svenska|sv-ID|
|Italien|Italienska|it-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreansk|ko-KR|
|Malaysia|Svenska|sv-MIN|
|Mexiko|Spanska|es-MX|
|Nederländerna|Nederländska|nl-NL|
|Nya Zeeland|Svenska|sv-NZ|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|pt-PT|
|Filippinerna|Svenska|sv-PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Svenska|sv-ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|sv-SE|
|Schweiz|Franska|fr-CH|
|Schweiz|Tyska|de-CH (olika)|
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|tr-TR|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|USA|Spanska|es-USA|

## <a name="next-steps"></a>Nästa steg
Mer information om slutpunkterna För Bing-nyheter finns i [API V7-referens för nyhetsbildsökning.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
