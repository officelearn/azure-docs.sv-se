---
title: Stöd för språk - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturligt språk, länder och regioner som stöds av den nyhetssökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 6d7b227ac182ca2cd30d6a6eaa428ea86757214c
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164408"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Stöd för språk och din region för Bing Web Search API

Bing Web Search API har stöd för över tre dussin länder eller regioner många med flera olika språk. Ange ett land eller region med en fråga kan du förfina sökresultaten baserat på de land eller regioner intressen. Resultaten kan innehålla länkar till Bing och dessa länkar kan lokalisera Bing användarupplevelsen enligt den angivna land/region eller språk.

Du kan ange ett land eller region med hjälp av den `cc` frågeparameter. När du anger ett land eller region måste du ange en eller flera språkkoder med den [ `Accept-Language` rubrik](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Använd den [marknader tabell](#Markets) en lista över språk som stöds i varje marknad.

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
|Norge|Norska|no-NO|
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
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|TR-TR|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|USA|Spanska|ES-USA|
