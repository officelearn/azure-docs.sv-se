---
title: Länder och språk som stöds för Bing Web Sök API i Azure | Microsoft Docs
description: Ta reda på vilka länder och språk som stöds av Bing webb-API för sökning.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 7b62c4a4feb7144662a8fe4d692f11f1efe5db1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351807"
---
# <a name="bing-web-search-countries-and-languages"></a>Bing webbsökning länder och språk

Bing Web Sök API stöder mer än tre 12 länder många med flera olika språk. Ange ett land med en fråga används huvudsakligen för att förfina sökresultat som bygger på verkliga intressen i det landet. Dessutom resultaten kan innehålla länkar till Bing och dessa länkar kan lokalisera Bing användarupplevelsen enligt angivna land eller språk.

Du kan ange ett land som använder den `cc` Frågeparametern. Om du anger ett land, måste du också ange en eller flera språkkoder som använder den `Accept-Language` HTTP-huvud. Språk som stöds varierar beroende på land. de ges för varje land i tabellen marknader.

Du kan också ange marknaden med hjälp av den `mkt` fråga parametern och en kod från de **marknader** tabell. Ange en marknad samtidigt anger ett land och önskat språk. Den `setLang` frågeparameter kan vara en språkkod i det här fallet; oftast är detta samma språk som anges av `mkt` om inte användaren föredrar att visa Bing på ett annat språk.

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
