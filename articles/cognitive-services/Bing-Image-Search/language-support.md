---
title: Språk stöd – API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Ta reda på vilka länder/regioner och språk som stöds av API för bildsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881925"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Språk-och region stöd för API för bildsökning i Bing

API för bildsökning i Bing har stöd för fler än tre dussin länder/regioner, många med fler än ett språk. Om du anger ett land/en region med en fråga, är det främst att förfina Sök resultat baserat på intressen i landet/regionen. Dessutom kan resultaten innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användargränssnittet enligt angivet land/regioner eller språk.

Om du vill ange land/region och språk ställer du `mkt` in Frågeparametern (marknads) till en kod från tabellen **marknader** nedan. Marknaden anger både land/region och språk. Om användaren vill se visnings text på ett annat språk anger `setLang` du Frågeparametern till rätt språkkod.

Alternativt kan du ange land/region med `cc` Frågeparametern. Om du anger ett land/en region måste du också ange en eller flera språk koder med `Accept-Language` HTTP-huvudet. Vilka språk som stöds varierar beroende på land/region; de anges för varje land/region i tabellen marknad.

> [!NOTE]
> API: et för Trends images stöder för närvarande endast följande marknader:
> - en-US (engelska, USA)
> - en-CA (engelska, Kanada)
> - en – AU (engelska, Australien)
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
|Hongkong SAR|HK|
|Indien|IN|
|Indonesien|id|
|Italien|it|
|Japan|JP|
|Korea|KR|
|Malaysia|MY|
|Mexico|MX|
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
|Storbritannien och Nordirland|GB|
|USA|USA|


## <a name="markets"></a>Marknaden

|Land/region|Språk|Marknads kod|
|-------|--------|-----------|
|Argentina|Spanska|es-AR|
|Australien|Svenska|SV-Australien|
|Österrike|Tyska|Tyskland-AT|
|Belgien|Nederländska|nl-BE|
|Belgien|Franska|fr-BE|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Svenska|en CA: N|
|Kanada|Franska|fr-CA|
|Chile|Spanska|es-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Hongkong SAR|Traditionell kinesiska|zh-HK|
|Indien|Svenska|en Indien|
|Indonesien|Svenska|en-ID|
|Italien|Italienska|IT-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreanska|ko-KR|
|Malaysia|Svenska|en-MY|
|Mexico|Spanska|es-MX|
|Nederländerna|Nederländska|NL-NL|
|Nya Zeeland|Svenska|en NZ|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|PT-PT|
|Filippinerna|Svenska|en-PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Svenska|en-ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|SV-SE|
|Schweiz|Franska|fr CH|
|Schweiz|Tyska|Tyskland – CH|
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|TR-TR|
|Storbritannien och Nordirland|Svenska|en-GB|
|USA|Svenska|en-US|
|USA|Spanska|es-US|

## <a name="next-steps"></a>Nästa steg
Mer information om Nyhetssökning i Bing slut punkter finns i [nyheter bildsökning API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
