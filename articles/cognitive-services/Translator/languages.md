---
title: Språk som stöds i Microsoft översättare API | Microsoft Docs
description: Visa språk som stöds av Microsoft översättare Text API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 10/30/2017
ms.author: v-jansko
ms.openlocfilehash: f388e9e39809774f307c0d1752e29f34b041ed13
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356401"
---
# <a name="supported-languages-in-the-microsoft-translator-text-api"></a>Språk som stöds i Microsoft översättare Text-API 
Microsoft översättare Text API stöder följande språk för översättning till text. Neural maskinöversättning (NMT) är den nya standarden för hög kvalitet AI-påslagen maskinöversättningar och är tillgänglig som standard med V3 översättare Text API när ett neural system är tillgänglig. Neural maskinöversättning är tillgänglig i V2 med hjälp av kategorin ”generalnn”. 

[Mer information om hur maskinöversättning fungerar](https://www.microsoft.com/translator/mt.aspx)

| Språk    | Översättning av typen |Språkkod |
|:----------- |:-------:|:-------------:|
| Afrikaans      | Statistisk |`af`          |
| Arabiska      | Neural | `ar`          |
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
| Malagassiska      | Statistisk |`mg`          |
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
| Thai      | Neural |`th`          |
| Tongan      | Statistisk |`to`          |
| Turkiska       |  Neural |`tr`          |
| Ukrainska      | Neural |`uk`          |
| Urdu      | Statistisk |`ur`          |
| Vietnamesiska      | Neural |`vi`          |
| Walesiska      | Neural |`cy`          |
| Yucatec Maya      | Statistisk |`yua`          |

## <a name="transliteration"></a>Transkribering

Transliterate-metoden stöder följande språk. I den ”till/från”, ”<>--” indikerar att språket som kan vara Translittererad till från eller till någon av de skript som visas. Den ”-->” anger att språket kan bara vara Translittererad till från ett skript till en annan.

| Språk    | Språkkod | Skript | Till och från | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabiska | ar | Arabiska | <--> | Latin |
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

Ordlistan stöder följande språk till eller från engelska metoderna sökning och exempel. 

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

## <a name="languages-detected-by-the-detect-method"></a>Språk som identifieras av metoden identifiera

Följande språk kan identifieras av metoden identifiera. Identifiera kan identifiera språk som Microsoft Translator inte kan översätta. 

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
| Chinese_Simplified |
| Chinese_Traditional |
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
| Haitian_Creole |
| Hebreiska |
| Hindi |
| Ungerska |
| Isländska |
| Indonesiska |
| Iriska |
| Italienska |
| Japanska |
| Koreanska |
| Kurdish_Arabic |
| Kurdish_Latin |
| Latin |
| Lettiska |
| Litauiska |
| Makedonska |
| Malajiska |
| Maltesiska |
| Norska |
| Norwegian_Nynorsk |
| Afghanska |
| Persiska |
| Polska |
| Portugisiska |
| Rumänska |
| Ryska |
| Serbian_Cyrillic |
| Serbian_Latin |
| Slovakiska |
| Slovenska |
| Somali |
| Spanska |
| Swahili |
| Svenska |
| Tagalog |
| Thai |
| Turkiska |
| Ukrainska |
| Urdu |
| Uzbek_Cyrillic |
| Uzbek_Latin |
| Vietnamesiska |
| Walesiska |
| Jiddisch |

## <a name="access-the-list-programmatically"></a>Programmässig åtkomst i listan

Du kan komma åt listan över språk som stöds genom programmering med åtgärden språk V3.0 Text API. Du kan visa listan med funktionen, språkkod samt språkets namn på engelska eller något annat språk som stöds. Den här listan uppdateras automatiskt av tjänsten Microsoft Translator när nya språk blir tillgängliga.

[Språk referensdokumentationen för åtgärden](/reference/languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Komma åt listan på webbplatsen Microsoft Translator

Webbplatsen Microsoft Translator visar alla språk som stöds av översättare Text och tal-API: er för en titt på språk. Den här listan innehåller developer-specifik information, till exempel språkkoder.

[Visa en lista över språk](https://www.microsoft.com/translator/languages.aspx)
