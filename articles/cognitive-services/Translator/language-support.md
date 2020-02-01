---
title: Språk stöd – Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API stöder följande språk för text översättning med hjälp av NMT (neurala Machine Translation).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 12/02/2019
ms.author: swmachan
ms.openlocfilehash: 2ec8d389c99ad96e59bf49d4345855fa44d6c7aa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901999"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Språk-och region stöd för Translator Text API

Translator Text API har stöd för följande språk för text till text översättning. Neurala Machine Translation (NMT) är den nya standarden för AI-drivna dator översättningar med hög kvalitet och är tillgänglig som standard med hjälp av v3 av den Translator Text API när ett neurala-system är tillgängligt.

[Lär dig mer om hur dator översättning fungerar](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Översättning

**V2 Translator-API**

> [!NOTE]
> V2 föråldrades den 30 april 2018. Migrera dina program till v3 för att kunna dra nytta av nya funktioner som är tillgängliga exklusivt i v3.

* Endast statistisk: inget neurala system är tillgängligt för det här språket.
* Neurala tillgängligt: ett neurala-system är tillgängligt. Använd parametern `category=generalnn` för att få åtkomst till neurala-systemet.
* Neurala standard: neurala är standard översättnings systemet. Använd parametern `category=smt` för att få åtkomst till statistik systemet för användning med Microsoft Translator Hub.
* Endast neurala: endast översättning av neurala är tillgängligt.

**V3 Translator-API** V3-Translator-API: et är neurala som standard och statistik system är bara tillgängliga när det inte finns något neurala-system.

> [!NOTE]
> För närvarande är en delmängd av neurala-språken tillgängliga i en anpassad översättare och vi lägger gradvis till ytterligare. [Visa språk som för närvarande är tillgängliga i en anpassad översättare](#customization).

|Språk|  Språkkod|  V3-API|
|:-----|:-----:|:-----|
|Afrikaans| `af`|   Neural|
|Arabiska|    `ar`    |   Neural|
|Bangla|    `bn`    |   Neural|
|Bosniska (latinsk)|   `bs`    |   Neural|
|Bulgariska| `bg`    |   Neural|
|Kantonesiska (traditionell)|   `yue`|  Statistikuppgifter|
|Katalanska|   `ca`    |   Statistikuppgifter|
|Kinesiska, förenklad|    `zh-Hans`|Neural|
|Kinesiska, traditionell|   `zh-Hant`       |Neural|
|Kroatiska|  `hr`    |Neural|
|Tjeckiska| `cs`    |   Neural|
|Danska|    `da`        |Neural|
|Nederländska| `nl`|   Neural|
|Svenska|   `en`    |   Neural|
|Estniska|  `et`    |   Neural|
|Fijianska|    `fj`    |   Statistikuppgifter|
|Filippinska|  `fil`   |   Statistikuppgifter|
|Finska|   `fi`    |   Neural|
|Franska|    `fr`    |   Neural|
|Tyska|    `de`    |   Neural|
|Grekiska| `el`    |   Neural|
|Haitiska|    `ht`        |Statistikuppgifter|
|Hebreiska |`he`   |Neural
|Hindi| `hi`    |   Neural|
|Hmong Daw| `mww`   |   Statistikuppgifter|
|Ungerska| `hu`    |   Neural|
|Isländska| `is`    |   Neural|
|Indonesiska|    `id`    |   Statistikuppgifter|
|Iriska | `ga`| Neural
|Italienska|   `it`    |   Neural|
|Japanska|  `ja`    |   Neural|
|Kannada|`kn`| Neural
|Kiswahili| `sw`    |   Statistikuppgifter|
|Klingon|   `tlh`   |   Statistikuppgifter|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statistikuppgifter|
|Koreanska |`ko`   |   Neural|
|Lettiska|   `lv`    |   Neural|
|Litauiska|    `lt`    |   Neural|
|Madagaskisk|  `mg`    |   Statistikuppgifter|
|Malajiska| `ms`        |Statistikuppgifter|
|Malayalam| `ml` | Neural
|Maltesiska|   `mt`    |   Statistikuppgifter|
|Maori| `mi`  | Neural|
|Norska| `nb`    |   Neural|
|Persiska|   `fa`    |   Neural|
|Polska|    `pl`    |   Neural|
|Portugisiska (Brasilien)|   `pt-br` |   Neural|
|Portugisiska (Portugal)| `pt-pt` | Neural
|Punjabi|`pa`|Neural
|Queretaro Otomi|   `otq`   |   Statistikuppgifter|
|Rumänska|  `ro`    |   Neural|
|Ryska|   `ru`    |   Neural|
|Samoa|    `sm`    |   Statistikuppgifter|
|Serbiska (kyrillisk)|    `sr-Cyrl`|  Statistikuppgifter|
|Serbiska (latinsk)|   `sr-Latn`       |Statistikuppgifter|
|Slovakiska|    `sk`    |   Neural|
|Slovenska| `sl`    |   Neural|
|Spanska|   `es`    |   Neural|
|Svenska|   `sv`    |Neural|
|Tahitian|  `ty`    |Statistikuppgifter|
|Tamilska| `ta`    |   Neural|
|Telugu|    `te`    |   Neural|
|Thai|  `th`    |   Neural|
|Tongan|    `to`    |   Statistikuppgifter|
|Turkiska|   `tr`        |Neural|
|Ukrainska| `uk`    |   Neural|
|Urdu|  `ur`    |   Statistikuppgifter|
|Vietnamesiska|    `vi`    |   Neural|
|Walesiska| `cy`    |   Neural|
|Yucatec Maya|  `yua`   |   Statistikuppgifter|

> [!NOTE]
> Språk koden `pt` används som standard `pt-br`, portugisiska (Brasilien).

## <a name="transliteration"></a>Translitteration

Metoden translittererad stöder följande språk. I "till/från", "<-->" anger att språket kan överföras från eller till något av de angivna skripten. "-->" Anger att språket bara kan vara translittererad från ett skript till ett annat.

| Språk    | Språkkod | Skript | Till/från | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabiska | `ar` | Arabisk `Arab` | <--> | Latinska `Latn` |
|Bangla  | `bn` | Bengali-`Beng` | <--> | Latinska `Latn` |
| Förenklad kinesiska | `zh-Hans` | Förenklad kinesiska `Hans`| <--> | Latinska `Latn` |
| Förenklad kinesiska | `zh-Hans` | Förenklad kinesiska `Hans`| <--> | Traditionell kinesiska `Hant`|
| Traditionell kinesiska | `zh-Hant` | Traditionell kinesiska `Hant`| <--> | Latinska `Latn` |
| Traditionell kinesiska | `zh-Hant` | Traditionell kinesiska `Hant`| <--> | Förenklad kinesiska `Hans` |
| Gujarati | `gu`  | Gujarati-`Gujr` | --> | Latinska `Latn` |
| Hebreiska | `he` | Hebreisk `Hebr` | <--> | Latinska `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latinska `Latn` |
| Japanska | `ja` | Japansk `Jpan` | <--> | Latinska `Latn` |
| Kannada | `kn` | Kannada `Knda` | --> | Latinska `Latn` |
| Malayalam | `ml` | Malayalam-`Mlym` | --> | Latinska `Latn` |
| Marathi | `mr` | Devanagari `Deva` | --> | Latinska `Latn` |
| Odia | `or` | Odia `Orya` | <--> | Latinska `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latinska `Latn`  |
| Serbiska (kyrillisk) | `sr-Cyrl` | Kyrilliska `Cyrl`  | --> | Latinska `Latn` |
| Serbiska (latinsk) | `sr-Latn` | Latinska `Latn` | --> | Kyrilliska `Cyrl`|
| Tamilska | `ta` | Tamil `Taml` | --> | Latinska `Latn` |
| Telugu | `te` | Telugu-`Telu` | --> | Latinska `Latn` |
| Thai | `th` | Thai-`Thai` | <--> | Latinska `Latn` |

## <a name="dictionary"></a>Ordlista

Ord listan har stöd för följande språk till eller från engelska med hjälp av Sök-och exempel metoderna.

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
| Portugisiska (Brasilien)     | `pt-br`          |
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

## <a name="detect"></a>Upptäcka

Translator Text API identifierar alla språk som är tillgängliga för översättning och transkriberingsspråk.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Få åtkomst till Translator Text API språk lista program mässigt

Du kan hämta en lista över språk som stöds för Translator Text API v 3.0 med hjälp av språk metoden. Du kan visa listan efter funktion, språkkod, språk kod och språk namn på engelska eller andra språk som stöds. Den här listan uppdateras automatiskt av tjänsten Microsoft Translator när nya språk görs tillgängliga.

[Visa referens dokumentation för språk åtgärder](reference/v3-0-languages.md)

## <a name="customization"></a>Anpassning

Följande språk är tillgängliga för anpassning till eller från engelska med hjälp av en [anpassad översättare](https://aka.ms/CustomTranslator).

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
| Koreanska      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
| Madagaskisk| `mg`    |
| Maori| `mi`  |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska (Brasilien) | `pt-br` |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
| Samoa|   `sm`    |
| Serbiska (latinsk)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Svenska      | `sv`          |
| Thai      | `th`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Vietnamesiska      | `vi`          |
| Walesiska | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Öppna listan på webbplatsen för Microsoft Translator

För en snabb titt på språken visar webbplatsen Microsoft Translator alla språk som stöds av API: erna Translator Text och tal. Den här listan innehåller inte information som är specifik för utvecklare, till exempel språk koder.

[Se listan över språk](https://www.microsoft.com/translator/languages.aspx)
