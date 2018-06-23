---
title: Språk som stöds i Microsofts översättare Speech API | Microsoft Docs
description: Visa språk som stöds av Microsoft översättare tal-API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 0d33033442a012290baa78d80f1b8bde0499b3f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355659"
---
# <a name="languages-supported-by-the-microsoft-translator-speech-api"></a>Språk som stöds av Microsoft översättare tal-API
Följande språk stöds för översättning för tal. Om både språk som stöds för tal-översättning tal tal eller tal till text är tillgänglig. Om mål-språk inte stöds för översättning av tal, är endast tal text översättning tillgänglig. 

| Talspråk    |
|:----------- |
| Arabiska (moderna Standard)      |
| Kinesiska (Mandarin)      |
| Svenska      |
| Franska      |
| Tyska      |
| Italienska      |
| Japanska      |
| Portugisiska (Brasilien)     |
| Ryska      |
| Spanska      | 

Microsoft översättare tal-API stöder följande språk som mål språk för taligenkänning översättning av text. 

| Textens språk    | Språkkod |
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
| Malagassiska      | `mg`          |
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

## <a name="access-the-list-programmatically"></a>Programmässig åtkomst i listan

Du kan komma åt listan över språk som stöds genom programmering med resursen språk. Listan innehåller språkkoden samt språknamn på engelska eller något annat språk som stöds. Den här listan uppdateras automatiskt av tjänsten Microsoft Translator när nya språk blir tillgängliga.

Resursen språk returnerar lista med språk som stöds för tal, text och text till tal. Språk-resurs kräver inte autentisering.

[Besök API-referens för att testa metoden språk](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Komma åt listan på webbplatsen Microsoft Translator

Webbplatsen Microsoft Translator visar alla språk som stöds av översättare Text och tal-API: er för en titt på språk. Den här listan innehåller inte developer-specifik information, till exempel språkkoder.

[Visa en lista över språk](https://www.microsoft.com/translator/languages.aspx) 
