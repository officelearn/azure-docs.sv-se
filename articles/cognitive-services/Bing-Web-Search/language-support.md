---
title: Språkstöd - API för webbsökning på Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API:et för sökning i Bing.A list of natural languages, countries and regions that are supported by the Bing News Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882677"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Språk- och regionstöd för API:et för webbsökning på Bing

Api:et för webbsökning på Bing stöder över tre dussin länder eller regioner, många med mer än ett språk. Genom att ange ett land eller en region med en fråga kan sökresultaten förfinas baserat på det eller de ekonomiska intressena. Resultaten kan innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användarupplevelsen enligt det angivna landet/regionen eller språket.

Du kan ange ett land `cc` eller en region med frågeparametern. När ett land eller en region anges måste du ange en eller flera språkkoder med [ `Accept-Language` huvudet](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Använd [tabellen Marknader](#markets) för en lista över språk som stöds på varje marknad.

Du kan också ange marknaden `mkt` med frågeparametern och en kod från tabellen **Marknader.** Om du anger en marknad samtidigt anges ett land eller en region och ett önskat språk. Du kan uttryckligen ange `setLang` språket med frågeparametern.

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

## <a name="next-steps"></a>Nästa steg

* [API-referens för bildsökning i Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
