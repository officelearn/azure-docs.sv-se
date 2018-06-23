---
title: Länder och språk som stöds för Bing Visual Sök API | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ta reda på vilka länder och språk som stöds av Bing Visual Sök-API.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4723d028cc22caf8be3eb294b52506ec112cbab5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354141"
---
# <a name="bing-visual-search-countries-and-languages"></a>Bing Visual Sök länder och språk

Bing Visual Sök API: et stöder mer än tre 12 länder många med flera olika språk. Varje begäran måste innehålla användarens land och språk. Känna till användarens marknaden hjälper Bing rätt resultat returneras. Om du inte anger ett land och språk, gör Bing en bästa för att fastställa användarens land och språk. Eftersom resultaten kan innehålla länkar till Bing, kan känna till land och språk ge en önskad lokaliserade Bing användarupplevelse om användaren klickar på Bing-länkar.

Ange land och språk, ange den `mkt` (marknaden)-frågeparameter som en kod från de **marknader** tabellen nedan. Marknaden anger både land och språk. Om du föredrar att se visa text i ett annat språk kan du ange `setLang` Frågeparametern om koden för rätt språk.

Du kan också ange land med hjälp av den `cc` Frågeparametern. Om du anger ett land, måste du också ange en eller flera språkkoder som använder den `Accept-Language` HTTP-huvud. Språk som stöds varierar beroende på land. de ges för varje land i tabellen marknader.



> [!NOTE]
> Följande marknaden begränsningar gäller:
> 
> - Bild recognition anteckningar finns endast på engelska. 
> - Recept shopping och sidor inklusive insikter finns på en-US marknaden. 


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
