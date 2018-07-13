---
title: Länder/regioner och språk som stöds för Bing Web Search API på Azure | Microsoft Docs
description: Ta reda på vilka länder/regioner och språk som stöds av Bing Web Search API.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: e1994ddf3bf71d01adeac4ff8688bdd1e6eac660
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001726"
---
# <a name="bing-web-search-countriesregions-and-languages"></a>Webbsökning i Bing länder/regioner och språk

Bing Web Search API stöder mer än tre dussin länder/regioner, många med flera olika språk. Ange land/region med en fråga används främst för att begränsa sökresultaten baserat på intresse i landet/regionen. Dessutom resultaten kan innehålla länkar till Bing och dessa länkar kan lokalisera Bing användarupplevelsen enligt den angivna land/region eller språk.

Du kan ange ett land/region med hjälp av den `cc` frågeparameter. Om du anger ett land/region, måste du även ange en eller flera språkkoder med hjälp av den `Accept-Language` HTTP-huvud. Språk som stöds varierar beroende på land/region de tilldelas för varje land/region i tabellen marknader.

Du kan också ange en marknad med hjälp av den `mkt` parameter och en kod från den **marknader** tabell. Ange en marknad samtidigt anger land/region och ett önskat språk. Den `setLang` Frågeparametern kan anges till en språkkod i det här fallet; detta är vanligtvis samma språk som anges av `mkt` såvida inte du föredrar att se Bing på ett annat språk.

## <a name="countries"></a>Länder/regioner

|Land/region|Kod|
|-------|----|
|Argentina|KUNDRESKONTRA|
|Australien|AUSTRALIEN|
|Österrike|AT|
|Belgien|VARA|
|Brasilien|BR|
|Kanada|CA|
|Chile|CL|
|Danmark|DK|
|Finland|FI|
|Frankrike|FR|
|Tyskland|TYSKLAND|
|Hongkong SAR|HK|
|Indien|INDIEN|
|Indonesien|ID|
|Italien|IT|
|Japan|JP|
|Korea|KOREA|
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
|Sydafrika|ZA|
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
|Taiwan SAR|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|TR-TR|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|USA|Spanska|ES-USA|
