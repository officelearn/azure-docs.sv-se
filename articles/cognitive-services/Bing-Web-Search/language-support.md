---
title: Stöd för språk - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturligt språk, länder och regioner som stöds av den nyhetssökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: e657c4678c76e8ff667c1a3f30409fc157f52d16
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798249"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Stöd för språk och din region för Bing Web Search API

Bing Web Search API har stöd för över tre dussin länder eller regioner många med flera olika språk. Ange ett land eller region med en fråga kan du förfina sökresultaten baserat på de land eller regioner intressen. Resultaten kan innehålla länkar till Bing och dessa länkar kan lokalisera Bing användarupplevelsen enligt den angivna land/region eller språk.

Du kan ange ett land eller region med hjälp av den `cc` frågeparameter. När du anger ett land eller region måste du ange en eller flera språkkoder med den [ `Accept-Language` rubrik](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Använd den [marknader tabell](#markets) en lista över språk som stöds i varje marknad.

Du kan också ange på marknaden med den `mkt` Frågeparametern och en kod från den **marknader** tabell. Ange en marknad samtidigt anger ett land / region och önskat språk. Du kan uttryckligen ange språk med den `setLang` frågeparameter.

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
|Indonesien|ID|
|Italien|it|
|Japan|JP|
|Korea|KR|
|Malaysia|MY|
|Mexico|MX|
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
|Taiwan|TW|
|Turkiet|TR|
|Storbritannien och Nordirland|GB|
|USA|USA|

## <a name="markets"></a>Marknader

|Land/region|Språk|Marknaden kod|
|-------|--------|-----------|
|Argentina|Spanska|es-AR|
|Australien|Engelska|SV-Australien|
|Österrike|Tyska|Tyskland-AT|
|Belgien|Nederländska|nl-BE|
|Belgien|Franska|fr-BE|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Engelska|en CA: N|
|Kanada|Franska|fr-CA|
|Chile|Spanska|es-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Hongkong SAR|Traditionell kinesiska|zh-HK|
|Indien|Engelska|en Indien|
|Indonesien|Engelska|en-ID|
|Italien|Italienska|IT-IT|
|Japan|japanska|ja-JP|
|Korea|Koreanska|ko-KR|
|Malaysia|Engelska|en-MY|
|Mexico|Spanska|es-MX|
|Nederländerna|Nederländska|NL-NL|
|Nya Zeeland|Engelska|en NZ|
|Norge|Norska (bokmål)|no-NO|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|PT-PT|
|Filippinerna|Engelska|en-PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Engelska|en ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|SV-SE|
|Schweiz|Franska|fr CH|
|Schweiz|Tyska|Tyskland – CH|
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|TR-TR|
|Storbritannien och Nordirland|Engelska|en-GB|
|USA|Engelska|en-US|
|USA|Spanska|es-US|

## <a name="next-steps"></a>Nästa steg

* [API-referens för bildsökning i Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
