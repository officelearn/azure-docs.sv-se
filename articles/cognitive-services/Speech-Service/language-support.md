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
ms.date: 11/21/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 87b065d2ecf9d80c072918c14e7861f72b8d7aba
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304905"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Språk-och region stöd för tal tjänsterna

Språk stöd varierar beroende på funktioner i tal tjänster. Följande tabeller sammanfattar språk stöd för [tal-till-text](#speech-to-text)-, [text-till-tal](#text-to-speech)-och [röst översättnings](#speech-translation) tjänst erbjudanden.

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech SDK och REST API stöder följande språk (lokala). För att förbättra noggrannheten erbjuds anpassning för en delmängd av språken genom uppladdning av ljud + mänskligt avskrifter eller relaterad text: meningar.  Uttal-anpassning är för närvarande bara tillgänglig för `en-US` och `de-DE`. Läs mer om anpassning [här](how-to-custom-speech.md).

 Nationell inställning | Språk | Stöds | Anpassningsbar
------|------------|-----------|-------------
`ar-EG` | Arabiska (Egypten), moderna standard | Ja | Ja
`ar-SA` | Arabiska (Saudiarabien) | Ja | Ja
`ar-AE` | Arabiska (Förenade Arabemiraten) | Ja | Ja
`ar-KW` | Arabiska (Kuwait) | Ja | Ja
`ar-QA` | Arabiska (Qatar) | Ja | Ja
`ca-ES` | Katalanska | Ja | Nej
`da-DK` | Danska (Danmark) | Ja | Nej
`de-DE` | Tyska (Tyskland) | Ja | Ja
`en-AU` | Engelska (Australien) | Ja | Ja
`en-CA` | Engelska (Kanada) | Ja | Ja
`en-GB` | Engelska (Storbritannien) | Ja | Ja
`en-IN` | English (India) | Ja | Ja
`en-NZ` | Engelska (Nya Zeeland) | Ja | Ja
`en-US` | Engelska (USA) | Ja | Ja
`es-ES` | Spanska (Spanien) | Ja | Ja
`es-MX` | Spanska (Mexiko) | Ja | Ja
`fi-FI` | Finska (Finland) | Ja | Nej
`fr-CA` | Franska (Kanada) | Ja | Ja
`fr-FR` | Franska (Frankrike) | Ja | Ja
`gu-IN` | Gujarati (indiska) | Ja | Ja
`hi-IN` | Hindi (Indien) | Ja | Ja
`it-IT` | Italienska (Italien) | Ja | Ja
`ja-JP` | Japanska (Japan) | Ja | Ja
`ko-KR` | Koreanska (Korea) | Ja | Ja
`mr-IN` | Marathi (Indien) | Ja | Ja
`nb-NO` | Norska (Bokmål) (Norge) | Ja | Nej
`nl-NL` | Nederländska (Nederländerna) | Ja | Ja
`pl-PL` | Polska (Polen) | Ja | Nej
`pt-BR` | Portugisiska (Brasilien) | Ja | Ja
`pt-PT` | Portugisiska (Portugal) | Ja | Ja
`ru-RU` | Ryska (Ryssland) | Ja | Ja
`sv-SE` | Svenska (Sverige) | Ja | Nej
`ta-IN` | Tamil (Indien) | Ja | Ja
`te-IN` | Telugu (Indien) | Ja | Ja
`zh-CN` | Kinesiska (Mandarin, förenklad) | Ja | Ja
`zh-HK` | Kinesiska (kantonesiska, traditionell) | Ja | Ja
`zh-TW` | Kinesiska (Mandarin Taiwanesiska) | Ja | Ja
`th-TH` | Thailändska (Thailand) | Ja | Nej
`tr-TR` | Turkiet | Ja | Ja

## <a name="text-to-speech"></a>Text till tal

Både Microsoft Speech SDK och REST-API: er har stöd för dessa röster, som var och en har stöd för ett särskilt språk och dialekt, som identifieras av nationella inställningar.

> [!IMPORTANT]
> Prissättningen varierar för standard-, anpassade och neurala röster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Neurala-röster

Neurala text till tal är en ny typ av tal syntes som drivs av djup neurala nätverk. När du använder en neurala-röst är syntetiskt tal nästan lätt att skilja från de mänskliga inspelningarna.

Neurala röster kan användas för att göra interaktioner med chattrobotar och röst assistenter mer naturligt och engagerande, konvertera digitala texter som e-böcker till Audiobooks och förbättra navigerings systemen i bilen. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när användarna interagerar med AI-system.

En fullständig lista över neurala-röster och regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

Nationell inställning | Språk | Kön | Fullständig tjänst namns mappning | Kort röst namn
--------|----------|--------|---------|------------
`de-DE` | Tyska (Tyskland) | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | English (US) | Man | "Microsoft Server Speech Text till tal Voice (en-US, GuyNeural)" | "en-US-GuyNeural"
`en-US` | English (US) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, JessaNeural)" | "en-US-JessaNeural"
`it-IT` | Italienska (Italien) | Kvinna |"Microsoft Server Speech Text till tal Voice (IT-IT, ElsaNeural)" | "IT-ElsaNeural"
`zh-CN` | Kinesiska (fastlandet) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Du kan antingen använda den fullständiga tjänst namns mappningen eller det korta röst namnet i ditt tal syntes begär Anden.

### <a name="standard-voices"></a>Standard röster

Det finns fler än 75 standard röster i över 45 språk och nationella inställningar, vilket gör att du kan konvertera text till syntetiskt tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

Nationell inställning | Språk | Kön | Fullständig tjänst namns mappning | Kort namn
-------|----------|---------|----------|----------
<sup>&dagger;</sup>`ar-EG` | Arabiska (Egypten) | Kvinna | ”Microsoft Server tal Text till tal-röst (ar-t.ex., Hoda)” | "ar-EG-Hoda"
`ar-SA` | Arabiska (Saudiarabien) | Man | ”Microsoft Server tal Text till tal-röst (ar-SA, Naayf)” | "ar-SA-Naayf"
`bg-BG` | Bulgariska | Man | ”Microsoft Server tal Text till tal röst (bg-BG, Ivan)” | "BG-BG-Ivan"
`ca-ES` | Katalanska (Spanien) | Kvinna | ”Microsoft Server tal Text till tal röst (ca-ES, HerenaRUS)” | "ca-ES-HerenaRUS"
`cs-CZ` | Tjeckiska | Man | ”Microsoft Server tal Text till tal-röst (cs-CZ, Jakub)” | "cs-CZ-Jakub"
`da-DK` | Danska | Kvinna | ”Microsoft Server tal Text till tal-röst (da-DK, HelleRUS)” | "da-DK-HelleRUS"
`de-AT` | Tyska (Österrike) | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-AT, Michael)” | "de-AT-Michael"
`de-CH` | Tyska (Schweiz) | Man | ”Microsoft Server tal Text till tal-röst (Tyskland-CH, Karsten)” | "de-CH-Karsten"
`de-DE` | Tyska (Tyskland) | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, Hedda)” | "de-DE-Hedda"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (de-DE, HeddaRUS)” | "de-DE-HeddaRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (de-DE, Stefan, Apollo)” | "de-DE-Stefan-Apollo"
`el-GR` | Grekiska | Man | ”Microsoft Server tal Text till tal-röst (el-GR, Stefanos)” | "El-GR-Stefanos"
`en-AU` | Engelska (Australien) | Kvinna | ”Microsoft Server tal Text till tal-röst (en AU, Catherine)” | "en-AU-Catherine"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en AU, HayleyRUS)” | "en-AU-HayleyRUS"
`en-CA` | Engelska (Kanada) | Kvinna | ”Microsoft Server tal Text till tal-röst (en CA, Johan)” | "en-CA-Linda"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en CA, HeatherRUS)” | "en-CA-HeatherRUS"
`en-GB` | English (UK) | Kvinna | ”Microsoft Server tal Text till tal-röst (en-GB, Susan, Apollo)” | "en-GB-Susan-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en-GB, HazelRUS)” | "en-GB-HazelRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (en-GB, George, Apollo)” | "en-GB-George-Apollo"
`en-IE` | Engelska (Irland) | Man | ”Microsoft Server tal Text till tal-röst (en IE, Stefan)” | "en-IE-Stefan"
`en-IN` | English (India) | Kvinna | ”Microsoft Server tal Text till tal-röst (en-IN-, Heera, Apollo)” | "en-IN-Heera-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en-IN-, PriyaRUS)” | "en-IN-PriyaRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (en-IN-, Ravi, Apollo)” | "en-IN-Ravi-Apollo"
`en-US` | English (US) | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, ZiraRUS)” | "en-US-ZiraRUS"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, JessaRUS)” | "en-US-JessaRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (en-US, BenjaminRUS)” | "en-US-BenjaminRUS"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (en-US, Jessa24kRUS)” | "en-US-Jessa24kRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (en-US, Guy24kRUS)” | "en-US-Guy24kRUS"
`es-ES` | Spanska (Spanien) |Kvinna | ”Microsoft Server tal Text till tal röst (es-ES, Lisa, Apollo)” | "es-ES-Laura-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (es-ES, HelenaRUS)” | "es-ES-HelenaRUS"
| | | Man | ”Microsoft Server tal Text till tal röst (es-ES, Pablo, Apollo)” | "es-ES-Pablo-Apollo"
`es-MX` | Spanska (Mexiko) | Kvinna | ”Microsoft Server tal Text till tal röst (es-MX, HildaRUS)” | "es-MX-HildaRUS"
| | | Man | ”Microsoft Server tal Text till tal röst (es-MX, Raul, Apollo)” | "es-MX-Raul-Apollo"
`fi-FI` | Finska | Kvinna | ”Microsoft Server tal Text till tal-röst (fi-FI, HeidiRUS)” | "fi-FI-HeidiRUS"
`fr-CA` | Franska (Kanada) |Kvinna | ”Microsoft Server tal Text till tal röst (fr-CA, Caroline)” | "fr-CA-Caroline"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (fr-CA, HarmonieRUS)” | "fr-CA-HarmonieRUS"
`fr-CH` | Franska (Schweiz)| Man | ”Microsoft Server tal Text till tal röst (fr-CH, Guillaume)” | "fr-CH-Guillaume"
`fr-FR` | Franska (Frankrike)| Kvinna | ”Microsoft Server tal Text till tal röst (fr-FR, Julia, Apollo)” | "fr-FR-Julie-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (fr-FR, HortenseRUS)” | "fr-FR-HortenseRUS"
| | | Man | ”Microsoft Server tal Text till tal röst (fr-FR, Paul, Apollo)” | "fr-FR-Paul-Apollo"
`he-IL` | Hebreiska (Israel) | Man| ”Microsoft Server tal Text till tal-röst (he IL-Asaf)” | "he-IL-Asaf"
`hi-IN` | Hindi (Indien) | Kvinna | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Kalpana, Apollo)” | "hi-IN-Kalpana-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Kalpana)” | "Hi-IN-Kalpana"
| | | Man | ”Microsoft Server tal Text till tal-röst (Hej-IN-, Hemant)” | "Hi-IN-Hemant"
`hr-HR` | Kroatiska | Man | ”Microsoft Server tal Text till tal röst (hr-HR, Matej)” | "hr-HR-Matej"
`hu-HU` | Ungerska | Man | ”Microsoft Server tal Text till tal-röst (hu-HU, Szabolcs)” | "hu-HU-Szabolcs"
`id-ID` | Indonesiska| Man | ”Microsoft Server tal Text till tal-röst (id-ID, Andika)” | "id-ID-Andika"
`it-IT` | Italienska | Man | ”Microsoft Server tal Text till tal-röst (it-IT, Cosimo, Apollo)” | "it-IT-Cosimo-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (it-IT, LuciaRUS)” | "it-IT-LuciaRUS"
`ja-JP` | Japanska | Kvinna | ”Microsoft Server tal Text till tal röst (ja-JP, Ayumi, Apollo)” | "ja-JP-Ayumi-Apollo"
| | | Man | ”Microsoft Server tal Text till tal röst (ja-JP, Ichiro, Apollo)” | "ja-JP-Ichiro-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (ja-JP, HarukaRUS)” | "ja-JP-HarukaRUS"
`ko-KR` | Koreanska | Kvinna | ”Microsoft Server tal Text till tal-röst (ko-KR, HeamiRUS)” | "ko-KR-HeamiRUS"
`ms-MY` | Malajiska | Man | ”Microsoft Server tal Text till tal röst (ms Mina Rizwan)” | "ms-MY-Rizwan"
`nb-NO` | Norska | Kvinna | ”Microsoft Server tal Text till tal röst (nb-NO HuldaRUS)” | "nb-NO-HuldaRUS"
`nl-NL` | Nederländska | Kvinna | ”Microsoft Server tal Text till tal röst (nl-NL, HannaRUS)” | "nl-NL-HannaRUS"
`pl-PL` | Polska | Kvinna | ”Microsoft Server tal Text till tal röst (pl-PL, PaulinaRUS)” | "pl-PL-PaulinaRUS"
`pt-BR` | Portugisiska (Brasilien) | Kvinna | ”Microsoft Server tal Text till tal röst (pt-BR, HeloisaRUS)” | "pt-BR-HeloisaRUS"
| | | Man |”Microsoft Server tal Text till tal röst (pt-BR, Daniel, Apollo)” | "pt-BR-Daniel-Apollo"
`pt-PT` | Portugisiska (Portugal) | Kvinna | ”Microsoft Server tal Text till tal röst (pt-PT, HeliaRUS)” | "pt-PT-HeliaRUS"
`ro-RO` | Rumänska | Man | ”Microsoft Server tal Text till tal-röst (ro-RO, Andrei)” | "ro-RO-Andrei"
`ru-RU` |Ryska| Kvinna | ”Microsoft Server tal Text till tal röst (ru-RU, Irina, Apollo)” | "ru-RU-Irina-Apollo"
| | | Man | ”Microsoft Server tal Text till tal röst (ru-RU, Pavel, Apollo)” | "ru-RU-Pavel-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal röst (ru-RU, EkaterinaRUS)” | ru-RU-EkaterinaRUS
`sk-SK` | Slovakiska | Man | ”Microsoft Server tal Text till tal röst (sk-SK, Filip)” | "sk-SK-Filip"
`sl-SI` | Slovenska | Man | ”Microsoft Server tal Text till tal röst (sl-SI, Lado)” | "sl-SI-Lado"
`sv-SE` | Svenska | Kvinna | ”Microsoft Server tal Text till tal-röst (sv-SE, HedvigRUS)” | "sv-SE-HedvigRUS"
`ta-IN` | Tamil (Indien) | Man | ”Microsoft Server tal Text till tal-röst (ta-IN-, Valluvar)” | "ta-IN-Valluvar"
`te-IN` | Telugu (Indien) | Kvinna | ”Microsoft Server tal Text till tal-röst (te-IN-, Chitra)” | "te-IN-Chitra"
`th-TH` | Thai | Man | ”Microsoft Server tal Text till tal röst (th-TH, Pattara)” | "th-TH-Pattara"
`tr-TR` | Turkiska | Kvinna | ”Microsoft Server tal Text till tal röst (tr-TR, SedaRUS)” | "tr-TR-SedaRUS"
`vi-VN` | Vietnamesiska | Man | ”Microsoft Server tal Text till tal röst (vi VN ett)” | "vi-VN-An"
`zh-CN` | Kinesiska (fastlandet) | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-CN, HuihuiRUS)” | "zh-CN-HuihuiRUS"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-CN, Yaoyao, Apollo)” | "zh-CN-Yaoyao-Apollo"
| | | Man | ”Microsoft Server tal Text till tal-röst (zh-CN, Kangkang, Apollo)” | "zh-CN-Kangkang-Apollo"
`zh-HK` | Kinesiska (Hongkong SAR) | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-HK Tracy, Apollo)” | "zh-HK-Tracy-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-HK TracyRUS)” | "zh-HK-TracyRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (zh-HK Danny, Apollo)” | "zh-HK-Danny-Apollo"
`zh-TW` | Kinesiska (Taiwan) | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-TW, Yating, Apollo)” | "zh-TW-Yating-Apollo"
| | | Kvinna | ”Microsoft Server tal Text till tal-röst (zh-TW, HanHanRUS)” | "zh-TW-HanHanRUS"
| | | Man | ”Microsoft Server tal Text till tal-röst (zh-TW, Zhiwei, Apollo)” | "zh-TW-Zhiwei-Apollo"

&dagger; *ar-tex stöder modern standard arabiska (MSA).*

> [!NOTE]
> Du kan antingen använda den fullständiga tjänst namns mappningen eller det korta röst namnet i ditt tal syntes begär Anden.

### <a name="customization"></a>Anpassning

Röst anpassning är tillgänglig för `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`och `zh-CN`. Välj rätt språk version som matchar de tränings data du behöver för att träna en anpassad röst modell. Om till exempel de inspelnings data du har talas på engelska med en brittisk accent väljer du `en-GB`.

> [!NOTE]
> Vi har inte stöd för dubbelriktad modell utbildning i anpassad röst, förutom kinesiska-engelska bi-språk. Välj "kinesiskt kinesiskt tvåspråkig" om du vill träna en kinesisk röst som även kan tala med engelska. Röstträning i alla språk börjar med en data uppsättning av 2000 + yttranden, med undantag för `en-US` och `zh-CN` där du kan börja med valfri storlek på tränings data.

## <a name="speech-translation"></a>Talöversättning

API: et för **tal översättning** stöder olika språk för översättning av tal till tal och tal till text. Käll språket måste alltid vara från språk tabellen för tal till text. Tillgängliga mål språk beror på om translation målet är tal eller text. Du kan översätta inkommande tal till fler än [60 språk](https://www.microsoft.com/translator/business/languages/). Det finns en delmängd av språk som är tillgängliga för [tal syntes](language-support.md#text-languages).

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
| Telugu      | `te`          |
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
* [Se identifiera tal iC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
