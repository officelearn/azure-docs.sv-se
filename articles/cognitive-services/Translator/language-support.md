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
ms.openlocfilehash: 0ecde5acb7dc57ed9e5802c1589d5813a9206643
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81684830"
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
|Fijian|    `fj`    |   Statistikuppgifter|
|Filipino|  `fil`   |   Statistikuppgifter|
|Finska|   `fi`    |   Neurala|
|Franska|    `fr`    |   Neurala|
|Tyska|    `de`    |   Neurala|
|Grekiska| `el`    |   Neurala|
|Gujarati|  `gu`    |   Neurala|
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
|Swahili| `sw`    |   Statistikuppgifter|
|Klingon|   `tlh`   |   Statistikuppgifter|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statistikuppgifter|
|Koreansk |`ko`   |   Neurala|
|Lettiska|   `lv`    |   Neurala|
|Litauiska|    `lt`    |   Neurala|
|Madagaskisk|  `mg`    |   Statistikuppgifter|
|Malajiska| `ms`        |Statistikuppgifter|
|Malayalam| `ml` | Neurala
|Maltesiska|   `mt`    |   Statistikuppgifter|
|Maori| `mi`  | Neurala|
|Marathi| `mr`  | Neurala|
|Norska| `nb`    |   Neurala|
|Persiska|   `fa`    |   Neurala|
|Polska|    `pl`    |   Neurala|
|Portugisiska (Brasilien)|   `pt-br` |   Neurala|
|Portugisiska (Portugal)| `pt-pt` | Neurala
|Punjabi|`pa`|Neurala
|Queretaro Otomi|   `otq`   |   Statistikuppgifter|
|Rumänska|  `ro`    |   Neurala|
|Ryska|   `ru`    |   Neurala|
|Samoan|    `sm`    |   Statistikuppgifter|
|Serbiska (kyrillisk)|    `sr-Cyrl`|  Statistikuppgifter|
|Serbiska (latinsk) (Serbien)|   `sr-Latn`       |Statistikuppgifter|
|Slovakiska|    `sk`    |   Neurala|
|Slovenska| `sl`    |   Neurala|
|Spanska|   `es`    |   Neurala|
|Svenska|   `sv`    |Neurala|
|Tahitian|  `ty`    |Statistikuppgifter|
|Tamilska| `ta`    |   Neurala|
|Telugu|    `te`    |   Neurala|
|Thailändska|  `th`    |   Neurala|
|Tonganska|    `to`    |   Statistikuppgifter|
|Turkiska|   `tr`        |Neurala|
|Ukrainska| `uk`    |   Neurala|
|Urdu|  `ur`    |   Statistikuppgifter|
|Vietnamesiska|    `vi`    |   Neurala|
|Walesiska| `cy`    |   Neurala|
|Yucatec Maya|  `yua`   |   Statistikuppgifter|

> [!NOTE]
> Språk koden `pt` är som `pt-br`standard portugisiska (Brasilien).

## <a name="transliteration"></a>Transkriberingsspråk

Metoden translittererad stöder följande språk. I "till/från", "<-->" anger att språket kan överföras från eller till något av de angivna skripten. "-->" anger att språket bara kan vara translittererad från ett skript till ett annat.

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
| Odia | `or` | Odia`Orya` | <--> | Latin`Latn` |
| Punjabi | `pa` | Gurmukhi`Guru`  | <--> | Latin`Latn`  |
| Serbiska (kyrillisk) | `sr-Cyrl` | Kyrillisk`Cyrl`  | --> | Latin`Latn` |
| Serbiska (latinsk) (Serbien) | `sr-Latn` | Latin`Latn` | --> | Kyrillisk`Cyrl`|
| Tamilska | `ta` | Tamilska`Taml` | --> | Latin`Latn` |
| Telugu | `te` | Telugu`Telu` | --> | Latin`Latn` |
| Thailändska | `th` | Thai`Thai` | --> | Latin`Latn` |

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
| Swahili      | `sw`          |
| Klingon      | `tlh`          |
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
| Swahili|    `sw`    |
| Koreansk      | `ko`          |
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
| Samoan|   `sm`    |
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

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Öppna listan på webbplatsen för Microsoft Translator

För en snabb titt på språken visar webbplatsen Microsoft Translator alla språk som stöds av API: erna Translator Text och tal. Den här listan innehåller inte information som är specifik för utvecklare, till exempel språk koder.

[Se listan över språk](https://www.microsoft.com/translator/languages.aspx)
