---
title: Språk stöd – API för anpassad Bing-sökning
titleSuffix: Azure Cognitive Services
description: En lista över språk och regioner som stöds för API för anpassad Bing-sökning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "66388567"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Språk-och region stöd för API för anpassad Bing-sökning

API för anpassad Bing-sökning har stöd för fler än tre dussin länder/regioner, många med fler än ett språk.

Även om det är valfritt, bör begäran ange Frågeparametern [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) , som identifierar den marknad där du vill att resultatet ska komma från. En lista med parametrar för valfria frågor finns i [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Du kan ange ett land/en region med `cc` hjälp av Frågeparametern. Om du anger ett land/en region måste du också ange en eller flera språk koder med hjälp `Accept-Language` av rubriken. Vilka språk som stöds varierar beroende på land/region; de anges för varje land/region i tabellen **marknad** .

`Accept-Language` Rubriken och `setLang` frågeparametern är ömsesidigt uteslutande – ange inte båda. Mer information finns i [Accept-language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

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


## <a name="markets"></a>Marknaden

|Land/region|Språk|Marknads kod|
|-------|--------|-----------|
|Argentina|Spanska|ES-AR|
|Australien|Svenska|en – AU|
|Österrike|Tyska|avinstallation|
|Belgien|Nederländska|NL-vara|
|Belgien|Franska|fr-vara|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Svenska|en-CA|
|Kanada|Franska|fr-CA|
|Chile|Spanska|ES-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Hong Kong, SAR|Traditionell kinesiska|zh-HK|
|Indien|Svenska|en-IN|
|Indonesien|Svenska|en-ID|
|Italien|Italienska|it-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreansk|ko-KR|
|Malaysia|Svenska|en-MY|
|Mexiko|Spanska|ES – MX|
|Nederländerna|Nederländska|nl-NL|
|Nya Zeeland|Svenska|en-NZ|
|Norge|Norska|ingen – nej|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|pt-PT|
|Filippinerna|Svenska|en-PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Svenska|en-ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|sv-SE|
|Schweiz|Franska|Frankrike-CH|
|Schweiz|Tyska|de-CH|
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|tr-TR|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|USA|Spanska|ES – USA|
