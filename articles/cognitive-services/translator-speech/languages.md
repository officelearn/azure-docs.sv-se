---
title: Språk som stöds – Translator Speech API
titlesuffix: Azure Cognitive Services
description: Visa språk som stöds av Translator Speech API.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2f5b48f2dbc2d109c03613676c6a119fd971603b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965432"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Språk som stöds av Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Följande språk stöds för tal översättning. Om båda språken stöds för tal översättning är tal till tal eller tal till text tillgängligt. Om mål språket inte stöds för tal översättning är det bara tal till text översättning som är tillgängligt.

| Talspråk    |
|:----------- |
| Arabiska (moderna Standard)      |
| Portugisiska (Brasilien)     |
| Kinesiska (Mandarin)      |
| Svenska      |
| Franska      |
| Tyska      |
| Italienska      |
| Japanska      |
| Ryska      |
| Spanska      |

Translator Speech API har stöd för följande språk som ett mål språk för tal till text översättning.

| Språk    | Språkkod |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabiska       | `ar`          |
| Bangla      | `bn`          |
| Bosniska (latinsk)      | `bs`          |
| Bulgariska      | `bg`          |
| Kantonesiska (traditionell)      | `yue`          |
| Katalanska      | `ca`          |
| Kinesiska, förenklad      | `zh-Hans`          |
| Kinesiska, traditionell      | `zh-Hant`          |
| Kroatiska      | `hr`          |
| Tjeckiska      | `cs`          |
| Danska      | `da`          |
| Nederländska      | `nl`          |
| Svenska      | `en`          |
| Estniska      | `et`          |
| Fijianska      | `fj`          |
| Filippinska      | `fil`          |
| Finska      | `fi`          |
| Franska      | `fr`          |
| Tyska      | `de`          |
| Grekiska      | `el`          |
| Haitiska      | `ht`          |
| Hebreiska      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Ungerska      | `hu`          |
|Isländska|`is`          |
| Indonesiska      | `id`          |
| Italienska      | `it`          |
| Japanska      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Koreanska      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
| Madagaskisk      | `mg`          |
| Malajiska      | `ms`          |
| Maltesiska      | `mt`          |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
| Samoa      | `sm`          |
| Serbiska (kyrillisk)      | `sr-Cyrl`          |
| Serbiska (latinsk)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Svenska      | `sv`          |
| Tahitian      | `ty`          |
| Tamilska      | `ta`          |
| Thai      | `th`          |
| Tongan      | `to`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Urdu      | `ur`          |
| Vietnamesiska      | `vi`          |
| Walesiska      | `cy`          |
| Yucatec Maya      | `yua`          |

## <a name="access-the-list-programmatically"></a>Få åtkomst till listan via programmering

Du kan komma åt listan över språk som stöds via programmering med hjälp av språk resursen. Listan innehåller språk koden samt språkets namn på engelska eller något annat språk som stöds. Den här listan uppdateras automatiskt av Translator Speech tjänsten när nya språk blir tillgängliga.

Språk resursen returnerar listan över språk som stöds för tal, text och text till tal. Språk resursen kräver inte autentisering.

[Besök API-referensen för att prova språk metoden](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Öppna listan på webbplatsen för Microsoft Translator

För en snabb titt på språken visar webbplatsen Microsoft Translator alla språk som stöds av API: erna Translator Text och tal. Den här listan innehåller inte information som är specifik för utvecklare, till exempel språk koder.

[Se listan över språk](https://www.microsoft.com/translator/languages.aspx)
