---
title: Språk stöd – API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API för nyhetssökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882677"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Språk-och region stöd för API för webbsökning i Bing

API för webbsökning i Bing har stöd för över tre dussin länder eller regioner, många med fler än ett språk. Genom att ange ett land eller en region med en fråga kan du förfina Sök resultat baserat på dessa länder eller regioner. Resultaten kan innehålla länkar till Bing, och dessa länkar kan lokalisera Bing-användargränssnittet enligt angivet land/region eller språk.

Du kan ange ett land eller en region med `cc` hjälp av Frågeparametern. När ett land eller en region har angetts måste du ange en eller flera språk koder med [ `Accept-Language` rubriken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Använd [tabellen marknader](#markets) för att visa en lista över språk som stöds på varje marknad.

Alternativt kan du ange marknaden med `mkt` Frågeparametern och en kod från tabellen **marknader** . Att ange en marknad samtidigt anger ett land eller en region och ett önskat språk. Du kan uttryckligen ange språket med `setLang` Frågeparametern.

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
|Norge|Norska|ingen – nej|
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

* [API-referens för bildsökning i Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
