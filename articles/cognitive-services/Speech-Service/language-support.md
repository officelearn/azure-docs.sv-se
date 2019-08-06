---
title: Språk stöd – tal tjänsten
titleSuffix: Azure Cognitive Services
description: Tal tjänsten har stöd för flera språk för konvertering från tal till text och text till tal, tillsammans med tal översättning. Den här artikeln innehåller en omfattande lista över språk stöd för tjänst funktionen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: ac3cb1d637eae1b4ee0a7db59efe631c7eb1ac6f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815271"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Språk-och region stöd för tal tjänsterna

Olika språk stöds för olika funktioner i tal tjänster. Följande tabeller sammanfattar språkstöd.

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech igenkänning SDK och REST API stöder följande språk (lokala). Olika typer av anpassningar är tillgängliga för varje språk.

  Kod | Språk | [Akustisk anpassning](how-to-customize-acoustic-models.md) | [Språk-anpassning](how-to-customize-language-model.md) | [Uttal av anpassning](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-t.ex. | Arabiska (Egypten), moderna standard | Nej | Ja | Nej
 CA-ES | Katalanska | Nej | Nej | Nej
 da-DK | Danska (Danmark) | Nej | Nej | Nej
 de-DE | Tyska (Tyskland) | Ja | Ja | Ja
 SV-Australien | Engelska (Australien) | Nej | Ja | Nej
 en CA: N | Engelska (Kanada) | Nej | Ja | Nej
 en-GB | Engelska (Storbritannien) | Nej | Ja | Nej
 en Indien | English (India) | Ja | Ja | Nej
 en NZ | Engelska (Nya Zeeland) | Nej | Ja | Nej 
 en-US | Engelska (USA) | Ja | Ja | Ja
 es-ES | Spanska (Spanien) | Ja | Ja | Nej
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
 pt-BR | Portugisiska (Brasilien) | Ja | Ja | Nej
 PT-PT | Portugisiska (Portugal) | Nej | Ja | Nej
 ru-RU | Ryska (Ryssland) | Ja | Ja | Nej
 SV-SE | Svenska (Sverige) | Nej | Nej | Nej
 zh-CN | Kinesiska (Mandarin, förenklad) | Ja | Ja | Nej
 zh-HK | Kinesiska (kantonesiska, traditionell) | Nej | Ja | Nej
 zh-TW | Kinesiska (Mandarin Taiwanesiska) | Nej | Ja | Nej
 TH-TH | Thailändska (Thailand) | Nej | Nej | Nej


## <a name="text-to-speech"></a>Text till tal

Text till tal-REST API stöder dessa röster, som var och en har stöd för ett särskilt språk och dialekt, som identifieras av locale.

> [!IMPORTANT]
> Prissättningen varierar för standard-, anpassade och neurala röster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Neurala-röster

Neurala text till tal är en ny typ av tal syntes som drivs av djup neurala nätverk. När du använder en neurala-röst är syntetiskt tal nästan lätt att skilja från de mänskliga inspelningarna.

Neurala röster kan användas för att göra interaktioner med chattrobotar och virtuella assistenter mer naturligt och engagerande, konvertera digitala texter som e-böcker till Audiobooks och förbättra navigerings systemen i bilen. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när användarna interagerar med AI-system.

En fullständig lista över neurala-röster och regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

Nationell inställning | Språk | Kön | Fullständig tjänst namns mappning | Kort röst namn
--------|----------|--------|---------|------------
de-DE | Tyska (Tyskland) | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | English (US) | Man | "Microsoft Server Speech Text till tal Voice (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | English (US) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, JessaNeural)" | "en-US-JessaNeural"
IT-IT | Italienska (Italien) | Kvinna |"Microsoft Server Speech Text till tal Voice (IT-IT, ElsaNeural)" | "IT-ElsaNeural"
zh-CN | Kinesiska (fastlandet) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Du kan antingen använda den fullständiga tjänst namns mappningen eller det korta röst namnet i ditt tal syntes begär Anden.

### <a name="standard-voices"></a>Standard röster

Det finns fler än 75 standard röster i över 45 språk och nationella inställningar, vilket gör att du kan konvertera text till syntetiskt tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

Nationell inställning | Språk | Kön | Fullständig tjänst namns mappning | Kort röst namn
-------|----------|---------|----------|----------
ar-t.ex.\* | Arabiska (Egypten) | Kvinna | ”Microsoft Server tal Text till tal-röst (ar-t.ex., Hoda)” | "ar-EG-Hoda"
ar-SA | Arabiska (Saudiarabien) | Man | ”Microsoft Server tal Text till tal-röst (ar-SA, Naayf)” | "ar-SA-Naayf"
BG-BG | Bulgariska | Man | ”Microsoft Server tal Text till tal röst (bg-BG, Ivan)” | "BG-BG-Ivan"
CA-ES | Katalanska (Spanien) | Kvinna | ”Microsoft Server tal Text till tal röst (ca-ES, HerenaRUS)” | "ca-ES-HerenaRUS"
CS-CZ | Tjeckiska | Man | ”Microsoft Server tal Text till tal-röst (cs-CZ, Jakub)” | "cs-CZ-Jakub"
da-DK | Danska | Kvinna | ”Microsoft Server tal Text till tal-röst (da-DK, HelleRUS)” | "da-DK-HelleRUS"
Tyskland-AT | Tyska (Österrike) | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-AT, Michael)” | "de-AT-Michael"
Tyskland – CH | Tyska (Schweiz) | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-CH, Karsten)” | "de-CH-Karsten"
de-DE | Tyska (Tyskland) | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, Hedda)” | "de-DE-Hedda"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, HeddaRUS)” | "de-DE-HeddaRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (de-DE, Stefan, Apollo)” | "de-DE-Stefan-Apollo"
el GR | Grekiska | Man | ”Microsoft Server tal Text till tal-röst (el-GR, Stefanos)” | "El-GR-Stefanos"
SV-Australien | Engelska (Australien) | Kvinna | ”Microsoft Server tal Text till tal-röst (en AU, Catherine)” | "en-AU-Catherine"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en AU, HayleyRUS)” | "en-AU-HayleyRUS"
en CA: N | Engelska (Kanada) | Kvinna | ”Microsoft Server tal Text till tal-röst (en CA, Johan)” | "en-CA-Linda"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en CA, HeatherRUS)” | "en-CA-HeatherRUS"
en-GB | English (UK) | Kvinna | ”Microsoft Server tal Text till tal-röst (en-GB, Susan, Apollo)” | "en-GB-Susan-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en-GB, HazelRUS)” | "en-GB-HazelRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (en-GB, George, Apollo)” | "en-GB-George-Apollo"
en IE | Engelska (Irland) | Man | ”Microsoft Server tal Text till tal-röst (en IE, Stefan)” | "en-IE-Stefan"
en Indien | English (India) | Kvinna | ”Microsoft Server tal Text till tal-röst (en-IN-, Heera, Apollo)” | "en-IN-Heera-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en-IN-, PriyaRUS)” | "en-IN-PriyaRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (en-IN-, Ravi, Apollo)” | "en-IN-Ravi-Apollo"
en-US | English (US) | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, ZiraRUS)” | "en-US-ZiraRUS"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, JessaRUS)” | "en-US-JessaRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (en-US, BenjaminRUS)” | "en-US-BenjaminRUS"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, Jessa24kRUS)” | "en-US-Jessa24kRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (en-US, Guy24kRUS)” | "en-US-Guy24kRUS"
es-ES | Spanska (Spanien) |Kvinna | ”Microsoft Server tal Text till tal röst (es-ES, Lisa, Apollo)” | "es-ES-Laura-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (es-ES, HelenaRUS)” | "es-ES-HelenaRUS"
| | | Man | ”Microsoft Server tal Text till tal röst (es-ES, Pablo, Apollo)” | "es-ES-Pablo-Apollo"
es-MX | Spanska (Mexiko) | Kvinna | ”Microsoft Server tal Text till tal röst (es-MX, HildaRUS)” | "es-MX-HildaRUS"
| | | Man | ”Microsoft Server tal Text till tal röst (es-MX, Raul, Apollo)” | "es-MX-Raul-Apollo"
fi-FI | Finska | Kvinna | ”Microsoft Server tal Text till tal-röst (fi-FI, HeidiRUS)” | "fi-FI-HeidiRUS"
fr-CA | Franska (Kanada) |Kvinna | ”Microsoft Server tal Text till tal röst (fr-CA, Caroline)” | "fr-CA-Caroline"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (fr-CA, HarmonieRUS)” | "fr-CA-HarmonieRUS"
fr CH | Franska (Schweiz)| Man | ”Microsoft Server tal Text till tal röst (fr-CH, Guillaume)” | "fr-CH-Guillaume"
fr-FR | Franska (Frankrike)| Kvinna | ”Microsoft Server tal Text till tal röst (fr-FR, Julia, Apollo)” | "fr-FR-Julie-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (fr-FR, HortenseRUS)” | "fr-FR-HortenseRUS"
| | | Man | ”Microsoft Server tal Text till tal röst (fr-FR, Paul, Apollo)” | "fr-FR-Paul-Apollo"
han IL| Hebreiska (Israel) | Man| ”Microsoft Server tal Text till tal-röst (he IL-Asaf)” | "he-IL-Asaf"
Hej Indien | Hindi (Indien) | Kvinna | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Kalpana, Apollo)” | "hi-IN-Kalpana-Apollo"
| | |Kvinna | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Kalpana)” | "Hi-IN-Kalpana"
| | | Man | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Hemant)” | "Hi-IN-Hemant"
HR-HR | Kroatiska | Man | ”Microsoft Server tal Text till tal röst (hr-HR, Matej)” | "hr-HR-Matej"
hu-HU | Ungerska | Man | ”Microsoft Server tal Text till tal-röst (hu-HU, Szabolcs)” | "hu-HU-Szabolcs"
ID-ID | Indonesiska| Man | ”Microsoft Server tal Text till tal-röst (id-ID, Andika)” | "id-ID-Andika"
IT-IT | Italienska | Man | ”Microsoft Server tal Text till tal-röst (it-IT, Cosimo, Apollo)” | "it-IT-Cosimo-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (it-IT, LuciaRUS)” | "it-IT-LuciaRUS"
ja-JP | Japanska | Kvinna | ”Microsoft Server tal Text till tal röst (ja-JP, Ayumi, Apollo)” | "ja-JP-Ayumi-Apollo"
| | | Man | ”Microsoft Server tal Text till tal röst (ja-JP, Ichiro, Apollo)” | "ja-JP-Ichiro-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (ja-JP, HarukaRUS)” | "ja-JP-HarukaRUS"
ko-KR | Koreanska | Kvinna | ”Microsoft Server tal Text till tal-röst (ko-KR, HeamiRUS)” | "ko-KR-HeamiRUS"
MS-Mina | Malajiska | Man | ”Microsoft Server tal Text till tal röst (ms Mina Rizwan)” | "ms-MY-Rizwan"
NB-NO | Norska | Kvinna | ”Microsoft Server tal Text till tal röst (nb-NO HuldaRUS)” | "nb-NO-HuldaRUS"
NL-NL | Nederländska | Kvinna | ”Microsoft Server tal Text till tal röst (nl-NL, HannaRUS)” | "nl-NL-HannaRUS"
pl-PL | Polska | Kvinna | ”Microsoft Server tal Text till tal röst (pl-PL, PaulinaRUS)” | "pl-PL-PaulinaRUS"
pt-BR | Portugisiska (Brasilien) | Kvinna | ”Microsoft Server tal Text till tal röst (pt-BR, HeloisaRUS)” | "pt-BR-HeloisaRUS"
| | | Man |”Microsoft Server tal Text till tal röst (pt-BR, Daniel, Apollo)” | "pt-BR-Daniel-Apollo"
PT-PT | Portugisiska (Portugal) | Kvinna | ”Microsoft Server tal Text till tal röst (pt-PT, HeliaRUS)” | "pt-PT-HeliaRUS"
RO-RO | Rumänska | Man | ”Microsoft Server tal Text till tal-röst (ro-RO, Andrei)” | "ro-RO-Andrei"
ru-RU |Ryska| Kvinna | ”Microsoft Server tal Text till tal röst (ru-RU, Irina, Apollo)” | "ru-RU-Irina-Apollo"
| | | Man | ”Microsoft Server tal Text till tal röst (ru-RU, Pavel, Apollo)” | "ru-RU-Pavel-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (ru-RU, EkaterinaRUS)” | ru-RU-EkaterinaRUS
sk-SK | Slovakiska | Man | ”Microsoft Server tal Text till tal röst (sk-SK, Filip)” | "sk-SK-Filip"
sl-SI | Slovenska | Man | ”Microsoft Server tal Text till tal röst (sl-SI, Lado)” | "sl-SI-Lado"
SV-SE | Svenska | Kvinna | ”Microsoft Server tal Text till tal-röst (sv-SE, HedvigRUS)” | "sv-SE-HedvigRUS"
ta IN | Tamil (Indien) | Man | ”Microsoft Server tal Text till tal-röst (ta-IN-, Valluvar)” | "ta-IN-Valluvar"
te Indien | Telugu (Indien) | Kvinna | ”Microsoft Server tal Text till tal-röst (te-IN-, Chitra)” | "te-IN-Chitra"
TH-TH | Thai | Man | ”Microsoft Server tal Text till tal röst (th-TH, Pattara)” | "th-TH-Pattara"
TR-TR | Turkiska | Kvinna | ”Microsoft Server tal Text till tal röst (tr-TR, SedaRUS)” | "tr-TR-SedaRUS"
Vi VN | Vietnamesiska | Man | ”Microsoft Server tal Text till tal röst (vi VN ett)” | "vi-VN-An"
zh-CN | Kinesiska (fastlandet) | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-CN, HuihuiRUS)” | "zh-CN-HuihuiRUS"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-CN, Yaoyao, Apollo)” | "zh-CN-Yaoyao-Apollo"
| | | Man | ”Microsoft Server tal Text till tal-röst (zh-CN, Kangkang, Apollo)” | "zh-CN-Kangkang-Apollo"
zh-HK | Kinesiska (Hongkong) | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-HK Tracy, Apollo)” | "zh-HK-Tracy-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-HK TracyRUS)” | "zh-HK-TracyRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (zh-HK Danny, Apollo)” | "zh-HK-Danny-Apollo"
zh-TW | Kinesiska (Taiwan) | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-TW, Yating, Apollo)” | "zh-TW-Yating-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-TW, HanHanRUS)” | "zh-TW-HanHanRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (zh-TW, Zhiwei, Apollo)” | "zh-TW-Zhiwei-Apollo"

\* *ar T.ex stöder moderna Standard arabiska (MSA).*

> [!NOTE]
> Du kan antingen använda den fullständiga tjänst namns mappningen eller det korta röst namnet i ditt tal syntes begär Anden.

### <a name="customization"></a>Anpassning

Röst anpassning är tillgänglig för de-DE, en-GB, en-i, en-US, ES-MX, fr-FR, IT-IT, pt-BR och zh-CN. Välj rätt språk version som matchar de tränings data du behöver för att träna en anpassad röst modell. Om till exempel de inspelnings data du har talas på engelska med en brittisk accent väljer du en-GB.  

> [!NOTE]
> Vi har inte stöd för dubbelriktad modell utbildning i anpassad röst, förutom kinesiska-engelska bi-språk. Välj kinesiskt "kinesiskt" (tvåspråkig) "om du vill träna en kinesisk röst som även kan tala med engelska. Röst träning i alla lokala platser börjar med en data uppsättning av 2000 + yttranden, med undantag för en-US och zh-CN där du kan börja med valfri storlek på tränings data.

## <a name="speech-translation"></a>Talöversättning

Den **Talöversättning** API har stöd för olika språk för översättning av tal-till-tal- och tal till text. Käll språket måste alltid vara från språk tabellen för tal till text. Tillgängliga mål språk beror på om translation målet är tal eller text. Du kan översätta inkommande tal i mer än [60 språk](https://www.microsoft.com/translator/business/languages/). En delmängd av dessa språk är tillgängliga för [talsyntes](language-support.md#text-languages).

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

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech Services](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
