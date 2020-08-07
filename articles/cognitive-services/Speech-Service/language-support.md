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
ms.openlocfilehash: c01e543b251020581d96d61434d0b3309535ef0c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902053"
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

| Nationell inställning  | Språk                          | Anpassningar                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Arabiska (Förenade Arabemiraten)                      | Nej                                                |
| `ar-BH` | Arabiska (Bahrain), modern standard | Språkmodell                                    |
| `ar-EG` | Arabiska (Egypten)                    | Språkmodell                                    |
| `ar-IL` | Arabiska (Israel)                   | Nej                                                |
| `ar-JO` | Arabiska (Jordanien)                   | Nej                                                |
| `ar-KW` | Arabiska (Kuwait)                   | Nej                                                |
| `ar-LB` | Arabiska (Libanon)                  | Nej                                                |
| `ar-PS` | Arabiska (Palestina)                | Nej                                                |
| `ar-QA` | Arabiska (Qatar)                    | Nej                                                |
| `ar-SA` | Arabiska (Saudiarabien)             | Nej                                                |
| `ar-SY` | Arabiska (Syrien)                    | Språkmodell                                    |
| `ca-ES` | Katalanska                           | Språkmodell                                    |
| `cs-CZ` | Tjeckiska (Tjeckien)            | Språk modell                                    | 
| `da-DK` | Danska (Danmark)                  | Språkmodell                                    |
| `de-DE` | Tyska (Tyskland)                  | Akustisk modell<br>Språkmodell<br>Uttal |
| `en-AU` | Engelska (Australien)               | Akustisk modell<br>Språkmodell                  |
| `en-CA` | Engelska (Kanada)                  | Akustisk modell<br>Språkmodell                  |
| `en-GB` | Engelska (Storbritannien)          | Akustisk modell<br>Språkmodell<br>Uttal |
| `en-HK` | Engelska (Hongkong)               | Språk modell                                    | 
| `en-IE` | Engelska (Irland)                 | Språk modell                                    | 
| `en-IN` | Engelska (Indien)                   | Akustisk modell<br>Språkmodell                  |
| `en-NZ` | Engelska (Nya Zeeland)             | Akustisk modell<br>Språkmodell                  |
| `en-PH` | Engelska (Filippinerna)             | Språk modell                                    | 
| `en-SG` | Engelska (Singapore)               | Språk modell                                    | 
| `en-US` | Engelska (USA)           | Akustisk modell<br>Språkmodell<br>Uttal |
| `en-ZA` | Engelska (Sydafrika)            | Språk modell                                    | 
| `es-AR` | Spanska (Argentina)               | Språk modell                                    | 
| `es-BO` | Spanska (Bolivia)                 | Språk modell                                    | 
| `es-CL` | Spanska (Chile)                   | Språk modell                                    | 
| `es-CO` | Spanska (Colombia)                | Språk modell                                    | 
| `es-CR` | Spanska (Costa Rica)              | Språk modell                                    | 
| `es-CU` | Spanska (Kuba)                    | Språk modell                                    | 
| `es-DO` | Spanska (Dominikanska republiken)      | Språk modell                                    | 
| `es-EC` | Spanska (Ecuador)                 | Språk modell                                    | 
| `es-ES` | Spanska (Spanien)                   | Akustisk modell<br>Språkmodell                  |
| `es-GT` | Spanska (Guatemala)               | Språk modell                                    | 
| `es-HN` | Spanska (Honduras)                | Språk modell                                    | 
| `es-MX` | Spanska (Mexiko)                  | Akustisk modell<br>Språkmodell                  |
| `es-NI` | Spanska (Nicaragua)               | Språk modell                                    | 
| `es-PA` | Spanska (Panama)                  | Språk modell                                    | 
| `es-PE` | Spanska (Peru)                    | Språk modell                                    | 
| `es-PR` | Spanska (Puerto Rico)             | Språk modell                                    | 
| `es-PY` | Spanska (Paraguay)                | Språk modell                                    | 
| `es-SV` | Spanska (El Salvador)             | Språk modell                                    | 
| `es-US` | Spanska (USA)                     | Språk modell                                    | 
| `es-UY` | Spanska (Uruguay)                 | Språk modell                                    | 
| `es-VE` | Spanska (Venezuela)               | Språk modell                                    | 
| `fi-FI` | Finska (Finland)                 | Språkmodell                                    |
| `fr-CA` | Franska (Kanada)                   | Akustisk modell<br>Språkmodell                  |
| `fr-FR` | Franska (Frankrike)                   | Akustisk modell<br>Språkmodell<br>Uttal |
| `gu-IN` | Gujarati (indiska)                 | Språkmodell                                    |
| `hi-IN` | Hindi (Indien)                     | Akustisk modell<br>Språkmodell                  |
| `hu-HU` | Ungerska (Ungern)               | Språk modell                                    | 
| `it-IT` | Italienska (Italien)                   | Akustisk modell<br>Språkmodell<br>Uttal |
| `ja-JP` | Japanska (Japan)                  | Språkmodell                                    |
| `ko-KR` | Koreanska (Korea)                    | Språkmodell                                    |
| `mr-IN` | Marathi (Indien)                   | Språkmodell                                    |
| `nb-NO` | Norska (bokmål) (Norge)       | Språkmodell                                    |
| `nl-NL` | Nederländska (Nederländerna)               | Språkmodell                                    |
| `pl-PL` | Polska (Polen)                   | Språkmodell                                    |
| `pt-BR` | Portugisiska (Brasilien)               | Akustisk modell<br>Språkmodell<br>Uttal |
| `pt-PT` | Portugisiska (Portugal)             | Språkmodell                                    |
| `ru-RU` | Ryska (Ryssland)                  | Akustisk modell<br>Språkmodell                  |
| `sv-SE` | Svenska (Sverige)                  | Språkmodell                                    |
| `ta-IN` | Tamiliska (Indien)                     | Språkmodell                                    |
| `te-IN` | Telugu (Indien)                    | Språkmodell                                    |
| `th-TH` | Thailändska (Thailand)                   | Nej                                                |
| `tr-TR` | Turkiska (Turkiet)                  | Språkmodell                                    |
| `zh-CN` | Kinesiska (mandariner, förenklad)    | Akustisk modell<br>Språkmodell                  |
| `zh-HK` | Kinesiska (kantonesiska, traditionell)  | Språkmodell                                    |
| `zh-TW` | Kinesiska (Taiwan-mandariner)      | Språkmodell                                    |

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
| `ar-EG` | Arabiska (Egypten)                  | Kvinna | `ar-EG-SalmaNeural`      | Allmänt |
| `ar-SA` | Arabiska (Saudiarabien)           | Kvinna | `ar-SA-ZariyahNeural`    | Allmänt |
| `ca-ES` | Katalanska (Spanien)                 | Kvinna | `ca-ES-AlbaNeural`       | Allmänt |
| `da-DK` | Danska (Danmark)                | Kvinna | `da-DK-ChristelNeural`   | Allmänt |
| `de-DE` | Tyska (Tyskland)                | Kvinna | `de-DE-KatjaNeural`      | Allmänt |
| `en-AU` | Engelska (Australien)             | Kvinna | `en-AU-NatashaNeural`    | Allmänt |
| `en-CA` | Engelska (Kanada)                | Kvinna | `en-CA-ClaraNeural`      | Allmänt |
| `en-GB` | Engelska (Storbritannien)                    | Kvinna | `en-GB-LibbyNeural`      | Allmänt |
|         |                                 | Kvinna | `en-GB-MiaNeural`        | Allmänt |
| `en-IN` | Engelska (Indien)                 | Kvinna | `en-IN-NeerjaNeural`     | Allmänt |
| `en-US` | Engelska (USA)                    | Kvinna | `en-US-AriaNeural`       | Allmänt, flera röst format är tillgängliga |
|         |                                 | Man   | `en-US-GuyNeural`        | Allmänt |
| `es-ES` | Spanska (Spanien)                 | Kvinna | `es-ES-ElviraNeural`     | Allmänt |
| `es-MX` | Spanska (Mexiko)                | Kvinna | `es-MX-DaliaNeural`      | Allmänt |
| `fi-FI` | Finska (Finland)               | Kvinna | `fi-FI-NooraNeural`      | Allmänt |
| `fr-CA` | Franska (Kanada)                 | Kvinna | `fr-CA-SylvieNeural`     | Allmänt |
| `fr-FR` | Franska (Frankrike)                 | Kvinna | `fr-FR-DeniseNeural`     | Allmänt |
| `hi-IN` | Hindi (Indien)                   | Kvinna | `hi-IN-SwaraNeural`      | Allmänt |
| `it-IT` | Italienska (Italien)                 | Kvinna | `it-IT-ElsaNeural`       | Allmänt |
| `ja-JP` | Japanska                        | Kvinna | `ja-JP-NanamiNeural`     | Allmänt |
| `ko-KR` | Koreanska                          | Kvinna | `ko-KR-SunHiNeural`      | Allmänt |
| `nb-NO` | Norska                       | Kvinna | `nb-NO-IselinNeural`     | Allmänt |
| `nl-NL` | Nederländska (Netherland)              | Kvinna | `nl-NL-ColetteNeural`    | Allmänt |
| `pl-PL` | Polska (Polen)                 | Kvinna | `pl-PL-ZofiaNeural`      | Allmänt |
| `pt-BR` | Portugisiska (Brasilien)             | Kvinna | `pt-BR-FranciscaNeural`  | Allmänt, flera röst format är tillgängliga |
| `tr-TR` | Turkiska                         | Kvinna | `tr-TR-EmelNeural`       | Allmänt |
| `pt-PT` | Portugisiska (Portugal)           | Kvinna | `pt-PT-FernandaNeural`   | Allmänt |
| `ru-RU` | Ryska (Ryssland)                | Kvinna | `ru-RU-DariyaNeural`     | Allmänt |
| `sv-SE` | Svenska (Sverige)                | Kvinna | `sv-SE-HilleviNeural`    | Allmänt |
| `th-TH` | Thailändska (Thailand)                 | Kvinna | `th-TH-AcharaNeural`     | Allmänt |
| `zh-CN` | Kinesiska (mandariner, förenklad)  | Kvinna | `zh-CN-XiaoxiaoNeural`   | Allmänt, flera röst format är tillgängliga |
|         |                                 | Kvinna | `zh-CN-XiaoyouNeural`    | Röst för barn, optimerad för artikel-berättarröst |
|         |                                 | Man   | `zh-CN-YunyangNeural`    | Optimerad för nyhets läsning, flera röst format är tillgängliga |
|         |                                 | Man   | `zh-CN-YunyeNeural`      | Optimerad för artikel-berättarröst |
| `zh-HK` | Kinesiska (kantonesiska, traditionell)   | Kvinna | `zh-HK-HiuGaaiNeural`| Allmänt |
| `zh-TW` | Kinesiska (Taiwan-mandariner)   | Kvinna | `zh-TW-HsiaoYuNeural`    | Allmänt |

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
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
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

## <a name="speaker-recognition"></a>Talarigenkänning

I följande tabell visas vilka språk som stöds för de olika Talarigenkänning API: erna. Mer information om Talarigenkänning finns i [översikten](speaker-recognition-overview.md) .

| Nationell inställning | Språk | Text beroende verifiering | Text oberoende verifiering | Text oberoende identifiering |
|----|----|----|----|----|
| sv-SE | Engelska (USA) | ja | ja | ja |
|zh-CN  |Kinesiska (mandariner, förenklad)|    saknas|    ja|    ja|
|de-DE  |Tyska (Tyskland)   |saknas    |ja    |ja|
|en-GB  |Engelska (Storbritannien)   |saknas    |ja    |ja|
|fr-FR  |Franska (Frankrike)    |saknas    |ja    |ja|
|en – AU  |Engelska (Australien)    |saknas    |ja    |ja|
|en-CA  |Engelska (Kanada)   |saknas|   ja|    ja|
|fr-CA  |Franska (Kanada)    |saknas    |ja|   ja|
|it-IT  |Italienska|   saknas |ja|   ja|
|es-ES| Spanska (Spanien) |saknas    |ja|   ja|
|ES – MX  |Spanska (Mexiko)   |saknas|   ja|    ja|
|ja-JP| Japanska    |saknas    |ja    |ja|
|pt-BR| Portugisiska (Brasilien)|    saknas|    ja|    ja|

## <a name="next-steps"></a>Nästa steg

* [Hämta utvärderings prenumerationen för din röst tjänst](https://azure.microsoft.com/try/cognitive-services/)
* [Se känna igen tal i C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
