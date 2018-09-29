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
ms.openlocfilehash: 775e098eb2a067e3e0446bccc223c1c54e082347
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435580"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Stöd för språk och din region för Translator Text API

Translator Text API stöder följande språk för översättning till text. Neural maskinöversättning NMT är den nya standarden för AI-driven maskinöversättningar i hög kvalitet och är tillgänglig som standard med V3 av Translator Text API när ett neural system är tillgänglig. Neural maskinöversättning är tillgänglig i V2 med hjälp av kategorin ”generalnn”.

[Mer information om hur maskinöversättning fungerar](https://www.microsoft.com/translator/mt.aspx)

| Språk    | Typ av översättning |Språkkod |
|:----------- |:-------:|:-------------:|
| Afrikaans      | Statistisk |`af`          |
| Arabiska      | Neural | `ar`          |
| Arabiska, Levantine    | Neural | `apc`
| Bangla      | Neural |`bn`          |
| Bosniska (latinsk)      | Statistisk |`bs`          |
| Bulgariska     |  Neural |`bg`          |
| Kantonesiska (traditionell)      | Statistisk |`yue`          |
| Katalanska      | Statistisk |`ca`          |
| Kinesiska, förenklad        |  Neural |`zh-Hans`          |
| Kinesiska, traditionell        |  Neural |`zh-Hant`          |
| Kroatiska      | Neural |`hr`          |
| Tjeckiska        |  Neural |`cs`          |
| Danska        |  Neural |`da`          |
| Nederländska        |  Neural |`nl`          |
| Svenska       |  Neural |`en`          |
| Estniska      | Neural |`et`          |
| Fijianska      | Statistisk |`fj`          |
| Filippinska      | Statistisk |`fil`          |
| Finska      | Neural |`fi`          |
| Franska        |  Neural |`fr`          |
| Tyska       |  Neural |`de`          |
| Grekiska      | Neural |`el`          |
| Haitiska      | Statistisk |`ht`          |
| Hebreiska      | Neural |`he`          |
| Hindi        |  Neural |`hi`          |
| Hmong Daw      | Statistisk |`mww`          |
| Ungerska      | Neural |`hu`          |
| Isländska      |  Neural |`is`           |
| Indonesiska      | Statistisk |`id`          |
| Italienska        |  Neural |`it`          |
| Japanska        |  Neural |`ja`          |
| Kiswahili      | Statistisk |`sw`          |
| Klingon      | Statistisk |`tlh`          |
| Klingon (plqaD)      | Statistisk |`tlh-Qaak`          |
| Koreanska        |  Neural |`ko`          |
| Lettiska      | Neural |`lv`          |
| Litauiska      | Neural |`lt`          |
| Madagaskisk      | Statistisk |`mg`          |
| Malajiska      | Statistisk |`ms`          |
| Maltesiska      | Statistisk |`mt`          |
| Norska        |  Neural |`nb`          |
| Persiska      | Statistisk |`fa`          |
| Polska        |  Neural |`pl`          |
| Portugisiska        |  Neural |`pt`          |
| Queretaro Otomi      | Statistisk |`otq`          |
| Rumänska        |  Neural |`ro`          |
| Ryska        |  Neural |`ru`          |
| Samoa      | Statistisk |`sm`          |
| Serbiska (kyrillisk)      | Statistisk |`sr-Cyrl`          |
| Serbiska (latinsk)      | Statistisk |`sr-Latn`          |
| Slovakiska     | Neural |`sk`          |
| Slovenska      | Neural |`sl`          |
| Spanska        |  Neural |`es`          |
| Svenska        |  Neural |`sv`          |
| Tahitian      | Statistisk |`ty`          |
| Tamilska      | Statistisk |`ta`          |
| Telugu   | Neural   | `te` |
| Thai      | Neural |`th`          |
| Tongan      | Statistisk |`to`          |
| Turkiska       |  Neural |`tr`          |
| Ukrainska      | Neural |`uk`          |
| Urdu      | Statistisk |`ur`          |
| Vietnamesiska      | Neural |`vi`          |
| Walesiska      | Neural |`cy`          |
| Yucatec Maya      | Statistisk |`yua`          |

## <a name="transliteration"></a>Translitteration

Metoden Transliterate stöder följande språk. I den ”till och från”, ”<>--” anger att språket kan vara Translittererad till från eller till någon av de skript som visas. Den ”-->” anger att språket kan bara vara Translittererad till från ett skript till en annan.

| Språk    | Språkkod | Skript | Till och från | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabiska | Kundreskontra | Arabiska | <--> | Latin |
|Bangla  | bn | Bengali | <--> | Latin |
| Förenklad kinesiska | zh-Hans | Kinesiska, förenklad | <--> | Latin |
| Förenklad kinesiska | zh-Hans | Kinesiska, förenklad | <--> | Kinesiska, traditionell |
| Traditionell kinesiska | zh-Hant | Kinesiska, traditionell | <--> | Latin |
| Traditionell kinesiska | zh-Hant | Kinesiska, traditionell | <--> | Kinesiska, förenklad |
| Gujarati | Gu  | Gujarati | --> | Latin |
| Hebreiska | han | Hebreiska | <--> | Latin |
| Hindi | Hej | Devanagari | <--> | Latin |
| Japanska | ja | Japanska | <--> | Latin |
| Kannada | KN | Kannada | --> | Latin |
| Malasian | ml | Malayalam | --> | Latin |
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
