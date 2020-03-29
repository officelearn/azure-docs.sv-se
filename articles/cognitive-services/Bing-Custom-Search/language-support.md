---
title: Språkstöd - API för anpassad sökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över språk och regioner som stöds för API:et för anpassad sökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "66388567"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Språk- och regionstöd för API:et för anpassad sökning i Bing

Api:et för anpassad sökning i Bing stöder mer än tre dussin länder/regioner, många med mer än ett språk.

Även om det är valfritt bör begäran ange [parametern mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) query, som identifierar den marknad där du vill att resultaten ska komma från. En lista över valfria frågeparametrar finns i [Frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Du kan ange ett land/en region med frågeparametern. `cc` Om du anger ett land/en region måste du också `Accept-Language` ange en eller flera språkkoder med hjälp av huvudet. Språken som stöds varierar mellan olika länder och regioner. de anges för varje land/region i tabellen **Marknader.**

Huvudet `Accept-Language` och `setLang` frågeparametern utesluter varandra – ange inte båda. Mer information finns i [Acceptera språk](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

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
|Hongkong, SAR|Traditionell kinesiska|zh-HK|
|Indien|Svenska|sv-IN|
|Indonesien|Svenska|sv-ID|
|Italien|Italienska|it-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreansk|ko-KR|
|Malaysia|Svenska|sv-MIN|
|Mexiko|Spanska|es-MX|
|Nederländerna|Nederländska|nl-NL|
|Nya Zeeland|Svenska|sv-NZ|
|Norge|Norska|No-no|
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
