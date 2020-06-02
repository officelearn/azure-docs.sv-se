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
ms.openlocfilehash: 437dc18dc16e879e95ff4ec7c1a9ab7ec3f17bef
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266006"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Språk-och röst stöd för tal tjänsten

Språk stöd varierar beroende på funktioner i tal tjänster. Följande tabeller sammanfattar språk stöd för [tal-till-text](#speech-to-text)-, [text-till-tal](#text-to-speech)-och [röst översättnings](#speech-translation) tjänst erbjudanden.

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech SDK och REST API stöder följande språk (lokala). 

För att förbättra noggrannheten erbjuds anpassning för en delmängd av språken genom uppladdning av **ljud + mänskligt avskrifter** eller **relaterad text: meningar**. Mer information om anpassning finns i [Kom igång med Custom Speech](how-to-custom-speech.md).

Mer information om hur du kan förbättra uttal finns i [förbättra en modell för Custom Speech](how-to-custom-speech-improve-accuracy.md#add-new-words-with-pronunciation).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Nationell inställning  | Språk                          | Stöds | Anpassningar                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabiska (Förenade Arabemiraten)                      | Ja       | Inga                                                |
| `ar-BH` | Arabiska (Bahrain), modern standard | Yes       | Språkmodell                                    |
| `ar-EG` | Arabiska (Egypten)                    | Yes       | Språkmodell                                    |
| `ar-IL` | Arabiska (Israel)                   | Ja       | Inga                                                |
| `ar-KW` | Arabiska (Kuwait)                   | Ja       | Inga                                                |
| `ar-PS` | Arabiska (Palestina)                | Ja       | Inga                                                |
| `ar-QA` | Arabiska (Qatar)                    | Ja       | Inga                                                |
| `ar-SA` | Arabiska (Saudiarabien)             | Ja       | Inga                                                |
| `ar-SY` | Arabiska (Syrien)                    | Yes       | Språkmodell                                    |
| `ca-ES` | Katalanska                           | Yes       | Språkmodell                                    |
| `da-DK` | Danska (Danmark)                  | Yes       | Språkmodell                                    |
| `de-DE` | Tyska (Tyskland)                  | Yes       | Akustisk modell<br>Språkmodell<br>Uttal |
| `en-AU` | Engelska (Australien)               | Yes       | Akustisk modell<br>Språkmodell                  |
| `en-CA` | Engelska (Kanada)                  | Yes       | Akustisk modell<br>Språkmodell                  |
| `en-GB` | Engelska (Storbritannien)          | Yes       | Akustisk modell<br>Språkmodell<br>Uttal |
| `en-IN` | Engelska (Indien)                   | Yes       | Akustisk modell<br>Språkmodell                  |
| `en-NZ` | Engelska (Nya Zeeland)             | Yes       | Akustisk modell<br>Språkmodell                  |
| `en-US` | Engelska (USA)           | Yes       | Akustisk modell<br>Språkmodell<br>Uttal |
| `es-ES` | Spanska (Spanien)                   | Yes       | Akustisk modell<br>Språkmodell                  |
| `es-MX` | Spanska (Mexiko)                  | Yes       | Akustisk modell<br>Språkmodell                  |
| `fi-FI` | Finska (Finland)                 | Yes       | Språkmodell                                    |
| `fr-CA` | Franska (Kanada)                   | Yes       | Akustisk modell<br>Språkmodell                  |
| `fr-FR` | Franska (Frankrike)                   | Yes       | Akustisk modell<br>Språkmodell<br>Uttal |
| `gu-IN` | Gujarati (indiska)                 | Yes       | Språkmodell                                    |
| `hi-IN` | Hindi (Indien)                     | Yes       | Akustisk modell<br>Språkmodell                  |
| `it-IT` | Italienska (Italien)                   | Yes       | Akustisk modell<br>Språkmodell<br>Uttal |
| `ja-JP` | Japanska (Japan)                  | Yes       | Språkmodell                                    |
| `ko-KR` | Koreanska (Korea)                    | Yes       | Språkmodell                                    |
| `mr-IN` | Marathi (Indien)                   | Yes       | Språkmodell                                    |
| `nb-NO` | Norska (bokmål) (Norge)       | Yes       | Språkmodell                                    |
| `nl-NL` | Nederländska (Nederländerna)               | Yes       | Språkmodell                                    |
| `pl-PL` | Polska (Polen)                   | Yes       | Språkmodell                                    |
| `pt-BR` | Portugisiska (Brasilien)               | Yes       | Akustisk modell<br>Språkmodell<br>Uttal |
| `pt-PT` | Portugisiska (Portugal)             | Yes       | Språkmodell                                    |
| `ru-RU` | Ryska (Ryssland)                  | Yes       | Akustisk modell<br>Språkmodell                  |
| `sv-SE` | Svenska (Sverige)                  | Yes       | Språkmodell                                    |
| `ta-IN` | Tamiliska (Indien)                     | Yes       | Språkmodell                                    |
| `te-IN` | Telugu (Indien)                    | Ja       | Inga                                                |
| `th-TH` | Thailändska (Thailand)                   | Ja       | Inga                                                |
| `tr-TR` | Turkiska (Turkiet)                  | Yes       | Språkmodell                                    |
| `zh-CN` | Kinesiska (mandariner, förenklad)    | Yes       | Akustisk modell<br>Språkmodell                  |
| `zh-HK` | Kinesiska (kantonesiska, traditionell)  | Yes       | Språkmodell                                    |
| `zh-TW` | Kinesiska (Taiwan-mandariner)      | Yes       | Språkmodell                                    |

## <a name="text-to-speech"></a>Text till tal

Både Microsoft Speech SDK och REST-API: er har stöd för dessa röster, som var och en har stöd för ett särskilt språk och dialekt, som identifieras av nationella inställningar. Du kan också få en fullständig lista över språk och röster som stöds för varje region/slut punkt via [API: t för röster/listor](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Prissättningen varierar för standard-, anpassade och neurala röster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Neurala-röster

Neurala text till tal är en ny typ av tal syntes som drivs av djup neurala nätverk. När du använder en neurala-röst är syntetiskt tal nästan lätt att skilja från de mänskliga inspelningarna.

Neurala röster kan användas för att göra interaktioner med chattrobotar och röst assistenter mer naturligt och engagerande, konvertera digitala texter som e-böcker till Audiobooks och förbättra navigerings systemen i bilen. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när användarna interagerar med AI-system.

Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

|Nationell inställning  | Språk            | Kön | Röst namn | Format stöd |
|--|--|--|--|--|
| `de-DE` | Tyska (Tyskland)                | `Female` | "de-KatjaNeural"      | Allmänt |
| `en-AU` | Engelska (Australien)             | `Female` | "en-AU-NatashaNeural"    | Allmänt |
| `en-CA` | Engelska (Kanada)                | `Female` | "en-CA-ClaraNeural"      | Allmänt |
| `en-GB` | Engelska (Storbritannien)                    | `Female` | "en-GB-LibbyNeural"      | Allmänt |
|         |                                 | `Female` | "en-GB-MiaNeural"        | Allmänt |
| `en-US` | Engelska (USA)                    | `Female` | "en-US-AriaNeural"       | Allmänt, flera röst format är tillgängliga |
|         |                                 | `Male`   | "en-US-GuyNeural"        | Allmänt |
| `es-ES` | Spanska (Spanien)                 | `Female` | "es-ES-ElviraNeural"     | Allmänt |
| `es-MX` | Spanska (Mexiko)                | `Female` | "es-MX-DaliaNeural"      | Allmänt |
| `fr-CA` | Franska (Kanada)                 | `Female` | "fr-CA-SylvieNeural"     | Allmänt |
| `fr-FR` | Franska (Frankrike)                 | `Female` | "fr-FR-DeniseNeural"     | Allmänt |
| `it-IT` | Italienska (Italien)                 | `Female` | "IT-ElsaNeural"       | Allmänt |
| `ja-JP` | Japanska                        | `Female` | "ja-JP-NanamiNeural"     | Allmänt |
| `ko-KR` | Koreanska                          | `Female` | "ko-KR-SunHiNeural"      | Allmänt |
| `nb-NO` | Norska                       | `Female` | "NB-NO-IselinNeural"     | Allmänt |
| `pt-BR` | Portugisiska (Brasilien)             | `Female` | "pt-BR-FranciscaNeural"  | Allmänt |
| `tr-TR` | Turkiska                         | `Female` | "TR-TR-EmelNeural"       | Allmänt |
| `zh-CN` | Kinesiska (mandariner, förenklad)  | `Female` | "zh-CN-XiaoxiaoNeural"   | Allmänt, flera röst format är tillgängliga |
|         |                                 | `Female` | "zh-CN-XiaoyouNeural"    | Röst för barn, optimerad för artikel-berättarröst |
|         |                                 | `Male`   | "zh-CN-YunyangNeural"    | Optimerad för nyhets läsning, flera röst format är tillgängliga |
|         |                                 | `Male`   | "zh-CN-YunyeNeural"      | Optimerad för artikel-berättarröst |

> [!IMPORTANT]
> `en-US-JessaNeural`Rösten har ändrats till `en-US-AriaNeural` . Om du använde "Jessa" tidigare, konvertera till "Aria".

Information om hur du kan konfigurera och justera neurala-röster finns i [tal syntes märknings språk](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Du kan fortsätta att använda den fullständiga tjänst namns mappningen, t. ex. "Microsoft Server Speech Text till tal Voice (en-US, AriaNeural)" i din begäran om tal syntes.

### <a name="standard-voices"></a>Standard röster

Det finns fler än 75 standard röster i över 45 språk och nationella inställningar, vilket gör att du kan konvertera text till syntetiskt tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

| Nationell inställning | Språk | Kön | Röst namn |
|--|--|--|--|
| <sup>81.1</sup>`ar-EG` | Arabiska (Egypten) | `Female` | "ar-tex-Hoda" |
| `ar-SA` | Arabiska (Saudiarabien) | `Male` | "ar-SA-Naayf" |
| `bg-BG` | Bulgariska | `Male` |  "BG-BG-Ivan" |
| `ca-ES` | Katalanska | `Female` |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Tjeckiska | `Male` | "CS-CZ-Jakub" |
| `da-DK` | Danska | `Female` |  "da-DK-HelleRUS" |
| `de-AT` | Tyska (Österrike) | `Male` | "avinstallation-Michael" |
| `de-CH` | Tyska (Schweiz) | `Male` |  "de-CH-Karsten" |
| `de-DE` | Tyska (Tyskland) | `Female` |  "de-Hedda" |
|  |  | `Female` | "de-HeddaRUS" |
|  |  | `Male` |  "avApollo-Stefan-" |
| `el-GR` | Grekiska | `Male` | "El-GR-Stefanos" |
| `en-AU` | Engelska (Australien) | `Female` |  "en-AU-Catherine" |
|  |  | `Female` |  "en-AU-HayleyRUS" |
| `en-CA` | Engelska (Kanada) | `Female` |  "en-CA-Linda" |
|  |  | `Female` |  "en-CA-HeatherRUS" |
| `en-GB` | Engelska (Storbritannien) | `Female` |  "en-GB-Susan-Apollo" |
|  |  | `Female` |  "en-GB-HazelRUS" |
|  |  | `Male` |  "en-GB-George-Apollo" |
| `en-IE` | Engelska (Irland) | `Male` | "en-IE-Stefan" |
| `en-IN` | Engelska (Indien) | `Female` | "en-IN-Heera-Apollo" |
|  |  | `Female` |  "en-IN-PriyaRUS" |
|  |  | `Male` |  "en-IN-Ravi-Apollo" |
| `en-US` | Engelska (USA) | `Female` |  "en-US-ZiraRUS" |
|  |  | `Female` | "en-US-AriaRUS" |
|  |  | `Male` | "en-US-BenjaminRUS" |
|  |  | `Male` |  "en-US-Guy24kRUS" |
| `es-ES` | Spanska (Spanien) | `Female` |  "es-ES-Lisa-Apollo" |
|  |  | `Female` | "es-ES-HelenaRUS" |
|  |  | `Male` | "es-ES-Pablo-Apollo" |
| `es-MX` | Spanska (Mexiko) | `Female` |  "es-MX-HildaRUS" |
|  |  | `Male` | "es-MX-Raul-Apollo" |
| `fi-FI` | Finska | `Female` | "fi-FI-HeidiRUS" |
| `fr-CA` | Franska (Kanada) | `Female` | "fr-CA-Caroline" |
|  |  | `Female` | "fr-CA-HarmonieRUS" |
| `fr-CH` | Franska (Schweiz) | `Male` | "fr-CH-Guillaume" |
| `fr-FR` | Franska (Frankrike) | `Female` |  "fr-FR-Julie-Apollo" |
|  |  | `Female` |"fr-FR-HortenseRUS" |
|  |  | `Male` |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebreiska (Israel) | `Male` |  "he-IL-Asaf" |
| `hi-IN` | Hindi (Indien) | `Female` | "Hi-IN-Kalpana-Apollo" |
|  |  | `Female` |  "Hi-IN-Kalpana" |
|  |  | `Male` |  "Hi-IN-Hemant" |
| `hr-HR` | Kroatiska | `Male` | "HR-HR-Matej" |
| `hu-HU` | Ungerska | `Male` |  "hu-HU-Szabolcs" |
| `id-ID` | Indonesiska | `Male` | "ID-ID-andika" |
| `it-IT` | Italienska | `Male` |  "IT-IT-Cosimo-Apollo" |
|  |  | `Female` |  "IT-LuciaRUS" |
| `ja-JP` | Japanska | `Female` |  "ja-JP-Ayumi-Apollo" |
|  |  | `Male` | "ja-JP-Ichiro-Apollo" |
|  |  | `Female` |  "ja-JP-HarukaRUS" |
| `ko-KR` | Koreanska | `Female` | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajiska | `Male` |  "MS-MY-Rizwan" |
| `nb-NO` | Norska | `Female` |  "NB-NO-HuldaRUS" |
| `nl-NL` | Nederländska | `Female` |  "nl-NL-HannaRUS" |
| `pl-PL` | Polska | `Female` |  "PL-PL-PaulinaRUS" |
| `pt-BR` | Portugisiska (Brasilien) | `Female` | "pt-BR-HeloisaRUS" |
|  |  | `Male` |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugisiska (Portugal) | `Female` | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumänska | `Male` | "RO-RO-tillhör" |
| `ru-RU` | Ryska | `Female` |  "ru-RU-Irina-Apollo" |
|  |  | `Male` | "ru-RU-Pavel-Apollo" |
|  |  | `Female` |  ru-RU-EkaterinaRUS |
| `sk-SK` | Slovakiska | `Male` | "sk-SK-Filip" |
| `sl-SI` | Slovenska | `Male` |  "SL-SI-Lado" |
| `sv-SE` | Svenska | `Female` | "sa-SE-HedvigRUS" |
| `ta-IN` | Tamiliska (Indien) | `Male` |  "ta-IN-Valluvar" |
| `te-IN` | Telugu (Indien) | `Female` |  "te-IN-Chitra" |
| `th-TH` | Thailändska | `Male` |  "th-Pattara" |
| `tr-TR` | Turkiska (Turkiet) | `Female` | "TR-TR-SedaRUS" |
| `vi-VN` | Vietnamesiska | `Male` |  "vi-VN-a" |
| `zh-CN` | Kinesiska (mandariner, förenklad) | `Female` |  "zh-CN-HuihuiRUS" |
|  |  | `Female` | "zh-CN-Yaoyao-Apollo" |
|  |  | `Male` | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Kinesiska (kantonesiska, traditionell) | `Female` |  "zh-HK-Tracy-Apollo" |
|  |  | `Female` | "zh-HK-TracyRUS" |
|  |  | `Male` |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Kinesiska (Taiwan-mandariner) | `Female` |  "zh-TW-Yating-Apollo" |
|  |  | `Female` | "zh-TW-HanHanRUS" |
|  |  | `Male` |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-tex stöder modern standard arabiska (MSA).*

> [!IMPORTANT]
> `en-US-Jessa`Rösten har ändrats till `en-US-Aria` . Om du använde "Jessa" tidigare, konvertera till "Aria".

> [!TIP]
> Du kan fortsätta att använda den fullständiga tjänst namns mappningen, t. ex. "Microsoft Server Speech Text till tal Voice (en-US, AriaRUS)" i din begäran om tal syntes.

### <a name="customization"></a>Anpassning

Röst anpassning är tillgänglig för,,,,,, `de-DE` `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` och `zh-CN` . Välj rätt språk version som matchar de tränings data du behöver för att träna en anpassad röst modell. Om till exempel de inspelnings data du har talas på engelska med en brittisk accent väljer du `en-GB` .

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
| Engelska                 | `en`          |
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
| Koreanska                  | `ko`          |
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
