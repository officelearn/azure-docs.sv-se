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
ms.openlocfilehash: 36b653fb3c3a24f3d19408aa2c87b3b764d04ec1
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84730330"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Språk-och röst stöd för tal tjänsten

Språk stöd varierar beroende på funktioner i tal tjänster. Följande tabeller sammanfattar språk stöd för [tal-till-text](#speech-to-text)-, [text-till-tal](#text-to-speech)-och [röst översättnings](#speech-translation) tjänst erbjudanden.

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech SDK och REST API stöder följande språk (lokala). 

För att förbättra noggrannheten erbjuds anpassning för en delmängd av språken genom uppladdning av **ljud + mänskligt avskrifter** eller **relaterad text: meningar**. Mer information om anpassning finns i [Kom igång med Custom Speech](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Nationell inställning  | Språk                          | Stöds | Anpassningar                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabiska (Förenade Arabemiraten)                      | Ja       | Inga                                                |
| `ar-BH` | Arabiska (Bahrain), modern standard | Ja       | Språkmodell                                    |
| `ar-EG` | Arabiska (Egypten)                    | Ja       | Språkmodell                                    |
| `ar-IL` | Arabiska (Israel)                   | Ja       | Inga                                                |
| `ar-JO` | Arabiska (Jordanien)                   | Ja       | Inga                                                |
| `ar-KW` | Arabiska (Kuwait)                   | Ja       | Inga                                                |
| `ar-LB` | Arabiska (Libanon)                  | Ja       | Inga                                                |
| `ar-PS` | Arabiska (Palestina)                | Ja       | Inga                                                |
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
| `te-IN` | Telugu (Indien)                    | Ja       | Språkmodell                                    |
| `th-TH` | Thailändska (Thailand)                   | Ja       | Inga                                                |
| `tr-TR` | Turkiska (Turkiet)                  | Ja       | Språkmodell                                    |
| `zh-CN` | Kinesiska (mandariner, förenklad)    | Ja       | Akustisk modell<br>Språkmodell                  |
| `zh-HK` | Kinesiska (kantonesiska, traditionell)  | Ja       | Språkmodell                                    |
| `zh-TW` | Kinesiska (Taiwan-mandariner)      | Ja       | Språkmodell                                    |

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
| `de-DE` | Tyska (Tyskland)                | Kvinna | "de-KatjaNeural"      | Allmänt |
| `en-AU` | Engelska (Australien)             | Kvinna | "en-AU-NatashaNeural"    | Allmänt |
| `en-CA` | Engelska (Kanada)                | Kvinna | "en-CA-ClaraNeural"      | Allmänt |
| `en-GB` | Engelska (Storbritannien)                    | Kvinna | "en-GB-LibbyNeural"      | Allmänt |
|         |                                 | Kvinna | "en-GB-MiaNeural"        | Allmänt |
| `en-US` | Engelska (USA)                    | Kvinna | "en-US-AriaNeural"       | Allmänt, flera röst format är tillgängliga |
|         |                                 | Man   | "en-US-GuyNeural"        | Allmänt |
| `es-ES` | Spanska (Spanien)                 | Kvinna | "es-ES-ElviraNeural"     | Allmänt |
| `es-MX` | Spanska (Mexiko)                | Kvinna | "es-MX-DaliaNeural"      | Allmänt |
| `fr-CA` | Franska (Kanada)                 | Kvinna | "fr-CA-SylvieNeural"     | Allmänt |
| `fr-FR` | Franska (Frankrike)                 | Kvinna | "fr-FR-DeniseNeural"     | Allmänt |
| `it-IT` | Italienska (Italien)                 | Kvinna | "IT-ElsaNeural"       | Allmänt |
| `ja-JP` | Japanska                        | Kvinna | "ja-JP-NanamiNeural"     | Allmänt |
| `ko-KR` | Koreanska                          | Kvinna | "ko-KR-SunHiNeural"      | Allmänt |
| `nb-NO` | Norska                       | Kvinna | "NB-NO-IselinNeural"     | Allmänt |
| `pt-BR` | Portugisiska (Brasilien)             | Kvinna | "pt-BR-FranciscaNeural"  | Allmänt |
| `tr-TR` | Turkiska                         | Kvinna | "TR-TR-EmelNeural"       | Allmänt |
| `zh-CN` | Kinesiska (mandariner, förenklad)  | Kvinna | "zh-CN-XiaoxiaoNeural"   | Allmänt, flera röst format är tillgängliga |
|         |                                 | Kvinna | "zh-CN-XiaoyouNeural"    | Röst för barn, optimerad för artikel-berättarröst |
|         |                                 | Man   | "zh-CN-YunyangNeural"    | Optimerad för nyhets läsning, flera röst format är tillgängliga |
|         |                                 | Man   | "zh-CN-YunyeNeural"      | Optimerad för artikel-berättarröst |

> [!IMPORTANT]
> `en-US-JessaNeural`Rösten har ändrats till `en-US-AriaNeural` . Om du använde "Jessa" tidigare, konvertera till "Aria".

Information om hur du kan konfigurera och justera neurala-röster finns i [tal syntes märknings språk](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Du kan fortsätta att använda den fullständiga tjänst namns mappningen, t. ex. "Microsoft Server Speech Text till tal Voice (en-US, AriaNeural)" i din begäran om tal syntes.

### <a name="standard-voices"></a>Standard röster

Det finns fler än 75 standard röster i över 45 språk och nationella inställningar, vilket gör att du kan konvertera text till syntetiskt tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

| Nationell inställning | Språk | Kön | Röst namn |
|--|--|--|--|
| <sup>81.1</sup>`ar-EG` | Arabiska (Egypten) | Kvinna | "ar-tex-Hoda" |
| `ar-SA` | Arabiska (Saudiarabien) | Man | "ar-SA-Naayf" |
| `bg-BG` | Bulgariska | Man |  "BG-BG-Ivan" |
| `ca-ES` | Katalanska | Kvinna |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Tjeckiska | Man | "CS-CZ-Jakub" |
| `da-DK` | Danska | Kvinna |  "da-DK-HelleRUS" |
| `de-AT` | Tyska (Österrike) | Man | "avinstallation-Michael" |
| `de-CH` | Tyska (Schweiz) | Man |  "de-CH-Karsten" |
| `de-DE` | Tyska (Tyskland) | Kvinna |  "de-Hedda" |
|  |  | Kvinna | "de-HeddaRUS" |
|  |  | Man |  "avApollo-Stefan-" |
| `el-GR` | Grekiska | Man | "El-GR-Stefanos" |
| `en-AU` | Engelska (Australien) | Kvinna |  "en-AU-Catherine" |
|  |  | Kvinna |  "en-AU-HayleyRUS" |
| `en-CA` | Engelska (Kanada) | Kvinna |  "en-CA-Linda" |
|  |  | Kvinna |  "en-CA-HeatherRUS" |
| `en-GB` | Engelska (Storbritannien) | Kvinna |  "en-GB-Susan-Apollo" |
|  |  | Kvinna |  "en-GB-HazelRUS" |
|  |  | Man |  "en-GB-George-Apollo" |
| `en-IE` | Engelska (Irland) | Man | "en-IE-Stefan" |
| `en-IN` | Engelska (Indien) | Kvinna | "en-IN-Heera-Apollo" |
|  |  | Kvinna |  "en-IN-PriyaRUS" |
|  |  | Man |  "en-IN-Ravi-Apollo" |
| `en-US` | Engelska (USA) | Kvinna |  "en-US-ZiraRUS" |
|  |  | Kvinna | "en-US-AriaRUS" |
|  |  | Man | "en-US-BenjaminRUS" |
|  |  | Man |  "en-US-Guy24kRUS" |
| `es-ES` | Spanska (Spanien) | Kvinna |  "es-ES-Lisa-Apollo" |
|  |  | Kvinna | "es-ES-HelenaRUS" |
|  |  | Man | "es-ES-Pablo-Apollo" |
| `es-MX` | Spanska (Mexiko) | Kvinna |  "es-MX-HildaRUS" |
|  |  | Man | "es-MX-Raul-Apollo" |
| `fi-FI` | Finska | Kvinna | "fi-FI-HeidiRUS" |
| `fr-CA` | Franska (Kanada) | Kvinna | "fr-CA-Caroline" |
|  |  | Kvinna | "fr-CA-HarmonieRUS" |
| `fr-CH` | Franska (Schweiz) | Man | "fr-CH-Guillaume" |
| `fr-FR` | Franska (Frankrike) | Kvinna |  "fr-FR-Julie-Apollo" |
|  |  | Kvinna |"fr-FR-HortenseRUS" |
|  |  | Man |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebreiska (Israel) | Man |  "he-IL-Asaf" |
| `hi-IN` | Hindi (Indien) | Kvinna | "Hi-IN-Kalpana-Apollo" |
|  |  | Kvinna |  "Hi-IN-Kalpana" |
|  |  | Man |  "Hi-IN-Hemant" |
| `hr-HR` | Kroatiska | Man | "HR-HR-Matej" |
| `hu-HU` | Ungerska | Man |  "hu-HU-Szabolcs" |
| `id-ID` | Indonesiska | Man | "ID-ID-andika" |
| `it-IT` | Italienska | Man |  "IT-IT-Cosimo-Apollo" |
|  |  | Kvinna |  "IT-LuciaRUS" |
| `ja-JP` | Japanska | Kvinna |  "ja-JP-Ayumi-Apollo" |
|  |  | Man | "ja-JP-Ichiro-Apollo" |
|  |  | Kvinna |  "ja-JP-HarukaRUS" |
| `ko-KR` | Koreanska | Kvinna | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajiska | Man |  "MS-MY-Rizwan" |
| `nb-NO` | Norska | Kvinna |  "NB-NO-HuldaRUS" |
| `nl-NL` | Nederländska | Kvinna |  "nl-NL-HannaRUS" |
| `pl-PL` | Polska | Kvinna |  "PL-PL-PaulinaRUS" |
| `pt-BR` | Portugisiska (Brasilien) | Kvinna | "pt-BR-HeloisaRUS" |
|  |  | Man |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugisiska (Portugal) | Kvinna | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumänska | Man | "RO-RO-tillhör" |
| `ru-RU` | Ryska | Kvinna |  "ru-RU-Irina-Apollo" |
|  |  | Man | "ru-RU-Pavel-Apollo" |
|  |  | Kvinna |  ru-RU-EkaterinaRUS |
| `sk-SK` | Slovakiska | Man | "sk-SK-Filip" |
| `sl-SI` | Slovenska | Man |  "SL-SI-Lado" |
| `sv-SE` | Svenska | Kvinna | "sa-SE-HedvigRUS" |
| `ta-IN` | Tamiliska (Indien) | Man |  "ta-IN-Valluvar" |
| `te-IN` | Telugu (Indien) | Kvinna |  "te-IN-Chitra" |
| `th-TH` | Thailändska | Man |  "th-Pattara" |
| `tr-TR` | Turkiska (Turkiet) | Kvinna | "TR-TR-SedaRUS" |
| `vi-VN` | Vietnamesiska | Man |  "vi-VN-a" |
| `zh-CN` | Kinesiska (mandariner, förenklad) | Kvinna |  "zh-CN-HuihuiRUS" |
|  |  | Kvinna | "zh-CN-Yaoyao-Apollo" |
|  |  | Man | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Kinesiska (kantonesiska, traditionell) | Kvinna |  "zh-HK-Tracy-Apollo" |
|  |  | Kvinna | "zh-HK-TracyRUS" |
|  |  | Man |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Kinesiska (Taiwan-mandariner) | Kvinna |  "zh-TW-Yating-Apollo" |
|  |  | Kvinna | "zh-TW-HanHanRUS" |
|  |  | Man |  "zh-TW-Zhiwei-Apollo" |

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
