---
title: 'Stöd för språk - API: et för Bing Custom Search'
titleSuffix: Azure Cognitive Services
description: 'En lista över språk som stöds och regioner för API: et för Bing Custom Search.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 56870a63f42c10b48cc2d8f0ae2995862be46d8f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790246"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Språk och din region stöd för API: et för Bing Custom Search

API: et för Bing Custom Search har stöd för fler än tre dussin länder/regioner, många med flera olika språk.

Men det är valfritt, begäran bör ange den [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) frågeparameter som identifierar marknaden där du vill att resultatet kommer från. En lista över valfria frågeparametrar, se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Du kan ange ett land/region med hjälp av den `cc` frågeparameter. Om du anger ett land/region, måste du även ange en eller flera språkkoder med hjälp av den `Accept-Language` rubrik. Språk som stöds varierar beroende på land/region de tilldelas för varje land/region i den **marknader** tabell.

Den `Accept-Language` rubrik och `setLang` Frågeparametern är ömsesidigt uteslutande – inte ange båda. Mer information finns i [acceptera språk](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

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
