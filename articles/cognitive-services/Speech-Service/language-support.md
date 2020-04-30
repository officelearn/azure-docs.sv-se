---
title: Språk stöd – tal tjänsten
titleSuffix: Azure Cognitive Services
description: Tal tjänsten har stöd för flera språk för konvertering från tal till text och text till tal, tillsammans med tal översättning. Den här artikeln innehåller en omfattande lista över språk stöd för tjänst funktionen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 0b6fea381bd6b4aa8ad3e7061e6f632176c41033
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113841"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Språk-och röst stöd för tal tjänsten

Språk stöd varierar beroende på funktioner i tal tjänster. Följande tabeller sammanfattar språk stöd för [tal-till-text](#speech-to-text)-, [text-till-tal](#text-to-speech)-och [röst översättnings](#speech-translation) tjänst erbjudanden.

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech SDK och REST API stöder följande språk (lokala). För att förbättra noggrannheten erbjuds anpassning för en delmängd av språken genom uppladdning av ljud + mänskligt avskrifter eller relaterad text: meningar. Uttal-anpassning är för närvarande endast `en-US` tillgängligt `de-DE`för och. Läs mer om anpassning [här](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Nationell inställning  | Språk                          | Stöds | Anpassningar                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabiska (Förenade Arabemiraten)                      | Ja       | Nej                                                |
| `ar-BH` | Arabiska (Bahrain), modern standard | Ja       | Språkmodell                                    |
| `ar-EG` | Arabiska (Egypten)                    | Ja       | Språkmodell                                    |
| `ar-KW` | Arabiska (Kuwait)                   | Ja       | Nej                                                |
| `ar-QA` | Arabiska (Qatar)                    | Ja       | Nej                                                |
| `ar-SA` | Arabiska (Saudiarabien)             | Ja       | Nej                                                |
| `ar-SY` | Arabiska (Syrien)                    | Ja       | Språkmodell                                    |
| `ca-ES` | Katalanska                           | Ja       | Språkmodell                                    |
| `da-DK` | Danska (Danmark)                  | Ja       | Språkmodell                                    |
| `de-DE` | Tyska (Tyskland)                  | Ja       | Akustisk modell<br>Språkmodell<br>Uttal |
| `en-AU` | Engelska (Australien)               | Ja       | Akustisk modell<br>Språkmodell                  |
| `en-CA` | Engelska (Kanada)                  | Ja       | Akustisk modell<br>Språkmodell                  |
| `en-GB` | Engelska (Storbritannien)          | Ja       | Akustisk modell<br>Språkmodell<br>Uttal |
| `en-IN` | Engelska (Indien)                   | Ja       | Akustisk modell<br>Språkmodell                  |
| `en-NZ` | Engelska (Nya Zeeland)             | Ja       | Akustisk modell<br>Språkmodell                  |
| `en-US` | Engelska (USA)           | Ja       | Akustisk modell<br>Språkmodell<br>Uttal |
| `es-ES` | Spanska (Spanien)                   | Ja       | Akustisk modell<br>Språkmodell                  |
| `es-MX` | Spanska (Mexiko)                  | Ja       | Akustisk modell<br>Språkmodell                  |
| `fi-FI` | Finska (Finland)                 | Ja       | Språkmodell                                    |
| `fr-CA` | Franska (Kanada)                   | Ja       | Akustisk modell<br>Språkmodell                  |
| `fr-FR` | Franska (Frankrike)                   | Ja       | Akustisk modell<br>Språkmodell<br>Uttal |
| `gu-IN` | Gujarati (indiska)                 | Ja       | Språkmodell                                    |
| `hi-IN` | Hindi (Indien)                     | Ja       | Akustisk modell<br>Språkmodell                  |
| `it-IT` | Italienska (Italien)                   | Ja       | Akustisk modell<br>Språkmodell<br>Uttal |
| `ja-JP` | Japanska (Japan)                  | Ja       | Språkmodell                                    |
| `ko-KR` | Koreanska (Korea)                    | Ja       | Språkmodell                                    |
| `mr-IN` | Marathi (Indien)                   | Ja       | Språkmodell                                    |
| `nb-NO` | Norska (bokmål) (Norge)       | Ja       | Språkmodell                                    |
| `nl-NL` | Nederländska (Nederländerna)               | Ja       | Språkmodell                                    |
| `pl-PL` | Polska (Polen)                   | Ja       | Språkmodell                                    |
| `pt-BR` | Portugisiska (Brasilien)               | Ja       | Akustisk modell<br>Språkmodell<br>Uttal |
| `pt-PT` | Portugisiska (Portugal)             | Ja       | Språkmodell                                    |
| `ru-RU` | Ryska (Ryssland)                  | Ja       | Akustisk modell<br>Språkmodell                  |
| `sv-SE` | Svenska (Sverige)                  | Ja       | Språkmodell                                    |
| `ta-IN` | Tamiliska (Indien)                     | Ja       | Språkmodell                                    |
| `te-IN` | Telugu (Indien)                    | Ja       | Nej                                                |
| `th-TH` | Thailändska (Thailand)                   | Ja       | Nej                                                |
| `tr-TR` | Turkiska (Turkiet)                  | Ja       | Nej                                                |
| `zh-CN` | Kinesiska (mandariner, förenklad)    | Ja       | Akustisk modell<br>Språkmodell                  |
| `zh-HK` | Kinesiska (kantonesiska, traditionell)  | Ja       | Språkmodell                                    |
| `zh-TW` | Kinesiska (Taiwan-mandariner)      | Ja       | Språkmodell                                    |

## <a name="text-to-speech"></a>Text till tal

Både Microsoft Speech SDK och REST-API: er har stöd för dessa röster, som var och en har stöd för ett särskilt språk och dialekt, som identifieras av nationella inställningar.

> [!IMPORTANT]
> Prissättningen varierar för standard-, anpassade och neurala röster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Neurala-röster

Neurala text till tal är en ny typ av tal syntes som drivs av djup neurala nätverk. När du använder en neurala-röst är syntetiskt tal nästan lätt att skilja från de mänskliga inspelningarna.

Neurala röster kan användas för att göra interaktioner med chattrobotar och röst assistenter mer naturligt och engagerande, konvertera digitala texter som e-böcker till Audiobooks och förbättra navigerings systemen i bilen. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när användarna interagerar med AI-system.

Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

| Nationell inställning  | Språk            | Kön | Fullständig tjänst namns mappning                                               | Kort röst namn        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Tyska (Tyskland)    | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, KatjaNeural)"     | "de-KatjaNeural"     |
| `en-US` | Engelska (USA)        | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Engelska (USA)        | Man   | "Microsoft Server Speech Text till tal Voice (en-US, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Italienska (Italien)     | Kvinna | "Microsoft Server Speech Text till tal Voice (IT-IT, ElsaNeural)"      | "IT-ElsaNeural"      |
| `pt-BR` | Portugisiska (Brasilien) | Kvinna | "Microsoft Server Speech Text till tal Voice (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Kinesiska (mandariner, förenklad)  | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> `en-US-JessaNeural` Rösten har ändrats till `en-US-AriaNeural`. Om du använde "Jessa" tidigare, konvertera till "Aria".

Information om hur du kan konfigurera och justera neurala-röster finns i [tal syntes märknings språk](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Du kan antingen använda den fullständiga tjänst namns mappningen eller det korta röst namnet i ditt tal syntes begär Anden.

### <a name="standard-voices"></a>Standard röster

Det finns fler än 75 standard röster i över 45 språk och nationella inställningar, vilket gör att du kan konvertera text till syntetiskt tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

| Nationell inställning | Språk | Kön | Fullständig tjänst namns mappning | Kort namn |
|--|--|--|--|--|
| <sup>81.1</sup>`ar-EG` | Arabiska (Egypten) | Kvinna | "Microsoft Server Speech Text till tal Voice (ar-tex, Hoda)" | "ar-tex-Hoda" |
| `ar-SA` | Arabiska (Saudiarabien) | Man | "Microsoft Server Speech Text till tal Voice (ar-SA, Naayf)" | "ar-SA-Naayf" |
| `bg-BG` | Bulgariska | Man | "Microsoft Server Speech Text till tal Voice (BG-BG, Ivan)" | "BG-BG-Ivan" |
| `ca-ES` | Katalanska (Spanien) | Kvinna | "Microsoft Server Speech Text till tal Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" |
| `cs-CZ` | Tjeckiska | Man | "Microsoft Server Speech Text till tal Voice (CS-CZ, Jakub)" | "CS-CZ-Jakub" |
| `da-DK` | Danska | Kvinna | "Microsoft Server Speech Text till tal Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Tyska (Österrike) | Man | "Microsoft Server Speech Text till tal Voice (avinstallations, Michael)" | "avinstallation-Michael" |
| `de-CH` | Tyska (Schweiz) | Man | "Microsoft Server Speech Text till tal Voice (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Tyska (Tyskland) | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, Hedda)" | "de-Hedda" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, HeddaRUS)" | "de-HeddaRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (de-DE, Stefan, Apollo)" | "avApollo-Stefan-" |
| `el-GR` | Grekiska | Man | "Microsoft Server Speech Text till tal Voice (El-GR, Stefanos)" | "El-GR-Stefanos" |
| `en-AU` | Engelska (Australien) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Engelska (Kanada) | Kvinna | "Microsoft Server Speech Text till tal Voice (sv-CA, Linda)" | "en-CA-Linda" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Engelska (Storbritannien) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Engelska (Irland) | Man | "Microsoft Server Speech Text till tal Voice (en-IE, Stefan)" | "en-IE-Stefan" |
| `en-IN` | Engelska (Indien) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" |
| `en-US` | Engelska (USA) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Spanska (Spanien) | Kvinna | "Microsoft Server Speech Text till tal Voice (es-ES, Lisa, Apollo)" | "es-ES-Lisa-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Spanska (Mexiko) | Kvinna | "Microsoft Server Speech Text till tal Voice (ES-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (ES-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | Finska | Kvinna | "Microsoft Server Speech Text till tal Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" |
| `fr-CA` | Franska (Kanada) | Kvinna | "Microsoft Server Speech Text till tal Voice (fr-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Franska (Schweiz) | Man | "Microsoft Server Speech Text till tal Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | Franska (Frankrike) | Kvinna | "Microsoft Server Speech Text till tal Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (fr-FR, Johan, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Hebreiska (Israel) | Man | "Microsoft Server Speech Text till tal Voice (HE-IL, Asaf)" | "he-IL-Asaf" |
| `hi-IN` | Hindi (Indien) | Kvinna | "Microsoft Server Speech Text till tal Voice (Hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (Hi-IN, Kalpana)" | "Hi-IN-Kalpana" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (Hi-IN, Hemant)" | "Hi-IN-Hemant" |
| `hr-HR` | Kroatiska | Man | "Microsoft Server Speech Text till tal Voice (HR-HR, Matej)" | "HR-HR-Matej" |
| `hu-HU` | Ungerska | Man | "Microsoft Server Speech Text till tal Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonesiska | Man | "Microsoft Server Speech Text till tal Voice (ID-ID, andika)" | "ID-ID-andika" |
| `it-IT` | Italienska | Man | "Microsoft Server Speech Text till tal Voice (IT-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (IT-IT, LuciaRUS)" | "IT-LuciaRUS" |
| `ja-JP` | Japanska | Kvinna | "Microsoft Server Speech Text till tal Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | Koreansk | Kvinna | "Microsoft Server Speech Text till tal Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajiska | Man | "Microsoft Server Speech Text till tal Voice (MS-MY, Rizwan)" | "MS-MY-Rizwan" |
| `nb-NO` | Norska | Kvinna | "Microsoft Server Speech Text till tal Voice (NB-NO, HuldaRUS)" | "NB-NO-HuldaRUS" |
| `nl-NL` | Nederländska | Kvinna | "Microsoft Server Speech Text till tal Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS" |
| `pl-PL` | Polska | Kvinna | "Microsoft Server Speech Text till tal Voice (PL-PL, PaulinaRUS)" | "PL-PL-PaulinaRUS" |
| `pt-BR` | Portugisiska (Brasilien) | Kvinna | "Microsoft Server Speech Text till tal Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugisiska (Portugal) | Kvinna | "Microsoft Server Speech Text till tal Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumänska | Man | "Microsoft Server Speech Text till tal Voice (RO-RO, tillhör)" | "RO-RO-tillhör" |
| `ru-RU` | Ryska | Kvinna | "Microsoft Server Speech Text till tal Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Slovakiska | Man | "Microsoft Server Speech Text till tal Voice (sk-SK, Filip)" | "sk-SK-Filip" |
| `sl-SI` | Slovenska | Man | "Microsoft Server Speech Text till tal Voice (SL-SI, Lado)" | "SL-SI-Lado" |
| `sv-SE` | Svenska | Kvinna | "Microsoft Server Speech Text till tal Voice (sa-SE, HedvigRUS)" | "sa-SE-HedvigRUS" |
| `ta-IN` | Tamiliska (Indien) | Man | "Microsoft Server Speech Text till tal Voice (ta-IN, Valluvar)" | "ta-IN-Valluvar" |
| `te-IN` | Telugu (Indien) | Kvinna | "Microsoft Server Speech Text till tal Voice (te-IN, Chitra)" | "te-IN-Chitra" |
| `th-TH` | Thailändska | Man | "Microsoft Server Speech Text till tal Voice (th-TH, Pattara)" | "th-Pattara" |
| `tr-TR` | Turkiska (Turkiet) | Kvinna | "Microsoft Server Speech Text till tal Voice (tr-TR, SedaRUS)" | "TR-TR-SedaRUS" |
| `vi-VN` | Vietnamesiska | Man | "Microsoft Server Speech Text till tal Voice (vi-VN, a)" | "vi-VN-a" |
| `zh-CN` | Kinesiska (mandariner, förenklad) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Kinesiska (kantonesiska, traditionell) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Kinesiska (Taiwan-mandariner) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Man | "Microsoft Server Speech Text till tal Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *ar-tex stöder modern standard arabiska (MSA).*

> [!IMPORTANT]
> `en-US-Jessa` Rösten har ändrats till `en-US-Aria`. Om du använde "Jessa" tidigare, konvertera till "Aria".

> [!TIP]
> Du kan antingen använda den fullständiga tjänst namns mappningen eller det korta röst namnet i ditt tal syntes begär Anden.

### <a name="customization"></a>Anpassning

Röst anpassning är tillgänglig för `de-DE`, `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT`,,,,, och `zh-CN` `pt-BR` Välj rätt språk version som matchar de tränings data du behöver för att träna en anpassad röst modell. Om till exempel de inspelnings data du har talas på engelska med en brittisk accent väljer `en-GB`du.

> [!NOTE]
> Vi har inte stöd för dubbelriktad modell utbildning i anpassad röst, förutom kinesiska-engelska bi-språk. Välj "kinesiskt kinesiskt tvåspråkig" om du vill träna en kinesisk röst som även kan tala med engelska. Röstträning i alla språk börjar med en data uppsättning av 2000 + yttranden, med undantag för `en-US` och `zh-CN` där du kan börja med valfri storlek på tränings data.

## <a name="speech-translation"></a>Talöversättning

API: et för **tal översättning** stöder olika språk för översättning av tal till tal och tal till text. Käll språket måste alltid vara från språk tabellen för tal till text. Vilka mål språk som är tillgängliga beror på om översättnings målet är tal eller text. Du kan översätta inkommande tal till fler än [60 språk](https://www.microsoft.com/translator/business/languages/). Det finns en delmängd av språk som är tillgängliga för [tal syntes](language-support.md#text-languages).

### <a name="text-languages"></a>Text språk

| Text språk           | Språkkod |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabiska                  | `ar`          |
| Bangla                  | `bn`          |
| Bosniska (latinsk)         | `bs`          |
| Bulgariska               | `bg`          |
| Kantonesiska (traditionell) | `yue`         |
| Katalanska                 | `ca`          |
| Kinesiska, förenklad      | `zh-Hans`     |
| Kinesiska, traditionell     | `zh-Hant`     |
| Kroatiska                | `hr`          |
| Tjeckiska                   | `cs`          |
| Danska                  | `da`          |
| Nederländska                   | `nl`          |
| Svenska                 | `en`          |
| Estniska                | `et`          |
| Fijian                  | `fj`          |
| Filipino                | `fil`         |
| Finska                 | `fi`          |
| Franska                  | `fr`          |
| Tyska                  | `de`          |
| Grekiska                   | `el`          |
| Gujarati                | `gu`          |
| Haitiska          | `ht`          |
| Hebreiska                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Ungerska               | `hu`          |
| Indonesiska              | `id`          |
| Iriska                   | `ga`          |
| Italienska                 | `it`          |
| Japanska                | `ja`          |
| Kannada                 | `kn`          |
| Swahili               | `sw`          |
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Koreansk                  | `ko`          |
| Lettiska                 | `lv`          |
| Litauiska              | `lt`          |
| Madagaskisk                | `mg`          |
| Malajiska                   | `ms`          |
| Malayalam               | `ml`          |
| Maltesiska                 | `mt`          |
| Maori                   | `mi`          |
| Marathi                 | `mr`          |
| Norska               | `nb`          |
| Persiska                 | `fa`          |
| Polska                  | `pl`          |
| Portugisiska (Brasilien)     | `pt-br`       |
| Portugisiska (Portugal)   | `pt-pt`       |
| Punjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Rumänska                | `ro`          |
| Ryska                 | `ru`          |
| Samoan                  | `sm`          |
| Serbiska (kyrillisk)      | `sr-Cyrl`     |
| Serbiska (latinsk) (Serbien)         | `sr-Latn`     |
| Slovakiska                  | `sk`          |
| Slovenska               | `sl`          |
| Spanska                 | `es`          |
| Svenska                 | `sv`          |
| Tahitian                | `ty`          |
| Tamilska                   | `ta`          |
| Telugu                  | `te`          |
| Thailändska                    | `th`          |
| Tonganska                  | `to`          |
| Turkiska                 | `tr`          |
| Ukrainska               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamesiska              | `vi`          |
| Walesiska                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>Nästa steg

* [Hämta utvärderings prenumerationen för din röst tjänst](https://azure.microsoft.com/try/cognitive-services/)
* [Se känna igen tal i C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
