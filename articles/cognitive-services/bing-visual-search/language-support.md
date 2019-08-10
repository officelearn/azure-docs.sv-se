---
title: Språk stöd – API för visuell sökning i Bing
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk, länder och regioner som stöds av API för visuell sökning i Bing. API för visuell sökning i Bing har stöd för fler än tre dussin länder/regioner, många med fler än ett språk.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883552"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Språk-och region stöd för API för visuell sökning i Bing

API för visuell sökning i Bing stöder fler än tre dussin länder/regioner, många med fler än ett språk. Varje begäran bör innehålla användarens land/region och valfritt språk. Att känna till användarens marknad hjälper Bing att returnera lämpliga resultat. Om du inte anger något land/region och språk, gör Bing det bästa arbetet med att fastställa användarens land/region och språk. Eftersom resultaten kan innehålla länkar till Bing, kan det vara en prioriterad lokaliserad Bing-användare om användaren klickar på Bing-länkarna om du vet att landet/regionen och språket har en prioriterad lokaliserad Bing-upplevelse

Om du vill ange land/region och språk ställer du `mkt` in Frågeparametern (marknads) till en kod från tabellen **marknader** nedan. Marknaden anger både land/region och språk. Om användaren vill se visnings text på ett annat språk anger `setLang` du Frågeparametern till rätt språkkod.

Alternativt kan du ange land/region med `cc` Frågeparametern. Om du anger ett land/en region måste du också ange en eller flera språk koder med `Accept-Language` HTTP-huvudet. Vilka språk som stöds varierar beroende på land/region; de anges för varje land i tabellen marknad.



> [!NOTE]
> Följande marknads begränsningar gäller:
>
> - Bild igenkännings anteckningar finns bara på engelska.
> - Recept, shopping och sidor – inklusive insikter finns bara på en-amerikanska marknaden.


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
