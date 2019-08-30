---
title: Språk stöd – Translator Text API
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk som stöds av Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be69f18f30b796e7401ef68aa5088153e07f14f5
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127890"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Språk-och region stöd för Translator Text API

Translator Text API har stöd för följande språk för text till text översättning. Neurala Machine Translation (NMT) är den nya standarden för AI-drivna dator översättningar med hög kvalitet och är tillgänglig som standard med hjälp av v3 av den Translator Text API när ett neurala-system är tillgängligt.

[Lär dig mer om hur dator översättning fungerar](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Översättning

**V2 Translator-API**

> [!NOTE]
> V2 föråldrades den 30 april 2018. Migrera dina program till v3 för att kunna dra nytta av nya funktioner som är tillgängliga exklusivt i v3.

* Endast statistisk: Inget neurala system är tillgängligt för det här språket.
* Tillgänglig neurala: Ett neurala system är tillgängligt. Använd parametern `category=generalnn` för att få åtkomst till neurala-systemet.
* Neurala standard: Neurala är standard översättnings systemet. Använd parametern `category=smt` för att få åtkomst till statistik systemet för användning med Microsoft Translator Hub.
* Endast neurala: Endast neurala översättning är tillgängligt.

**V3 Translator-API** V3-Translator-API: et är neurala som standard och statistik system är bara tillgängliga när det inte finns något neurala-system.

> [!NOTE]
> För närvarande är en delmängd av neurala-språken tillgängliga i en anpassad översättare och vi lägger gradvis till ytterligare. [Visa språk som för närvarande är tillgängliga i en anpassad översättare](#customization).

|Språk|  Språkkod|  V2-API| V3-API|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Endast statistik|  Neural|
|Arabiska|    `ar`    |Neurala tillgänglig|  Neural|
|Bangla|    `bn`    |Neurala tillgänglig|  Neural|
|Bosniska (latinsk)|   `bs`    |Neurala tillgänglig|  Neural|
|Bulgariska| `bg`    |Neurala tillgänglig|  Neural|
|Kantonesiska (traditionell)|   `yue`   |Endast statistik|  Statistikuppgifter|
|Katalanska|   `ca`    |Endast statistik|  Statistikuppgifter|
|Kinesiska, förenklad|    `zh-Hans`   |Neurala standard |Neural|
|Kinesiska, traditionell|   `zh-Hant`   |Neurala standard |Neural|
|Kroatiska|  `hr`    |Neurala tillgänglig|  Neural|
|Tjeckiska| `cs`    |Neurala tillgänglig|  Neural|
|Danska|    `da`    |Neurala tillgänglig   |Neural|
|Nederländska| `nl`    |Neurala tillgänglig|  Neural|
|Svenska|   `en`    |Neurala tillgänglig|  Neural|
|Estniska|  `et`    |Neurala tillgänglig|  Neural|
|Fijianska|    `fj`    |Endast statistik|  Statistikuppgifter|
|Filippinska|  `fil`   |Endast statistik|  Statistikuppgifter|
|Finska|   `fi`    |Neurala tillgänglig|  Neural|
|Franska|    `fr`    |Neurala tillgänglig|  Neural|
|Tyska|    `de`    |Neurala tillgänglig|  Neural|
|Grekiska| `el`    |Neurala tillgänglig|  Neural|
|Haitiska|    `ht`    |Endast statistik   |Statistikuppgifter|
|Hebreiska |`he`   |Neurala tillgänglig   |Neural|
|Hindi| `hi`    |Neurala standard|    Neural|
|Hmong Daw| `mww`   |Endast statistik|  Statistikuppgifter|
|Ungerska| `hu`    |Neurala tillgänglig|  Neural|
|Isländska| `is`    |Endast neurala|   Neural|
|Indonesiska|    `id`    |Endast statistik|  Statistikuppgifter|
|Italienska|   `it`    |Neurala tillgänglig|  Neural|
|Japanska|  `ja`    |Neurala tillgänglig|  Neural|
|Kiswahili| `sw`    |Endast statistik|  Statistikuppgifter|
|Klingon|   `tlh`   |Endast statistik|  Statistikuppgifter|
|Klingon (plqaD)|   `tlh-Qaak`  |Endast statistik|  Statistikuppgifter|
|Koreanska |`ko`   |Neurala tillgänglig|  Neural|
|Lettiska|   `lv`    |Neurala tillgänglig|  Neural|
|Litauiska|    `lt`    |Neurala tillgänglig|  Neural|
|Madagaskisk|  `mg`    |Endast statistik|  Statistikuppgifter|
|Malajiska| `ms`    |Endast statistik   |Statistikuppgifter|
|Maltesiska|   `mt`    |Endast statistik|  Statistikuppgifter|
|Norska| `nb`    |Neurala tillgänglig|  Neural|
|Persiska|   `fa`    |Neurala tillgänglig|  Neural|
|Polska|    `pl`    |Neurala tillgänglig|  Neural|
|Portugisiska|    `pt`    |Neurala tillgänglig|  Neural|
|Queretaro Otomi|   `otq`   |Endast statistik|  Statistikuppgifter|
|Rumänska|  `ro`    |Neurala tillgänglig|  Neural|
|Ryska|   `ru`    |Neurala tillgänglig|  Neural|
|Samoa|    `sm`    |Endast statistik|  Statistikuppgifter|
|Serbiska (kyrillisk)|    `sr-Cyrl`   |Endast statistik|  Statistikuppgifter|
|Serbiska (latinsk)|   `sr-Latn`   |Endast statistik   |Statistikuppgifter|
|Slovakiska|    `sk`    |Neurala tillgänglig|  Neural|
|Slovenska| `sl`    |Neurala tillgänglig|  Neural|
|Spanska|   `es`    |Neurala tillgänglig|  Neural|
|Svenska|   `sv`    |Neurala tillgänglig   |Neural|
|Tahitian|  `ty`    |Endast statistik|  Statistikuppgifter|
|Tamilska| `ta`    |Endast statistik|  Statistikuppgifter|
|Telugu|    `te`    |Endast neurala|   Neural|
|Thai|  `th`    |Neurala tillgänglig|  Neural|
|Tongan|    `to`    |Endast statistik|  Statistikuppgifter|
|Turkiska|   `tr`    |Neurala tillgänglig   |Neural|
|Ukrainska| `uk`    |Neurala tillgänglig|  Neural|
|Urdu|  `ur`    |Endast statistik|  Statistikuppgifter|
|Vietnamesiska|    `vi`    |Neurala tillgänglig|  Neural|
|Walesiska| `cy`    |Neurala tillgänglig|  Neural|
|Yucatec Maya|  `yua`   |Endast statistik|  Statistikuppgifter|

## <a name="transliteration"></a>Transkribering

Metoden translittererad stöder följande språk. I "till/från", "<-->" anger att språket kan överföras från eller till något av de angivna skripten. "-->" Anger att språket bara kan vara translittererad från ett skript till ett annat.

| Språk    | Språkkod | Skript | Till/från | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabiska | `ar` | Arabiska`Arab` | <--> | Latin`Latn` |
|Bangla  | `bn` | Bengali`Beng` | <--> | Latin`Latn` |
| Förenklad kinesiska | `zh-Hans` | Kinesiska, förenklad`Hans`| <--> | Latin`Latn` |
| Förenklad kinesiska | `zh-Hans` | Kinesiska, förenklad`Hans`| <--> | Kinesiska, traditionell`Hant`|
| Traditionell kinesiska | `zh-Hant` | Kinesiska, traditionell`Hant`| <--> | Latin`Latn` |
| Traditionell kinesiska | `zh-Hant` | Kinesiska, traditionell`Hant`| <--> | Kinesiska, förenklad`Hans` |
| Gujarati | `gu`  | Gujarati`Gujr` | --> | Latin`Latn` |
| Hebreiska | `he` | Hebreiska`Hebr` | <--> | Latin`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Latin`Latn` |
| Japanska | `ja` | Japanska`Jpan` | <--> | Latin`Latn` |
| Kannada | `kn` | Kannada `Knda` | --> | Latin`Latn` |
| Malayalam | `ml` | Malayalam`Mlym` | --> | Latin`Latn` |
| Marathi | `mr` | Devanagari`Deva` | --> | Latin`Latn` |
| Odia | `or` | Odia`Orya` | <--> | Latin`Latn` |
| Punjabi | `pa` | Gurmukhi`Guru`  | <--> | Latin`Latn`  |
| Serbiska (kyrillisk) | `sr-Cyrl` | Kyrillisk`Cyrl`  | --> | Latin`Latn` |
| Serbiska (latinsk) | `sr-Latn` | Latin`Latn` | --> | Kyrillisk`Cyrl`|
| Tamilska | `ta` | Tamilska`Taml` | --> | Latin`Latn` |
| Telugu | `te` | Telugu`Telu` | --> | Latin`Latn` |
| Thai | `th` | Thai`Thai` | <--> | Latin`Latn` |

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

## <a name="detect"></a>Detect

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
| Italienska      | `it`          |
| Japanska      | `ja`          |
|Kiswahili| `sw`    |
| Koreanska      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
|Madagaskisk|  `mg`    |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska      | `pt`          |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
|Samoa|    `sm`    |
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
