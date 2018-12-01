---
title: Språkstöd – Translator Text API
titleSuffix: Azure Cognitive Services
description: En lista med naturligt språk som stöds av Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jansko
ms.openlocfilehash: 0b1187083c14fc7c536f6a32f3a41957f53f299b
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679723"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Stöd för språk och din region för Translator Text API

Translator Text API stöder följande språk för översättning till text. Neural maskinöversättning NMT är den nya standarden för AI-driven maskinöversättningar i hög kvalitet och är tillgänglig som standard med V3 av Translator Text API när ett neural system är tillgänglig. 

[Mer information om hur maskinöversättning fungerar](https://www.microsoft.com/translator/mt.aspx)

**V2 Translator API**

> [!NOTE]
> V2 upphörde den 30 April 2018 och kommer att upphöra den 30 April 2019.

* Statistisk endast: inga neural system är tillgänglig för det här språket.
* Neural tillgängliga: en neural system är tillgänglig. Använd parametern `category=generalnn` till neural systemet.
* Neural standard: Neural är standard translation system. Använd parametern `category=smt` till statistiska systemet för användning med Microsoft Translator Hub.
* Endast Neural: endast neural översättning är tillgänglig.

**V3 Translator API** V3 Translator API är neural som standard och statistiska system är bara tillgängliga när det finns inga neural system. Anpassade Translator kan bara användas med neural språk. 

|Språk|  Språkkod|  V2 API| V3-API|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Statistisk endast|  Neural|
|Arabiska|    `ar`    |Neural tillgängliga|  Neural|
|Arabiska, Levantine| `apc`   |Neural tillgängliga|  Neural|
|Bangla|    `bn`    |Neural tillgängliga|  Neural|
|Bosniska (latinsk)|   `bs`    |Statistisk endast|  Statistisk|
|Bulgariska| `bg`    |Neural tillgängliga|  Neural|
|Kantonesiska (traditionell)|   `yue`   |Statistisk endast|  Statistisk|
|Katalanska|   `ca`    |Statistisk endast|  Statistisk|
|Kinesiska, förenklad|    `zh-Hans`   |Neural standard |Neural|
|Kinesiska, traditionell|   `zh-Hant`   |Neural standard |Neural|
|Kroatiska|  `hr`    |Neural tillgängliga|  Neural|
|Tjeckiska| `cs`    |Neural tillgängliga|  Neural|
|Danska|    `da`    |Neural tillgängliga   |Neural|
|Nederländska| `nl`    |Neural tillgängliga|  Neural|
|Svenska|   `en`    |Neural tillgängliga|  Neural|
|Estniska|  `et`    |Neural tillgängliga|  Neural|
|Fijianska|    `fj`    |Statistisk endast|  Statistisk|
|Filippinska|  `fil`   |Statistisk endast|  Statistisk|
|Finska|   `fi`    |Neural tillgängliga|  Neural|
|Franska|    `fr`    |Neural tillgängliga|  Neural|
|Tyska|    `de`    |Neural tillgängliga|  Neural|
|Grekiska| `el`    |Neural tillgängliga|  Neural|
|Haitiska|    `ht`    |Statistisk endast   |Statistisk|
|Hebreiska |`he`   |Neural tillgängliga   |Neural|
|Hindi| `hi`    |Neural standard|    Neural|
|Hmong Daw| `mww`   |Statistisk endast|  Statistisk|
|Ungerska| `hu`    |Neural tillgängliga|  Neural|
|Isländska| `is`    |Endast Neural|   Neural|
|Indonesiska|    `id`    |Statistisk endast|  Statistisk|
|Italienska|   `it`    |Neural tillgängliga|  Neural|
|Japanska|  `ja`    |Neural tillgängliga|  Neural|
|Kiswahili| `sw`    |Statistisk endast|  Statistisk|
|Klingon|   `tlh`   |Statistisk endast|  Statistisk|
|Klingon (plqaD)|   `tlh-Qaak`  |Statistisk endast|  Statistisk|
|Koreanska |`ko`   |Neural tillgängliga|  Neural|
|Lettiska|   `lv`    |Neural tillgängliga|  Neural|
|Litauiska|    `lt`    |Neural tillgängliga|  Neural|
|Madagaskisk|  `mg`    |Statistisk endast|  Statistisk|
|Malajiska| `ms`    |Statistisk endast   |Statistisk|
|Maltesiska|   `mt`    |Statistisk endast|  Statistisk|
|Norska| `nb`    |Neural tillgängliga|  Neural|
|Persiska|   `fa`    |Statistisk endast|  Statistisk|
|Polska|    `pl`    |Neural tillgängliga|  Neural|
|Portugisiska|    `pt`    |Neural tillgängliga|  Neural|
|Queretaro Otomi|   `otq`   |Statistisk endast|  Statistisk|
|Rumänska|  `ro`    |Neural tillgängliga|  Neural|
|Ryska|   `ru`    |Neural tillgängliga|  Neural|
|Samoa|    `sm`    |Statistisk endast|  Statistisk|
|Serbiska (kyrillisk)|    `sr-Cyrl`   |Statistisk endast|  Statistisk|
|Serbiska (latinsk)|   `sr-Latn`   |Statistisk endast   |Statistisk|
|Slovakiska|    `sk`    |Neural tillgängliga|  Neural|
|Slovenska| `sl`    |Neural tillgängliga|  Neural|
|Spanska|   `es`    |Neural tillgängliga|  Neural|
|Svenska|   `sv`    |Neural tillgängliga   |Neural|
|Tahitian|  `ty`    |Statistisk endast|  Statistisk|
|Tamilska| `ta`    |Statistisk endast|  Statistisk|
|Telugu|    `te`    |Endast Neural|   Neural|
|Thai|  `th`    |Neural tillgängliga|  Neural|
|Tongan|    `to`    |Statistisk endast|  Statistisk|
|Turkiska|   `tr`    |Neural tillgängliga   |Neural|
|Ukrainska| `uk`    |Neural tillgängliga|  Neural|
|Urdu|  `ur`    |Statistisk endast|  Statistisk|
|Vietnamesiska|    `vi`    |Neural tillgängliga|  Neural|
|Walesiska| `cy`    |Neural tillgängliga|  Neural|
|Yucatec Maya|  `yua`   |Statistisk endast|  Statistisk|

## <a name="transliteration"></a>Translitteration

Metoden Transliterate stöder följande språk. I den ”till och från”, ”<>--” anger att språket kan vara Translittererad till från eller till någon av de skript som visas. Den ”-->” anger att språket kan bara vara Translittererad till från ett skript till en annan.

| Språk    | Språkkod | Skript | Till och från | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabiska | Kundreskontra | Arabiska | <--> | Latin |
|Bangla  | bn | Bengali | <--> | Latin |
| Förenklad kinesiska | zh Hans | Kinesiska, förenklad | <--> | Latin |
| Förenklad kinesiska | zh Hans | Kinesiska, förenklad | <--> | Kinesiska, traditionell |
| Traditionell kinesiska | zh-Hant | Kinesiska, traditionell | <--> | Latin |
| Traditionell kinesiska | zh-Hant | Kinesiska, traditionell | <--> | Kinesiska, förenklad |
| Gujarati | Gu  | Gujarati | --> | Latin |
| Hebreiska | han | Hebreiska | <--> | Latin |
| Hindi | Hej | Devanagari | <--> | Latin |
| Japanska | ja | Japanska | <--> | Latin |
| Kannada | KN | Kannada | --> | Latin |
| Malayalam | ml | Malayalam | --> | Latin |
| Marathi | MR | Devanagari | --> | Latin |
| Odia | eller | Odia | <--> | Latin |
| Punjabi | pa | Gurmukhi | <--> | Latin  |
| Serbiska (kyrillisk) | SR-Cyrl | Kyrillisk  | --> | Latin |
| Serbiska (latinsk) | SR-Latn | Latin | --> | Kyrillisk |
| Tamilska | ta | Tamilska | --> | Latin |
| Telugu | te | Telugu | --> | Latin |
| Thai | :e | Thai | <--> | Latin |

## <a name="dictionary"></a>Ordlista

Ordlistan stöder följande språk till eller från engelska med hjälp av Sök- och exempelmetoder.

| Språk    | Språkkod |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabiska       | `ar`          |
| Bangla      | `bn`          |
| Bosniska (latinsk)      | `bs`          |
| Bulgariska      | `bg`          |
| Katalanska      | `ca`          |
| Kinesiska, förenklad      | `zh-Hans`          |
| Kroatiska      | `hr`          |
| Tjeckiska      | `cs`          |
| Danska      | `da`          |
| Nederländska      | `nl`          |
| Estniska      | `et`          |
| Finska      | `fi`          |
| Franska      | `fr`          |
| Tyska      | `de`          |
| Grekiska      | `el`          |
| Haitiska      | `ht`          |
| Hebreiska      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Ungerska      | `hu`          |
| Isländska    | `is`  |
| Indonesiska      | `id`          |
| Italienska      | `it`          |
| Japanska      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Koreanska      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
| Malajiska      | `ms`          |
| Maltesiska      | `mt`          |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska      | `pt`          |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
| Serbiska (latinsk)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Svenska      | `sv`          |
| Tamilska      | `ta`          |
| Thai      | `th`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Urdu      | `ur`          |
| Vietnamesiska      | `vi`          |
| Walesiska      | `cy`          |

## <a name="languages-detected-by-the-detect-method"></a>Språk som identifieras av metoden identifiering

Följande språk kan identifieras av metoden identifiering. Identifiera maj identifiera språk som Microsoft Translator inte kan översätta.

| Språk    |
|:----------- |
| Afrikaans |
| Albanska |
| Arabiska |
| Baskiska |
| Vitryska |
| Bulgariska |
| Katalanska |
| Kinesiska |
| Förenklad kinesiska |
| Traditionell kinesiska |
| Kroatiska |
| Tjeckiska |
| Danska |
| Nederländska |
| Svenska |
| Esperanto |
| Estniska |
| Finska |
| Franska |
| Galiciska |
| Tyska |
| Grekiska |
| Haitiska |
| Hebreiska |
| Hindi |
| Ungerska |
| Isländska |
| Indonesiska |
| Iriska |
| Italienska |
| Japanska |
| Koreanska |
| Kurdiska (arabiska) |
| Kurdiska (latinsk) |
| Latin |
| Lettiska |
| Litauiska |
| Makedonska |
| Malajiska |
| Maltesiska |
| Norska |
| Norska (nynorsk) |
| Afghanska |
| Persiska |
| Polska |
| Portugisiska |
| Rumänska |
| Ryska |
| Serbiska (kyrillisk) |
| Serbiska (latinsk) |
| Slovakiska |
| Slovenska |
| Somali |
| Spanska |
| Swahili |
| Svenska |
| Tagalog |
| Telugu |
| Thai |
| Turkiska |
| Ukrainska |
| Urdu |
| Uzbekiska (kyrillisk) |
| Uzbekiska (latinsk) |
| Vietnamesiska |
| Walesiska |
| Jiddish |

## <a name="access-the-list-programmatically"></a>Programmässig åtkomst i listan

Du kan komma åt listan över språk som stöds via programmering med språk driften av Text-API version 3.0. Du kan visa listan efter funktionen, språkkod samt språkets namn på engelska eller något annat språk som stöds. Den här listan uppdateras automatiskt av tjänsten Microsoft Translator när nya språk blir tillgängliga.

[Visa referensdokumentation för språk-åtgärden](reference/v3-0-languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Komma åt listan på webbplatsen Microsoft Translator

Webbplatsen Microsoft Translator visar alla språk som stöds av Translator Text och tal-API: er för en snabb titt på språk. Den här listan innehåller inte developer-specifik information, till exempel språkkoder.

[Se en lista över språk](https://www.microsoft.com/translator/languages.aspx)
