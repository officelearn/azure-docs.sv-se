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
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: a4f9833e8dd14dc7c8ec5849cb809bf2089a5dae
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206131"
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
|Afrikaans| `af`|   Neurala|
|Arabiska|    `ar`    |   Neurala|
|Bangla|    `bn`    |   Neurala|
|Bosniska (latinsk)|   `bs`    |   Neurala|
|Bulgariska| `bg`    |   Neurala|
|Kantonesiska (traditionell)|   `yue`|  Statistikuppgifter|
|Katalanska|   `ca`    |   Statistikuppgifter|
|Kinesiska, förenklad|    `zh-Hans`|Neurala|
|Kinesiska, traditionell|   `zh-Hant`       |Neurala|
|Kroatiska|  `hr`    |Neurala|
|Tjeckiska| `cs`    |   Neurala|
|Danska|    `da`        |Neurala|
|Nederländska| `nl`|   Neurala|
|Svenska|   `en`    |   Neurala|
|Estniska|  `et`    |   Neurala|
|Fijianska|    `fj`    |   Statistikuppgifter|
|Filippinska|  `fil`   |   Statistikuppgifter|
|Finska|   `fi`    |   Neurala|
|Franska|    `fr`    |   Neurala|
|Tyska|    `de`    |   Neurala|
|Grekiska| `el`    |   Neurala|
|Haitiska|    `ht`        |Statistikuppgifter|
|Hebreiska |`he`   |Neurala
|Hindi| `hi`    |   Neurala|
|Hmong Daw| `mww`   |   Statistikuppgifter|
|Ungerska| `hu`    |   Neurala|
|Isländska| `is`    |   Neurala|
|Indonesiska|    `id`    |   Statistikuppgifter|
|Iriska | `ga`| Neurala
|Italienska|   `it`    |   Neurala|
|Japanska|  `ja`    |   Neurala|
|Kannada|`kn`| Neurala
|Kiswahili| `sw`    |   Statistikuppgifter|
|Klingon|   `tlh`   |   Statistikuppgifter|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statistikuppgifter|
|Koreanska |`ko`   |   Neurala|
|Lettiska|   `lv`    |   Neurala|
|Litauiska|    `lt`    |   Neurala|
|Madagaskisk|  `mg`    |   Statistikuppgifter|
|Malajiska| `ms`        |Statistikuppgifter|
|Malayalam| `ml` | Neurala
|Maltesiska|   `mt`    |   Statistikuppgifter|
|Maori| `mi`  | Neurala|
|Norska| `nb`    |   Neurala|
|Persiska|   `fa`    |   Neurala|
|Polska|    `pl`    |   Neurala|
|Portugisiska (Brasilien)|   `pt-br` |   Neurala|
|Portugisiska (Portugal)| `pt-pt` | Neurala
|Punjabi|`pa`|Neurala
|Queretaro Otomi|   `otq`   |   Statistikuppgifter|
|Rumänska|  `ro`    |   Neurala|
|Ryska|   `ru`    |   Neurala|
|Samoa|    `sm`    |   Statistikuppgifter|
|Serbiska (kyrillisk)|    `sr-Cyrl`|  Statistikuppgifter|
|Serbiska (latinsk)|   `sr-Latn`       |Statistikuppgifter|
|Slovakiska|    `sk`    |   Neurala|
|Slovenska| `sl`    |   Neurala|
|Spanska|   `es`    |   Neurala|
|Svenska|   `sv`    |Neurala|
|Tahitian|  `ty`    |Statistikuppgifter|
|Tamilska| `ta`    |   Neurala|
|Telugu|    `te`    |   Neurala|
|Thai|  `th`    |   Neurala|
|Tongan|    `to`    |   Statistikuppgifter|
|Turkiska|   `tr`        |Neurala|
|Ukrainska| `uk`    |   Neurala|
|Urdu|  `ur`    |   Statistikuppgifter|
|Vietnamesiska|    `vi`    |   Neurala|
|Walesiska| `cy`    |   Neurala|
|Yucatec Maya|  `yua`   |   Statistikuppgifter|

> [!NOTE]
> Språk koden `pt` används som standard `pt-br`, portugisiska (Brasilien).

## <a name="transliteration"></a>Transkriberingsspråk

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
| Kannada | `kn` | Kannada-`Knda` | --> | Latinska `Latn` |
| Malayalam | `ml` | Malayalam-`Mlym` | --> | Latinska `Latn` |
| Marathi | `mr` | Devanagari `Deva` | --> | Latinska `Latn` |
| Odia | `or` | Odia `Orya` | <--> | Latinska `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latinska `Latn`  |
| Serbiska (kyrillisk) | `sr-Cyrl` | Kyrilliska `Cyrl`  | --> | Latinska `Latn` |
| Serbiska (latinsk) | `sr-Latn` | Latinska `Latn` | --> | Kyrilliska `Cyrl`|
| Tamilska | `ta` | Tamil `Taml` | --> | Latinska `Latn` |
| Telugu | `te` | Telugu-`Telu` | --> | Latinska `Latn` |
| Thai | `th` | Thai-`Thai` | --> | Latinska `Latn` |

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
