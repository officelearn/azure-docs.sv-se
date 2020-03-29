---
title: Språkstöd - API för visuell sökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API:et för visuell sökning i Bing. Api:et för visuell sökning i Bing stöder mer än tre dussin länder/regioner, många med mer än ett språk.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883552"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Språk- och regionstöd för API:et för visuell sökning i Bing

Bing Visual Search API stöder mer än tre dussin länder/regioner, många med mer än ett språk. Varje begäran bör innehålla användarens land/region och språkval. Att känna till användarens marknad hjälper Bing att returnera lämpliga resultat. Om du inte anger ett land/en region och ett språk gör Bing sitt bästa för att bestämma användarens land/region och språk. Eftersom resultaten kan innehålla länkar till Bing kan vetskap om land/region och språk ge en önskad lokaliserad Bing-användarupplevelse om användaren klickar på Bing-länkarna.

Om du vill ange land/region `mkt` och språk anger du frågeparametern (marknad) till en kod i tabellen **Marknader** nedan. Marknaden anger både ett land/en region och ett språk. Om användaren föredrar att visa visningstext på `setLang` ett annat språk anger du frågeparametern till lämplig språkkod.

Du kan också ange land/region `cc` med frågeparametern. Om du anger ett land/en region måste du också `Accept-Language` ange en eller flera språkkoder med HTTP-huvudet. Språken som stöds varierar mellan olika länder och regioner. de anges för varje land i tabellen Marknader.



> [!NOTE]
> Följande marknadsrestriktioner gäller:
>
> - Bildigenkänningsanteckningar är endast tillgängliga på engelska.
> - Recept, shopping och sidor, inklusive insikter, är endast tillgängliga på marknaden i USA.


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


## <a name="markets"></a>Marknader

|Land/region|Språk|Marknadskod|
|-------|--------|-----------|
|Argentina|Spanska|es-AR|
|Australien|Svenska|sv-AU|
|Österrike|Tyska|av-AT|
|Belgien|Nederländska|nl-BE|
|Belgien|Franska|fr-BE|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Svenska|sv-CA|
|Kanada|Franska|fr-CA|
|Chile|Spanska|es-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Hongkong|Traditionell kinesiska|zh-HK|
|Indien|Svenska|sv-IN|
|Indonesien|Svenska|sv-ID|
|Italien|Italienska|it-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreansk|ko-KR|
|Malaysia|Svenska|sv-MIN|
|Mexiko|Spanska|es-MX|
|Nederländerna|Nederländska|nl-NL|
|Nya Zeeland|Svenska|sv-NZ|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|pt-PT|
|Filippinerna|Svenska|sv-PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Svenska|sv-ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|sv-SE|
|Schweiz|Franska|fr-CH|
|Schweiz|Tyska|de-CH (olika)|
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|tr-TR|
|Storbritannien|Svenska|en-GB|
|USA|Svenska|sv-SE|
|USA|Spanska|es-USA|
