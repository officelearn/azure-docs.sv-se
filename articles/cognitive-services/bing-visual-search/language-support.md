---
title: Stöd för språk - API i Bing Visual Search
titleSuffix: Azure Cognitive Services
description: En lista över naturligt språk, länder och regioner som stöds av Bing Visual Search API. Bing Visual Search API stöder mer än tre dussin länder/regioner, många med flera olika språk.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 1dd434bbf71e482f5d0a52b3c84e46382079b43a
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886407"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Stöd för språk och din region för API i Bing Visual Search

Bing Visual Search API stöder mer än tre dussin länder/regioner, många med flera olika språk. Varje begäran måste innehålla användarens land/region och språk. Att känna till användarens marknaden hjälper Bing returnera rätt resultat. Om du inte anger ett land/region och språk, gör Bing bästa förmåga att avgöra användarens land/region och språk. Eftersom resultatet kan innehålla länkar till Bing, kan att känna till land/region och språk ge en önskad lokaliserade Bing användarupplevelse om användaren klickar på länkarna i Bing.

Ange land/region och språk genom att ange den `mkt` (marknaden)-frågeparameter som en kod från den **marknader** tabellen nedan. Marknaden anger både ett land/region och språk. Om du föredrar att se visa text i ett annat språk genom att ange `setLang` frågeparameter i koden för språket.

Du kan också ange land/region med hjälp av den `cc` frågeparameter. Om du anger ett land/region, måste du även ange en eller flera språkkoder med hjälp av den `Accept-Language` HTTP-huvud. Språk som stöds varierar beroende på land/region de tilldelas för varje land/region i tabellen marknader.



> [!NOTE]
> Följande marknaden begränsningar gäller:
>
> - Bild av anteckningar finns bara på engelska.
> - Recept shopping och sidor inklusive insights finns endast en-US-marknaden.


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
