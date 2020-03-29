---
title: Språkstöd - Taltjänst
titleSuffix: Azure Cognitive Services
description: Taltjänsten stöder många språk för tal-till-text- och text-till-tal-konvertering, tillsammans med talöversättning. Den här artikeln innehåller en omfattande lista över språkstöd efter tjänstfunktion.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d01ab60790311649e424a98d5a08c6af0bca90f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336040"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Språk- och röststöd för taltjänsten

Språkstödet varierar beroende på taltjänstens funktioner. I följande tabeller sammanfattas språkstöd för erbjudanden om [tal-till-text,](#speech-to-text) [text-till-tal](#text-to-speech)och [talöversättning.](#speech-translation)

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech SDK och REST API stöder följande språk (språk). För att förbättra noggrannheten erbjuds anpassning för en delmängd av språken genom att ladda upp Ljud + Mänskligt märkta transkriptioner eller Relaterad text: Meningar. Uttalsanpassning är för närvarande `en-US` `de-DE`endast tillgänglig för och . Läs mer om anpassning [här](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Nationell inställning  | Språk                          | Stöds | Anpassningar                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabiska (UAE)                      | Ja       | Inga                                                |
| `ar-BH` | Arabiska (Bahrain), modern standard | Ja       | Språkmodell                                    |
| `ar-EG` | Arabiska (Egypten)                    | Ja       | Språkmodell                                    |
| `ar-KW` | Arabiska (Kuwait)                   | Ja       | Inga                                                |
| `ar-QA` | Arabiska (Qatar)                    | Ja       | Inga                                                |
| `ar-SA` | Arabiska (Saudiarabien)             | Ja       | Inga                                                |
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
| `gu-IN` | Gujarati (indisk)                 | Ja       | Språkmodell                                    |
| `hi-IN` | Hindi (Indien)                     | Ja       | Akustisk modell<br>Språkmodell                  |
| `it-IT` | Italienska (Italien)                   | Ja       | Akustisk modell<br>Språkmodell<br>Uttal |
| `ja-JP` | Japanska (Japan)                  | Ja       | Språkmodell                                    |
| `ko-KR` | Koreanska (Korea)                    | Ja       | Språkmodell                                    |
| `mr-IN` | Marathi (Indien)                   | Ja       | Språkmodell                                    |
| `nb-NO` | Norska (Bokmål) (Norge)       | Ja       | Språkmodell                                    |
| `nl-NL` | Nederländska (Nederländerna)               | Ja       | Språkmodell                                    |
| `pl-PL` | Polska (Polen)                   | Ja       | Språkmodell                                    |
| `pt-BR` | Portugisiska (Brasilien)               | Ja       | Akustisk modell<br>Språkmodell<br>Uttal |
| `pt-PT` | Portugisiska (Portugal)             | Ja       | Språkmodell                                    |
| `ru-RU` | Ryska (Ryssland)                  | Ja       | Akustisk modell<br>Språkmodell                  |
| `sv-SE` | Svenska (Sverige)                  | Ja       | Språkmodell                                    |
| `ta-IN` | Tamil (Indien)                     | Ja       | Språkmodell                                    |
| `te-IN` | Telugu (Indien)                    | Ja       | Inga                                                |
| `th-TH` | Thailändska (Thailand)                   | Ja       | Inga                                                |
| `tr-TR` | Turkiska (Turkiet)                  | Ja       | Inga                                                |
| `zh-CN` | Kinesiska (mandarin, förenklad)    | Ja       | Akustisk modell<br>Språkmodell                  |
| `zh-HK` | Kinesiska (kantonesiska, traditionella)  | Ja       | Språkmodell                                    |
| `zh-TW` | Kinesiska (taiwanesisk mandarin)      | Ja       | Språkmodell                                    |

## <a name="text-to-speech"></a>Text till tal

Både Microsoft Speech SDK- och REST-API:erna stöder dessa röster, som alla stöder ett visst språk och en viss dialekt, som identifieras av språk.

> [!IMPORTANT]
> Prissättningen varierar för standard, anpassade och neurala röster. Besök sidan [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) för ytterligare information.

### <a name="neural-voices"></a>Neurala röster

Neural text-till-tal är en ny typ av talsyntes som drivs av djupa neurala nätverk. När du använder en neural röst, syntetiserade tal är nästan omöjlig att skilja från de mänskliga inspelningarna.

Neurala röster kan användas för att göra interaktioner med chatbots och röstassistenter mer naturliga och engagerande, konvertera digitala texter som e-böcker till ljudböcker och förbättra i bilen navigationssystem. Med den människoliknande naturliga prosodien och tydliga artikulation av ord minskar neurala röster avsevärt lyssningströttheten när användare interagerar med AI-system.

Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

| Nationell inställning  | Språk            | Kön | Mappning av fullständigt tjänstnamn                                               | Kort röstnamn        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Tyska (Tyskland)    | Kvinna | "Microsoft Server Tal text till tal röst (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Engelska (USA)        | Kvinna | "Microsoft Server Tal text till tal röst (en-USA, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Engelska (USA)        | Man   | "Microsoft Server Tal text till tal röst (en-USA, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Italienska (Italien)     | Kvinna | "Microsoft Server Tal Text till tal Röst (it-IT, ElsaNeural)"      | "det-IT-ElsaNeural"      |
| `pt-BR` | Portugisiska (Brasilien) | Kvinna | "Microsoft Server Tal Text till tal Röst (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Kinesiska (mandarin, förenklad)  | Kvinna | "Microsoft Server Tal Text till tal Röst (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> Rösten `en-US-JessaNeural` har `en-US-AriaNeural`ändrats till . Om du använde "Jessa" innan, konvertera över till "Aria".

Mer information om hur du kan konfigurera och justera neurala röster finns i [Markeringsspråk för talsyntes](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Du kan använda antingen mappningen av fullständigt tjänstnamn eller det korta röstnamnet i dina begäranden om talsyntes.

### <a name="standard-voices"></a>Standardröster

Mer än 75 standardröster finns på över 45 språk och språk, vilket gör att du kan konvertera text till syntetiserat tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

| Nationell inställning | Språk | Kön | Mappning av fullständigt tjänstnamn | Kort namn |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arabiska (Egypten) | Kvinna | "Taltext för Microsoft Server till talröst (ar-EG, Hoda)" | "ar-EG-Hoda" |
| `ar-SA` | Arabiska (Saudiarabien) | Man | "Taltext för Microsoft Server till talröst (ar-SA, Naayf)" | "ar-SA-Naayf" |
| `bg-BG` | Bulgariska | Man | "Taltext för Microsoft Server till talröst (bg-BG, Ivan)" | "bg-BG-Ivan" |
| `ca-ES` | Katalanska (Spanien) | Kvinna | "Taltext för Microsoft Server till talröst (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" |
| `cs-CZ` | Tjeckiska | Man | "Microsoft Server Tal text till tal röst (CS-CZ, Jakub)" | "cs-CZ-Jakub" |
| `da-DK` | Danska | Kvinna | "Microsoft Server Tal text till tal röst (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Tyska (Österrike) | Man | "Taltext för Microsoft Server till talröst (de-AT, Michael)" | "de-AT-Michael" |
| `de-CH` | Tyska (Schweiz) | Man | "Microsoft Server Tal text till tal röst (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Tyska (Tyskland) | Kvinna | "Microsoft Server Tal text till tal röst (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Kvinna | "Microsoft Server Tal text till tal röst (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Man | "Microsoft Server Tal text till tal röst (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | Grekiska | Man | "Taltext för Microsoft Server till talröst (el-GR, Stefanos)" | "el-GR-Stefanos" |
| `en-AU` | Engelska (Australien) | Kvinna | "Taltext för Microsoft Server till talröst (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Kvinna | "Microsoft Server Tal text till tal röst (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Engelska (Kanada) | Kvinna | "Taltext för Microsoft Server till talröst (en-CA, Linda)" | "en-CA-Linda" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Engelska (Storbritannien) | Kvinna | "Taltext för Microsoft Server till talröst (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Man | "Microsoft Server Tal text till tal röst (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Engelska (Irland) | Man | "Taltext för Microsoft Server till talröst (en-IE, Sean)" | "en-IE-Sean" |
| `en-IN` | Engelska (Indien) | Kvinna | "Microsoft Server Tal text till tal röst (in-in, Heera, Apollo)" | "en-I-Heera-Apollo" |
|  |  | Kvinna | "Microsoft Server Tal text till tal röst (in-in, PriyaRUS)" | "en-I-PriyaRUS" |
|  |  | Man | "Microsoft Server Tal text till tal röst (in-in, Ravi, Apollo)" | "en-I-Ravi-Apollo" |
| `en-US` | Engelska (USA) | Kvinna | "Microsoft Server Tal text till tal röst (en-USA, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (en-USA, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Man | "Microsoft Server Tal text till tal röst (en-USA, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Man | "Taltext för Microsoft Server till talröst (en-USA, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Spanska (Spanien) | Kvinna | "Microsoft Server Tal text till talröst (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Man | "Microsoft Server Tal text till tal röst (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Spanska (Mexiko) | Kvinna | "Taltext för Microsoft Server till talröst (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Man | "Microsoft Server Tal text till tal röst (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | Finska | Kvinna | "Taltext för Microsoft Server till talröst (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" |
| `fr-CA` | Franska (Kanada) | Kvinna | "Taltext för Microsoft Server till talröst (fr-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Franska (Schweiz) | Man | "Taltext för Microsoft Server till talröst (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | Franska (Frankrike) | Kvinna | "Microsoft Server Tal text till tal röst (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Man | "Microsoft Server Tal text till tal röst (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Hebreiska (Israel) | Man | "Taltext för Microsoft Server till talröst (han-IL, Asaf)" | "han-IL-Asaf" |
| `hi-IN` | Hindi (Indien) | Kvinna | "Microsoft Server Tal text till tal röst (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo" |
|  |  | Kvinna | "Microsoft Server Tal text till tal röst (hi-IN, Kalpana)" | "hej-IN-Kalpana" |
|  |  | Man | "Microsoft Server Tal text till tal röst (hi-IN, Hemant)" | "hi-IN-Hemant" |
| `hr-HR` | Kroatiska | Man | "Taltext för Microsoft Server till talröst (hr-HR, Matej)" | "hr-HR-Matej" |
| `hu-HU` | Ungerska | Man | "Microsoft Server Tal text till tal röst (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonesiska | Man | "Taltext för Microsoft Server till talröst (id-ID, Andika)" | "id-ID-Andika" |
| `it-IT` | Italienska | Man | "Microsoft Server Tal text till tal röst (it-IT, Cosimo, Apollo)" | "det-IT-Cosimo-Apollo" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (it-IT, LuciaRUS)" | "det-IT-LuciaRUS" |
| `ja-JP` | Japanska | Kvinna | "Microsoft Server Tal text till tal röst (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Man | "Microsoft Server Tal Text till tal Röst (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (ja-JP, HarukaRUS)" | "ja-JP-Harukarus" |
| `ko-KR` | Koreansk | Kvinna | "Microsoft Server Tal text till talröst (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajiska | Man | "Taltext för Microsoft Server till talröst (ms-MY, Rizwan)" | "ms-MY-Rizwan" |
| `nb-NO` | Norska | Kvinna | "Taltext för Microsoft Server till talröst (obs-NO, HuldaRUS)" | "nb-NO-HuldaRUS" |
| `nl-NL` | Nederländska | Kvinna | "Taltext för Microsoft Server till talröst (NL-NL, HannaRUS)" | "nl-NL-HannaRUS" |
| `pl-PL` | Polska | Kvinna | "Microsoft Server Tal text till tal röst (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugisiska (Brasilien) | Kvinna | "Taltext för Microsoft Server till talröst (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Man | "Microsoft Server Tal text till tal röst (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugisiska (Portugal) | Kvinna | "Microsoft Server Tal text till talröst (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumänska | Man | "Taltext för Microsoft Server till talröst (ro-RO, Andrei)" | "Ro-RO-Andrei" |
| `ru-RU` | Ryska | Kvinna | "Microsoft Server Tal Text till tal Röst (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Man | "Microsoft Server Tal Text till tal Röst (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Kvinna | "Taltext för Microsoft Server till talröst (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Slovakiska | Man | "Taltext för Microsoft Server till talröst (sk-SK, Filip)" | "sk-SK-Filip" |
| `sl-SI` | Slovenska | Man | "Taltext för Microsoft Server till talröst (sl-SI, Lado)" | "sl-SI-Lado" |
| `sv-SE` | Svenska | Kvinna | "Microsoft Server Tal text till talröst (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (Indien) | Man | "Microsoft Server Tal text till tal röst (ta-IN, Valluvar)" | "ta-IN-Valluvar" |
| `te-IN` | Telugu (Indien) | Kvinna | "Microsoft Server Tal text till tal röst (te-IN, Chitra)" | "te-IN-Chitra" |
| `th-TH` | Thailändska | Man | "Taltext för Microsoft Server till talröst (th-TH, Pattara)" | "th-TH-Pattara" |
| `tr-TR` | Turkiska (Turkiet) | Kvinna | "Taltext för Microsoft Server till talröst (tr-TR, SedaRUS)" | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamesiska | Man | "Taltext för Microsoft Server till talröst (vi-VN, An)" | "vi-VN-An" |
| `zh-CN` | Kinesiska (mandarin, förenklad) | Kvinna | "Microsoft Server Tal text till tal röst (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Kvinna | "Microsoft Server Tal text till tal röst (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Man | "Microsoft Server Tal text till tal röst (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Kinesiska (kantonesiska, traditionella) | Kvinna | "Microsoft Server Tal Text till tal Röst (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Kvinna | "Microsoft Server Tal Text till tal Röst (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Man | "Microsoft Server Tal Text till tal Röst (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Kinesiska (taiwanesisk mandarin) | Kvinna | "Microsoft Server Tal text till tal röst (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Kvinna | "Microsoft Server Tal text till tal röst (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Man | "Microsoft Server Tal Text till tal Röst (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG stöder modern standard arabiska (MSA).*

> [!IMPORTANT]
> Rösten `en-US-Jessa` har `en-US-Aria`ändrats till . Om du använde "Jessa" innan, konvertera över till "Aria".

> [!TIP]
> Du kan använda antingen mappningen av fullständigt tjänstnamn eller det korta röstnamnet i dina begäranden om talsyntes.

### <a name="customization"></a>Anpassning

Röstanpassning är `de-DE` `en-GB`tillgängligt `en-IN` `en-US`för `es-MX` `fr-FR`, `it-IT` `pt-BR`, `zh-CN`, , , , , och . Välj rätt språk som matchar de träningsdata du har för att träna en anpassad röstmodell. Om till exempel inspelningsdata som du har talas på `en-GB`engelska med brittisk accent väljer du .

> [!NOTE]
> Vi stöder inte tvåspråkig modellutbildning i Custom Voice, förutom den kinesisk-engelska tvåspråkiga. Välj "Kinesisk-engelska tvåspråkig" om du vill träna en kinesisk röst som kan tala engelska också. Röstutbildning på alla språk börjar med en datauppsättning med 2 000 `en-US` `zh-CN` + yttranden, förutom och där du kan börja med valfri storlek på träningsdata.

## <a name="speech-translation"></a>Talöversättning

Api:et **för talöversättning** stöder olika språk för tal-till-tal- och tal-till-text-översättning. Källspråket måste alltid finnas i språktabellen Tal till text. Vilka målspråk som är tillgängliga beror på om översättningsmålet är tal eller text. Du kan översätta inkommande tal till mer än [60 språk](https://www.microsoft.com/translator/business/languages/). En delmängd av språken är tillgängliga för [talsyntes](language-support.md#text-languages).

### <a name="text-languages"></a>Textspråk

| Textspråk           | Språkkod |
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
| Fijianska                  | `fj`          |
| Filipino                | `fil`         |
| Finska                 | `fi`          |
| Franska                  | `fr`          |
| Tyska                  | `de`          |
| Grekiska                   | `el`          |
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
| Kiswahili               | `sw`          |
| Klingonska                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Koreansk                  | `ko`          |
| Lettiska                 | `lv`          |
| Litauiska              | `lt`          |
| Madagaskiska                | `mg`          |
| Malajiska                   | `ms`          |
| Malayalam               | `ml`          |
| Maltesiska                 | `mt`          |
| Norska               | `nb`          |
| Persiska                 | `fa`          |
| Polska                  | `pl`          |
| Portugisiska (Brasilien)     | `pt-br`       |
| Portugisiska (Portugal)   | `pt-pt`       |
| Punjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Rumänska                | `ro`          |
| Ryska                 | `ru`          |
| Samoanska                  | `sm`          |
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
| Tongan (ton)                  | `to`          |
| Turkiska                 | `tr`          |
| Ukrainska               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamesiska              | `vi`          |
| Walesiska                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>Nästa steg

* [Skaffa provprenumerationen för Taltjänsten](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du känner igen tal i C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
