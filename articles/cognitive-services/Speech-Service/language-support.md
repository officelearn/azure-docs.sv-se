---
title: Stöd för språk - tjänsten för Taligenkänning
titleSuffix: Azure Cognitive Services
description: En lista med naturligt språk som stöds av Speech Service.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: erhopf
ms.openlocfilehash: c1eeee35869f29170a7f15b1d9e2c1ee69a6314d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622524"
---
# <a name="language-and-region-support-for-speech-service-api"></a>Stöd för språk och din region för tjänsten för Taligenkänning

Olika språk har stöd för olika funktioner för tal-tjänst. Följande tabeller sammanfattar språkstöd.

## <a name="speech-to-text"></a>Tal till text

Microsoft taligenkänningens API stöder följande språk. Olika typer av anpassningar är tillgängliga för varje språk.

  Kod | Språk | [Akustisk anpassning](how-to-customize-acoustic-models.md) | [Språk-anpassning](how-to-customize-language-model.md) | [Uttal av anpassning](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-t.ex. | Arabiska (Egypten), moderna standard | Nej | Ja | Nej
 CA-ES | Katalanska (Spanien) | Nej | Nej | Nej
 da-DK | Danska (Danmark) | Nej | Nej | Nej
 de-DE | Tyska (Tyskland) | Ja | Ja | Nej
 SV-Australien | Engelska (Australien) | Nej | Ja | Ja
 en CA: N | Engelska (Kanada) | Nej | Ja | Ja
 en-GB | Engelska (Storbritannien) | Nej | Ja | Ja
 en Indien | English (India) | Ja | Ja | Ja
 en NZ | Engelska (Nya Zeeland) | Nej | Ja | Ja  
 sv-SE | Engelska (USA) | Ja | Ja | Ja
 es-ES | Spanska (Spanien) | Nej | Ja | Nej
 es-MX | Spanska (Mexiko) | Nej | Ja | Nej
 fi-FI | Finska (Finland) | Nej | Nej | Nej
 fr-CA | Franska (Kanada) | Nej | Ja | Nej
 fr-FR | Franska (Frankrike) | Ja | Ja | Nej
 Hej Indien | Hindi (Indien) | Nej | Ja | Nej
 IT-IT | Italienska (Italien) | Ja | Ja | Nej
 ja-JP | Japanska (Japan) | Nej | Ja | Nej
 ko-KR | Koreanska (Korea) | Nej | Ja | Nej
 NB-NO | Norska (Bokmål) (Norge) | Nej | Nej | Nej
 NL-NL | Nederländska (Nederländerna) | Nej | Ja | Nej
 pl-PL | Polska (Polen) | Nej | Nej | Nej
 pt-BR | Portugisiska (Brasilien) | Nej | Ja | Nej
 PT-PT | Portugisiska (Portugal) | Nej | Ja | Nej
 ru-RU | Ryska (Ryssland) | Ja | Ja | Nej
 SV-SE | Svenska (Sverige) | Nej | Nej | Nej
 zh-CN | Kinesiska (Mandarin, förenklad) | Ja | Ja | Nej
 zh-HK | Kinesiska (Mandarin, traditionell) | Nej | Ja | Nej
 zh-TW | Kinesiska (Mandarin Taiwanesiska) | Nej | Ja | Nej
 TH-TH | Thailändska (Thailand) | Nej | Nej | Nej


## <a name="text-to-speech"></a>Text till tal

API för taligenkänning syntes erbjuder följande röster, som stöder ett visst språk och dialekt som identifieras av nationella inställningar.

Nationell inställning | Språk | Kön | Tjänsten Namnmappningen
-------|----------|---------|--------------------
ar-t.ex.\* | Arabiska (Egypten) | Kvinna | ”Microsoft Server tal Text till tal-röst (ar-t.ex., Hoda)”
ar-SA | Arabiska (Saudiarabien) | Man | ”Microsoft Server tal Text till tal-röst (ar-SA, Naayf)”
BG-BG | Bulgariska | Man | ”Microsoft Server tal Text till tal röst (bg-BG, Ivan)”
CA-ES | Katalanska (Spanien) | Kvinna | ”Microsoft Server tal Text till tal röst (ca-ES, HerenaRUS)”
CS-CZ | Tjeckiska | Man | ”Microsoft Server tal Text till tal-röst (cs-CZ, Jakub)”
CS-CZ | Tjeckiska | Man | ”Microsoft Server tal Text till tal-röst (cs-CZ, Vit)”
da-DK | Danska | Kvinna | ”Microsoft Server tal Text till tal-röst (da-DK, HelleRUS)”
Tyskland-AT | Tyska (Österrike) | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-AT, Michael)”
Tyskland – CH | Tyska (Schweiz) | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-CH, Karsten)”
de-DE | Tyska (Tyskland) | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, Hedda)”
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, HeddaRUS)”
| | | Man | ”Microsoft Server tal Text till tal-röst (de-DE, Stefan, Apollo)”
el GR | Grekiska | Man | ”Microsoft Server tal Text till tal-röst (el-GR, Stefanos)”
SV-Australien | Engelska (Australien) | Kvinna | ”Microsoft Server tal Text till tal-röst (en AU, Catherine)”
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en AU, HayleyRUS)”
en CA: N | Engelska (Kanada) | Kvinna | ”Microsoft Server tal Text till tal-röst (en CA, Johan)”
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en CA, HeatherRUS)”
en-GB | English (UK) | Kvinna | ”Microsoft Server tal Text till tal-röst (en-GB, Susan, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (en-GB, HazelRUS)”
| | |Man | ”Microsoft Server tal Text till tal-röst (en-GB, George, Apollo)”
en IE | Engelska (Irland) |Man | ”Microsoft Server tal Text till tal-röst (en IE, Stefan)”
en IE | Engelska (Irland) |Man | ”Microsoft Server tal Text till tal-röst (en IE, Shaun)”
en Indien | English (India) | Kvinna | ”Microsoft Server tal Text till tal-röst (en-IN-, Heera, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (en-IN-, PriyaRUS)”
| | |Man | ”Microsoft Server tal Text till tal-röst (en-IN-, Ravi, Apollo)”
sv-SE | English (US) |Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, ZiraRUS)”
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, JessaRUS)”
| | |Man | ”Microsoft Server tal Text till tal-röst (en-US, BenjaminRUS)”
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, Jessa24kRUS)”
| | |Man | ”Microsoft Server tal Text till tal-röst (en-US, Guy24kRUS)”
es-ES | Spanska (Spanien) |Kvinna | ”Microsoft Server tal Text till tal röst (es-ES, Lisa, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal röst (es-ES, HelenaRUS)”
| | |Man | ”Microsoft Server tal Text till tal röst (es-ES, Pablo, Apollo)”
es-MX | Spanska (Mexiko) | Kvinna | ”Microsoft Server tal Text till tal röst (es-MX, HildaRUS)”
| | | Man | ”Microsoft Server tal Text till tal röst (es-MX, Raul, Apollo)”
fi-FI | Finska | Kvinna | ”Microsoft Server tal Text till tal-röst (fi-FI, HeidiRUS)”
fr-CA | Franska (Kanada) |Kvinna | ”Microsoft Server tal Text till tal röst (fr-CA, Caroline)”
| | | Kvinna | ”Microsoft Server tal Text till tal röst (fr-CA, HarmonieRUS)”
fr CH | Franska (Schweiz)|Man | ”Microsoft Server tal Text till tal röst (fr-CH, Guillaume)”
fr-FR | Franska (Frankrike)|Kvinna | ”Microsoft Server tal Text till tal röst (fr-FR, Julia, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal röst (fr-FR, HortenseRUS)”
| | |Man | ”Microsoft Server tal Text till tal röst (fr-FR, Paul, Apollo)”
han IL| Hebreiska (Israel) | Man| ”Microsoft Server tal Text till tal-röst (he IL-Asaf)”
Hej Indien | Hindi (Indien) | Kvinna | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Kalpana, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Kalpana)”
| | | Man | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Hemant)”
HR-HR | Kroatiska | Man | ”Microsoft Server tal Text till tal röst (hr-HR, Matej)”
hu-HU | Ungerska | Man | ”Microsoft Server tal Text till tal-röst (hu-HU, Szabolcs)”
ID-ID | Indonesiska| Man | ”Microsoft Server tal Text till tal-röst (id-ID, Andika)”
IT-IT | Italienska |Man | ”Microsoft Server tal Text till tal-röst (it-IT, Cosimo, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (it-IT, LuciaRUS)”
ja-JP | Japanska |Kvinna | ”Microsoft Server tal Text till tal röst (ja-JP, Ayumi, Apollo)”
| | |Man | ”Microsoft Server tal Text till tal röst (ja-JP, Ichiro, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal röst (ja-JP, HarukaRUS)”
ko-KR | Koreanska |Kvinna | ”Microsoft Server tal Text till tal-röst (ko-KR, HeamiRUS)”
MS-Mina | Malajiska | Man | ”Microsoft Server tal Text till tal röst (ms Mina Rizwan)”
NB-NO | Norska | Kvinna | ”Microsoft Server tal Text till tal röst (nb-NO HuldaRUS)”
NL-NL | Nederländska | Kvinna | ”Microsoft Server tal Text till tal röst (nl-NL, HannaRUS)”
pl-PL | Polska | Kvinna | ”Microsoft Server tal Text till tal röst (pl-PL, PaulinaRUS)”
pt-BR | Portugisiska (Brasilien) | Kvinna | ”Microsoft Server tal Text till tal röst (pt-BR, HeloisaRUS)”
| | |Man | ”Microsoft Server tal Text till tal röst (pt-BR, Daniel, Apollo)”
PT-PT | Portugisiska (Portugal) | Kvinna | ”Microsoft Server tal Text till tal röst (pt-PT, HeliaRUS)”
RO-RO | Rumänska | Man | ”Microsoft Server tal Text till tal-röst (ro-RO, Andrei)”
ru-RU |Ryska| Kvinna | ”Microsoft Server tal Text till tal röst (ru-RU, Irina, Apollo)”
| | |Man | ”Microsoft Server tal Text till tal röst (ru-RU, Pavel, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal röst (ru-RU, EkaterinaRUS)”
sk-SK | Slovakiska|Man | ”Microsoft Server tal Text till tal röst (sk-SK, Filip)”
sl-SI | Slovenska|Man | ”Microsoft Server tal Text till tal röst (sl-SI, Lado)”
SV-SE | Svenska|Kvinna | ”Microsoft Server tal Text till tal-röst (sv-SE, HedvigRUS)”
ta IN | Tamil (Indien) |Man | ”Microsoft Server tal Text till tal-röst (ta-IN-, Valluvar)”
te Indien | Telugu (Indien) |Kvinna | ”Microsoft Server tal Text till tal-röst (te-IN-, Chitra)”
TH-TH | Thai|Man | ”Microsoft Server tal Text till tal röst (th-TH, Pattara)”
TR-TR |Turkiska| Kvinna | ”Microsoft Server tal Text till tal röst (tr-TR, SedaRUS)”
Vi VN | Vietnamesiska|Man | ”Microsoft Server tal Text till tal röst (vi VN ett)”
zh-CN | Kinesiska (fastlandet)|Kvinna | ”Microsoft Server tal Text till tal-röst (zh-CN, HuihuiRUS)”
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (zh-CN, Yaoyao, Apollo)”
| | |Man | ”Microsoft Server tal Text till tal-röst (zh-CN, Kangkang, Apollo)”
zh-HK | Kinesiska (Hongkong)|Kvinna | ”Microsoft Server tal Text till tal-röst (zh-HK Tracy, Apollo)”
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (zh-HK TracyRUS)”
| || Man | ”Microsoft Server tal Text till tal-röst (zh-HK Danny, Apollo)”
zh-TW | Kinesiska (Taiwan)|Kvinna | ”Microsoft Server tal Text till tal-röst (zh-TW, Yating, Apollo)”
| || Kvinna | ”Microsoft Server tal Text till tal-röst (zh-TW, HanHanRUS)”
| || Man | ”Microsoft Server tal Text till tal-röst (zh-TW, Zhiwei, Apollo)”

\* *ar T.ex stöder moderna Standard arabiska (MSA).*

### <a name="customization"></a>Anpassning

Röst anpassning är tillgängligt för amerikansk engelska (en-US), fastlandet kinesiska (zh-CN) och italienska (it-IT).

> [!NOTE]
> Italienska ton börjar med en uppsättning av över 2 000 yttranden. Kinesiska – engelska tvåspråkig modeller stöds också med en inledande data över 2 000 yttranden.

## <a name="speech-translation"></a>Talöversättning

Den **Talöversättning** API har stöd för olika språk för översättning av tal-till-tal- och tal till text. Källspråk måste alltid vara i följande tabell för tal-språk. Tillgängliga mål språk beror på om translation målet är tal eller text. Du kan översätta inkommande tal i mer än [60 språk](https://www.microsoft.com/translator/business/languages/). En delmängd av dessa språk är tillgängliga för [talsyntes](language-support.md#text-languages). 

### <a name="speech-languages"></a>Talspråk

| Talspråk   | Språkkod |
|:----------- |-|
| Arabiska (moderna Standard)      | `ar` |
| Kinesiska (Mandarin)      | `zh` |
| Svenska      | `en` |
| Franska      | `fr` |
| Tyska      | `de` |
| Italienska      | `it` |
| Japanska      | `jp` |
| Portugisiska (Brasilien)     | `pt` |
| Ryska      | `ru` |
| Spanska      |  `es` |

### <a name="text-languages"></a>Språken för mobilapptext

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


## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
