---
title: Språkstöd - Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API stöder följande språk för text till text översättning med neurala maskinöversättning (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: a4f9833e8dd14dc7c8ec5849cb809bf2089a5dae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77206131"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Språk- och regionstöd för Translator Text API

Translator Text API stöder följande språk för text till text översättning. Neural Machine Translation (NMT) är den nya standarden för högkvalitativa AI-drivna maskinöversättningar och är tillgänglig som standard med V3 av Translator Text API när ett neuralt system är tillgängligt.

[Läs mer om hur maskinöversättning fungerar](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Översättning

**V2 Översättare API**

> [!NOTE]
> V2 inaktuella den 30 april 2018. Migrera dina program till V3 för att dra nytta av nya funktioner som är tillgängliga exklusivt i V3.

* Endast statistiskt: Inget neuralt system är tillgängligt för detta språk.
* Neural tillgänglig: Ett neuralt system är tillgängligt. Använd parametern `category=generalnn` för att komma åt det neurala systemet.
* Neural standard: Neurala är standardöversättningssystemet. Använd parametern `category=smt` för att komma åt det statistiska systemet för användning med Microsoft Translator Hub.
* Neural endast: Endast neural översättning är tillgänglig.

**V3 Översättare API** V3 Translator API är neurala som standard och statistiska system är endast tillgängliga när det inte finns något neuralt system.

> [!NOTE]
> För närvarande finns en delmängd av de neurala språken i Custom Translator och vi lägger gradvis till ytterligare. [Visa språk som för närvarande är tillgängliga i Custom Translator](#customization).

|Språk|  Språkkod|  V3-API|
|:-----|:-----:|:-----|
|Afrikaans| `af`|   Neurala|
|Arabiska|    `ar`    |   Neurala|
|Bangla|    `bn`    |   Neurala|
|Bosniska (latinsk)|   `bs`    |   Neurala|
|Bulgariska| `bg`    |   Neurala|
|Kantonesiska (traditionell)|   `yue`|  Statistiska|
|Katalanska|   `ca`    |   Statistiska|
|Kinesiska, förenklad|    `zh-Hans`|Neurala|
|Kinesiska, traditionell|   `zh-Hant`       |Neurala|
|Kroatiska|  `hr`    |Neurala|
|Tjeckiska| `cs`    |   Neurala|
|Danska|    `da`        |Neurala|
|Nederländska| `nl`|   Neurala|
|Svenska|   `en`    |   Neurala|
|Estniska|  `et`    |   Neurala|
|Fijianska|    `fj`    |   Statistiska|
|Filipino|  `fil`   |   Statistiska|
|Finska|   `fi`    |   Neurala|
|Franska|    `fr`    |   Neurala|
|Tyska|    `de`    |   Neurala|
|Grekiska| `el`    |   Neurala|
|Haitiska|    `ht`        |Statistiska|
|Hebreiska |`he`   |Neurala
|Hindi| `hi`    |   Neurala|
|Hmong Daw| `mww`   |   Statistiska|
|Ungerska| `hu`    |   Neurala|
|Isländska| `is`    |   Neurala|
|Indonesiska|    `id`    |   Statistiska|
|Iriska | `ga`| Neurala
|Italienska|   `it`    |   Neurala|
|Japanska|  `ja`    |   Neurala|
|Kannada|`kn`| Neurala
|Kiswahili| `sw`    |   Statistiska|
|Klingonska|   `tlh`   |   Statistiska|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statistiska|
|Koreansk |`ko`   |   Neurala|
|Lettiska|   `lv`    |   Neurala|
|Litauiska|    `lt`    |   Neurala|
|Madagaskiska|  `mg`    |   Statistiska|
|Malajiska| `ms`        |Statistiska|
|Malayalam| `ml` | Neurala
|Maltesiska|   `mt`    |   Statistiska|
|Maori| `mi`  | Neurala|
|Norska| `nb`    |   Neurala|
|Persiska|   `fa`    |   Neurala|
|Polska|    `pl`    |   Neurala|
|Portugisiska (Brasilien)|   `pt-br` |   Neurala|
|Portugisiska (Portugal)| `pt-pt` | Neurala
|Punjabi|`pa`|Neurala
|Queretaro Otomi|   `otq`   |   Statistiska|
|Rumänska|  `ro`    |   Neurala|
|Ryska|   `ru`    |   Neurala|
|Samoanska|    `sm`    |   Statistiska|
|Serbiska (kyrillisk)|    `sr-Cyrl`|  Statistiska|
|Serbiska (latinsk) (Serbien)|   `sr-Latn`       |Statistiska|
|Slovakiska|    `sk`    |   Neurala|
|Slovenska| `sl`    |   Neurala|
|Spanska|   `es`    |   Neurala|
|Svenska|   `sv`    |Neurala|
|Tahitian|  `ty`    |Statistiska|
|Tamilska| `ta`    |   Neurala|
|Telugu|    `te`    |   Neurala|
|Thailändska|  `th`    |   Neurala|
|Tongan (ton)|    `to`    |   Statistiska|
|Turkiska|   `tr`        |Neurala|
|Ukrainska| `uk`    |   Neurala|
|Urdu|  `ur`    |   Statistiska|
|Vietnamesiska|    `vi`    |   Neurala|
|Walesiska| `cy`    |   Neurala|
|Yucatec Maya|  `yua`   |   Statistiska|

> [!NOTE]
> Språkkoden `pt` kommer `pt-br`som standard att , portugisiska (Brasilien).

## <a name="transliteration"></a>Transliteration

Translitteratmetoden stöder följande språk. I "Till/Från" anger "< –->" att språket kan translittereras från eller till något av de listade skripten. "-->" anger att språket endast kan translittereras från ett skript till ett annat.

| Språk    | Språkkod | Skript | Till/från | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabiska | `ar` | Arabiska`Arab` | <--> | Latin`Latn` |
|Bangla  | `bn` | Bengali`Beng` | <--> | Latin`Latn` |
| Kinesiska (förenklad) | `zh-Hans` | Kinesiska, förenklad`Hans`| <--> | Latin`Latn` |
| Kinesiska (förenklad) | `zh-Hans` | Kinesiska, förenklad`Hans`| <--> | Kinesiska, traditionell`Hant`|
| Kinesiska (traditionell) | `zh-Hant` | Kinesiska, traditionell`Hant`| <--> | Latin`Latn` |
| Kinesiska (traditionell) | `zh-Hant` | Kinesiska, traditionell`Hant`| <--> | Kinesiska, förenklad`Hans` |
| Gujarati | `gu`  | Gujarati`Gujr` | --> | Latin`Latn` |
| Hebreiska | `he` | Hebreiska`Hebr` | <--> | Latin`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Latin`Latn` |
| Japanska | `ja` | Japanska`Jpan` | <--> | Latin`Latn` |
| Kannada | `kn` | Kannada`Knda` | --> | Latin`Latn` |
| Malayalam | `ml` | Malayalam`Mlym` | --> | Latin`Latn` |
| Marathi | `mr` | Devanagari`Deva` | --> | Latin`Latn` |
| Oriya | `or` | Oriya`Orya` | <--> | Latin`Latn` |
| Punjabi | `pa` | Gurmukhi (på andra sätt)`Guru`  | <--> | Latin`Latn`  |
| Serbiska (kyrillisk) | `sr-Cyrl` | Kyrilliska`Cyrl`  | --> | Latin`Latn` |
| Serbiska (latinsk) (Serbien) | `sr-Latn` | Latin`Latn` | --> | Kyrilliska`Cyrl`|
| Tamilska | `ta` | Tamilska`Taml` | --> | Latin`Latn` |
| Telugu | `te` | Telugu`Telu` | --> | Latin`Latn` |
| Thailändska | `th` | Thai`Thai` | --> | Latin`Latn` |

## <a name="dictionary"></a>Ordlista

Ordlistan stöder följande språk till eller från engelska med hjälp av metoderna Uppslag och exempel.

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
| Klingonska      | `tlh`          |
| Koreansk      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
| Malajiska      | `ms`          |
| Maltesiska      | `mt`          |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska (Brasilien)     | `pt-br`          |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
| Serbiska (latinsk) (Serbien)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Svenska      | `sv`          |
| Tamilska      | `ta`          |
| Thailändska      | `th`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Urdu      | `ur`          |
| Vietnamesiska      | `vi`          |
| Walesiska      | `cy`          |

## <a name="detect"></a>Upptäcka

Translator Text API identifierar alla språk som är tillgängliga för översättning och translitterering.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Öppna språklistan för translator text API programmässigt

Du kan hämta en lista över språk som stöds för translator text API v3.0 med hjälp av språk metoden. Du kan visa listan efter funktion, språkkod samt språknamnet på engelska eller något annat språk som stöds. Den här listan uppdateras automatiskt av Tjänsten Microsoft Translator när nya språk görs tillgängliga.

[Referensdokumentation för visa språk](reference/v3-0-languages.md)

## <a name="customization"></a>Anpassning

Följande språk är tillgängliga för anpassning till eller från engelska med [anpassad översättare](https://aka.ms/CustomTranslator).

| Språk    | Språkkod |
|:----------- |:-------------:|
| Arabiska       | `ar`          |
| Bangla      | `bn`          |
| Bosniska (latinsk)      | `bs`          |
| Bulgariska      | `bg`          |
| Kinesiska, förenklad      | `zh-Hans`          |
|Kinesiska, traditionell|   `zh-Hant`   |
| Kroatiska      | `hr`          |
| Tjeckiska      | `cs`          |
| Danska      | `da`          |
| Nederländska      | `nl`          |
| Svenska    | `en`     |
| Estniska      | `et`          |
| Finska      | `fi`          |
| Franska      | `fr`          |
| Tyska      | `de`          |
| Grekiska      | `el`          |
| Hebreiska      | `he`          |
| Hindi      | `hi`          |
| Ungerska      | `hu`          |
| Isländska | `is` |
| Indonesiska|   `id`    |
| Iriska | `ga`  |
| Italienska      | `it`          |
| Japanska      | `ja`          |
| Kiswahili|    `sw`    |
| Koreansk      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
| Madagaskiska| `mg`    |
| Maori| `mi`  |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska (Brasilien) | `pt-br` |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
| Samoanska|   `sm`    |
| Serbiska (latinsk) (Serbien)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Svenska      | `sv`          |
| Thailändska      | `th`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Vietnamesiska      | `vi`          |
| Walesiska | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Få tillgång till listan på webbplatsen Microsoft Translator

För en snabb titt på språken visar webbplatsen Microsoft Translator alla språk som stöds av translatorns text- och tal-API:er. Den här listan innehåller inte utvecklarspecifik information, till exempel språkkoder.

[Se listan över språk](https://www.microsoft.com/translator/languages.aspx)
