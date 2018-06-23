---
title: Länder och språk som stöds för Bing anpassad sökning API i Azure | Microsoft Docs
description: Ta reda på vilka länder och språk som stöds av Bing anpassad sökning API.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/19/2017
ms.author: v-gedod
ms.openlocfilehash: 7ff309f9b789662c4ebd791dffaa2bc2e440763e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352890"
---
# <a name="bing-custom-search-countries-and-languages"></a>Bing anpassad sökning länder och språk

API: et för Bing anpassad sökning stöder mer än tre 12 länder många med flera olika språk. 

Men det är valfritt, begäran måste ange den [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) frågeparameter som identifierar marknaden där du vill att resultaten från. En lista med valfria parametrar finns [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Du kan ange ett land som använder den `cc` Frågeparametern. Om du anger ett land, måste du också ange en eller flera språkkoder som använder den `Accept-Language` rubrik. Språk som stöds varierar beroende på land. de ges för varje land i den **marknader** tabell.

Den `Accept-Language` sidhuvud och `setLang` frågeparameter är ömsesidigt uteslutande – inte ange båda. Mer information finns i [acceptera språk](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countries"></a>Land

|Land/region|Kod|
|-------|----|
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
|Kina|CN|
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


## <a name="markets"></a>Marknader

|Land/region|Språk|Marknaden kod|
|-------|--------|-----------|
|Argentina|Spanska|ES AR|
|Australien|Svenska|SV Australien|
|Österrike|Tyska|Tyskland AT|
|Belgien|Nederländska|NL-vara|
|Belgien|Franska|fr-vara|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Svenska|en Certifikatutfärdare|
|Kanada|Franska|fr-CA|
|Chile|Spanska|ES-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Hongkong SAR|Traditionell kinesiska|zh-HK|
|Indien|Svenska|SV IN|
|Indonesien|Svenska|SV-ID|
|Italien|Italienska|IT-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreanska|ko-KR|
|Malaysia|Svenska|en Mina|
|Mexiko|Spanska|es-MX|
|Nederländerna|Nederländska|NL-NL|
|Nya Zeeland|Svenska|SV NZ|
|Norge|Norska|no-NO|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|PT-PT|
|Filippinerna|Svenska|SV PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Svenska|SV-ÖA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|SV-SE|
|Schweiz|Franska|fr CH|
|Schweiz|Tyska|Tyskland CH|
|Taiwan SAR|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|TR-TR|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|USA|Spanska|ES-USA|
