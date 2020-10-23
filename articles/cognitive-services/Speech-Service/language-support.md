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
ms.openlocfilehash: 6f484277ee940c63888fff068739598dd553b515
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440901"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Språk-och röst stöd för tal tjänsten

Språk stöd varierar beroende på funktioner i tal tjänster. Följande tabeller sammanfattar språk stöd för [tal-till-text](#speech-to-text)-, [text-till-tal](#text-to-speech)-och [röst översättnings](#speech-translation) tjänst erbjudanden.

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech SDK och REST API stöder följande språk (lokala). 

För att förbättra noggrannheten erbjuds anpassning för en delmängd av språken genom uppladdning av **ljud + mänskligt avskrifter** eller **relaterad text: meningar**. Mer information om anpassning finns i [Kom igång med Custom Speech](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Nationell inställning  | Språk                          | Anpassningar                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Arabiska (Förenade Arabemiraten)     | Språkmodell                                    |
| `ar-BH` | Arabiska (Bahrain), modern standard | Språkmodell                                    |
| `ar-EG` | Arabiska (Egypten)                    | Språkmodell                                    |
| `ar-IQ` | Arabiska (Irak)                     | Språkmodell                                    |
| `ar-JO` | Arabiska (Jordanien)                   | Språkmodell                                    |
| `ar-KW` | Arabiska (Kuwait)                   | Språkmodell                                    |
| `ar-LB` | Arabiska (Libanon)                  | Språkmodell                                    |
| `ar-OM` | Arabiska (Oman)                     | Språkmodell                                    |
| `ar-QA` | Arabiska (Qatar)                    | Språkmodell                                    |
| `ar-SA` | Arabiska (Saudiarabien)             | Språkmodell                                    |
| `ar-SY` | Arabiska (Syrien)                    | Språkmodell                                    |
| `bg-BG` | Bulgariska (Bulgarien)              | Språkmodell                                    |
| `ca-ES` | Katalanska (Spanien)                   | Språkmodell                                    |
| `cs-CZ` | Tjeckiska (Tjeckien)            | Språk modell                                    | 
| `da-DK` | Danska (Danmark)                  | Språkmodell                                    |
| `de-DE` | Tyska (Tyskland)                  | Akustisk modell<br>Språkmodell<br>Uttal |
| `el-GR` | Grekiska (Grekland)                    | Språkmodell                                    |
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
| `et-EE` | Estniska (Estland)                 | Språk modell                                    | 
| `fi-FI` | Finska (Finland)                 | Språkmodell                                    |
| `fr-CA` | Franska (Kanada)                   | Akustisk modell<br>Språkmodell                  |
| `fr-FR` | Franska (Frankrike)                   | Akustisk modell<br>Språkmodell<br>Uttal |
| `ga-IE` | Irländskt (Irland)                    | Språkmodell                                    |
| `gu-IN` | Gujarati (indiska)                 | Språkmodell                                    |
| `hi-IN` | Hindi (Indien)                     | Akustisk modell<br>Språkmodell                  |
| `hr-HR` | Kroatiska (Kroatien)                | Språkmodell                                    |
| `hu-HU` | Ungerska (Ungern)               | Språk modell                                    | 
| `it-IT` | Italienska (Italien)                   | Akustisk modell<br>Språkmodell<br>Uttal |
| `ja-JP` | Japanska (Japan)                  | Språkmodell                                    |
| `ko-KR` | Koreanska (Korea)                    | Språkmodell                                    |
| `lt-LT` | Litauiska (Litauen)            | Språkmodell                                    |
| `lv-LV` | Lettiska (Lettland)                  | Språkmodell                                    |
| `mr-IN` | Marathi (Indien)                   | Språkmodell                                    |
| `mt-MT` | Maltesiska (Malta)                    | Språkmodell                                    |
| `nb-NO` | Norska (bokmål) (Norge)       | Språkmodell                                    |
| `nl-NL` | Nederländska (Nederländerna)               | Språkmodell                                    |
| `pl-PL` | Polska (Polen)                   | Språkmodell                                    |
| `pt-BR` | Portugisiska (Brasilien)               | Akustisk modell<br>Språkmodell<br>Uttal |
| `pt-PT` | Portugisiska (Portugal)             | Språkmodell                                    |
| `ro-RO` | Rumänska (Rumänien)                | Språkmodell                                    |
| `ru-RU` | Ryska (Ryssland)                  | Akustisk modell<br>Språkmodell                  |
| `sk-SK` | Slovakiska (Slovakien)                 | Språkmodell                                    |
| `sl-SI` | Slovenska (Slovenien)              | Språkmodell                                    |
| `sv-SE` | Svenska (Sverige)                  | Språkmodell                                    |
| `ta-IN` | Tamiliska (Indien)                     | Språkmodell                                    |
| `te-IN` | Telugu (Indien)                    | Språkmodell                                    |
| `th-TH` | Thailändska (Thailand)                   | Språkmodell                                    |
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
| `ar-EG` | Arabiska (Egypten) | Kvinna | `ar-EG-SalmaNeural` | Allmänt |
| `ar-SA` | Arabiska (Saudiarabien) | Kvinna | `ar-SA-ZariyahNeural` | Allmänt |
| `bg-BG` <sup>Nytt</sup> | Bulgariska (Bulgary) | Kvinna | `bg-BG-KalinaNeural` | Allmänt |
| `ca-ES` | Katalanska (Spanien) | Kvinna | `ca-ES-AlbaNeural` | Allmänt |
| `cs-CZ` <sup>Nytt</sup> | Tjeckiska (Tjeckien) | Kvinna | `cs-CZ-VlastaNeural` | Allmänt |
| `da-DK` | Danska (Danmark) | Kvinna | `da-DK-ChristelNeural` | Allmänt |
| `de-AT` <sup>Nytt</sup> | Tyska (Österrike) | Kvinna | `de-AT-IngridNeural` | Allmänt |
| `de-CH` <sup>Nytt</sup> | Tyska (Schweiz) | Kvinna | `de-CH-LeniNeural` | Allmänt |
| `de-DE` | Tyska (Tyskland) | Kvinna | `de-DE-KatjaNeural` | Allmänt |
| `de-DE` <sup>Nytt</sup> | Tyska (Tyskland) | Man | `de-DE-ConradNeural` | Allmänt |
| `el-GR` <sup>Nytt</sup> | Grekiska (Grekland) | Kvinna | `el-GR-AthinaNeural` | Allmänt |
| `en-AU` | Engelska (Australien) | Kvinna | `en-AU-NatashaNeural` | Allmänt |
| `en-AU` <sup>Nytt</sup> | Engelska (Australien) | Man | `en-AU-WilliamNeural` | Allmänt |
| `en-CA` | Engelska (Kanada) | Kvinna | `en-CA-ClaraNeural` | Allmänt |
| `en-GB` | Engelska (Storbritannien) | Kvinna | `en-GB-LibbyNeural` | Allmänt |
| `en-GB` | Engelska (Storbritannien) | Kvinna | `en-GB-MiaNeural` | Allmänt |
| `en-GB` <sup>Nytt</sup> | Engelska (Storbritannien) | Man | `en-GB-RyanNeural` | Allmänt |
| `en-IE` <sup>Nytt</sup> | Engelska (Irland) | Kvinna | `en-IE-EmilyNeural` | Allmänt |
| `en-IN` | Engelska (Indien) | Kvinna | `en-IN-NeerjaNeural` | Allmänt |
| `en-US` | Engelska (USA) | Kvinna | `en-US-AriaNeural` | Allmänt, flera röst format är tillgängliga |
| `en-US` | Engelska (USA) | Man | `en-US-GuyNeural` | Allmänt |
| `en-US` <sup>Nytt</sup> | Engelska (USA) | Kvinna | `en-US-JennyNeural` | Allmänt, flera röst format är tillgängliga |
| `es-ES` | Spanska (Spanien) | Kvinna | `es-ES-ElviraNeural` | Allmänt |
| `es-ES` <sup>Nytt</sup> | Spanska (Spanien) | Man | `es-ES-AlvaroNeural` | Allmänt |
| `es-MX` | Spanska (Mexiko) | Kvinna | `es-MX-DaliaNeural` | Allmänt |
| `es-MX` <sup>Nytt</sup> | Spanska (Mexiko) | Man | `es-MX-JorgeNeural` | Allmänt |
| `fi-FI` | Finska (Finland) | Kvinna | `fi-FI-NooraNeural` | Allmänt |
| `fr-CA` | Franska (Kanada) | Kvinna | `fr-CA-SylvieNeural` | Allmänt |
| `fr-CA` <sup>Nytt</sup> | Franska (Kanada) | Man | `fr-CA-JeanNeural` | Allmänt |
| `fr-CH` <sup>Nytt</sup> | Franska (Schweiz) | Kvinna | `fr-CH-ArianeNeural` | Allmänt |
| `fr-FR` | Franska (Frankrike) | Kvinna | `fr-FR-DeniseNeural` | Allmänt |
| `fr-FR` <sup>Nytt</sup> | Franska (Frankrike) | Man | `fr-FR-HenriNeural` | Allmänt |
| `he-IL` <sup>Nytt</sup> | Hebreiska (Israel) | Kvinna | `he-IL-HilaNeural` | Allmänt |
| `hi-IN` | Hindi (Indien) | Kvinna | `hi-IN-SwaraNeural` | Allmänt |
| `hr-HR` <sup>Nytt</sup> | Kroatiska (Kroatien) | Kvinna | `hr-HR-GabrijelaNeural` | Allmänt |
| `hu-HU` <sup>Nytt</sup> | Ungerska (Ungern) | Kvinna | `hu-HU-NoemiNeural` | Allmänt |
| `id-ID` <sup>Nytt</sup> | Indonesiska (Indonesien) | Man | `id-ID-ArdiNeural` | Allmänt |
| `it-IT` | Italienska (Italien) | Kvinna | `it-IT-ElsaNeural` | Allmänt |
| `it-IT` <sup>Nytt</sup> | Italienska (Italien) | Kvinna | `it-IT-IsabellaNeural` | Allmänt |
| `it-IT` <sup>Nytt</sup> | Italienska (Italien) | Man | `it-IT-DiegoNeural` | Allmänt |
| `ja-JP` | Japanska (Japan) | Kvinna | `ja-JP-NanamiNeural` | Allmänt |
| `ja-JP` <sup>Nytt</sup> | Japanska (Japan) | Man | `ja-JP-KeitaNeural` | Allmänt |
| `ko-KR` | Koreanska (Korea) | Kvinna | `ko-KR-SunHiNeural` | Allmänt |
| `ko-KR` <sup>Nytt</sup> | Koreanska (Korea) | Man | `ko-KR-InJoonNeural` | Allmänt |
| `ms-MY` <sup>Nytt</sup> | Malajiska (Malaysia) | Kvinna | `ms-MY-YasminNeural` | Allmänt |
| `nb-NO` | Norska, bokmål (Norge) | Kvinna | `nb-NO-IselinNeural` | Allmänt |
| `nl-NL` | Nederländska (Nederländerna) | Kvinna | `nl-NL-ColetteNeural` | Allmänt |
| `pl-PL` | Polska (Polen) | Kvinna | `pl-PL-ZofiaNeural` | Allmänt |
| `pt-BR` | Portugisiska (Brasilien) | Kvinna | `pt-BR-FranciscaNeural` | Allmänt, flera röst format är tillgängliga |
| `pt-BR` <sup>Nytt</sup> | Portugisiska (Brasilien) | Man | `pt-BR-AntonioNeural` | Allmänt |
| `pt-PT` | Portugisiska (Portugal) | Kvinna | `pt-PT-FernandaNeural` | Allmänt |
| `ro-RO` <sup>Nytt</sup> | Rumänska (Rumänien) | Kvinna | `ro-RO-AlinaNeural` | Allmänt |
| `ru-RU` | Ryska (Ryssland) | Kvinna | `ru-RU-DariyaNeural` | Allmänt |
| `sk-SK` <sup>Nytt</sup> | Slovakiska (Slovakien) | Kvinna | `sk-SK-ViktoriaNeural` | Allmänt |
| `sl-SI` <sup>Nytt</sup> | Slovenska (Slovenien) | Kvinna | `sl-SI-PetraNeural` | Allmänt |
| `sv-SE` | Svenska (Sverige) | Kvinna | `sv-SE-HilleviNeural` | Allmänt |
| `ta-IN` <sup>Nytt</sup> | Tamiliska (Indien) | Kvinna | `ta-IN-PallaviNeural` | Allmänt |
| `te-IN` <sup>Nytt</sup> | Telugu (Indien) | Kvinna | `te-IN-ShrutiNeural` | Allmänt |
| `th-TH` | Thailändska (Thailand) | Kvinna | `th-TH-AcharaNeural` | Allmänt |
| `th-TH` <sup>Nytt</sup> | Thailändska (Thailand) | Kvinna | `th-TH-PremwadeeNeural` | Allmänt |
| `tr-TR` | Turkiska (Turkiet) | Kvinna | `tr-TR-EmelNeural` | Allmänt |
| `vi-VN` <sup>Nytt</sup> | Vietnamesiska (Vietnam) | Kvinna | `vi-VN-HoaiMyNeural` | Allmänt |
| `zh-CN` | Mandariner (förenklad kinesiska, Kina) | Kvinna | `zh-CN-XiaoxiaoNeural` | Allmänt, flera röst format är tillgängliga |
| `zh-CN` | Mandariner (förenklad kinesiska, Kina) | Kvinna | `zh-CN-XiaoyouNeural` | Röst för barn, optimerad för artikel-berättarröst |
| `zh-CN` | Mandariner (förenklad kinesiska, Kina) | Man | `zh-CN-YunyangNeural` | Optimerad för nyhets läsning, flera röst format är tillgängliga |
| `zh-CN` | Mandariner (förenklad kinesiska, Kina) | Man | `zh-CN-YunyeNeural` | Optimerad för artikel-berättarröst |
| `zh-HK` | Kantonesiska (traditionell kinesiska, Hongkong) | Kvinna | `zh-HK-HiuGaaiNeural` | Allmänt |
| `zh-TW` | Mandariner (traditionell kinesiska, Taiwan) | Kvinna | `zh-TW-HsiaoYuNeural` | Allmänt |

> [!IMPORTANT]
> `en-US-JessaNeural`Rösten har ändrats till `en-US-AriaNeural` . Om du använde "Jessa" tidigare, konvertera till "Aria".

Information om hur du kan konfigurera och justera neurala-röster finns i [tal syntes märknings språk](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Du kan fortsätta att använda den fullständiga tjänst namns mappningen, t. ex. "Microsoft Server Speech Text till tal Voice (en-US, AriaNeural)" i din begäran om tal syntes.

### <a name="standard-voices"></a>Standard röster

Det finns fler än 75 standard röster i över 45 språk och nationella inställningar, vilket gör att du kan konvertera text till syntetiskt tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

| Nationell inställning | Språk | Kön | Röst namn |
|--|--|--|--|
| `ar-EG` | Arabiska (arabiska) | Kvinna | `ar-EG-Hoda` |
| `ar-SA` | Arabiska (Saudiarabien) | Man | `ar-SA-Naayf` |
| `bg-BG` | Bulgariska (Bulgarien) | Man | `bg-BG-Ivan` |
| `ca-ES` | Katalanska (Spanien) | Kvinna | `ca-ES-HerenaRUS` |
| `cs-CZ` | Tjeckiska (Tjeckien) | Man | `cs-CZ-Jakub` |
| `da-DK` | Danska (Danmark) | Kvinna | `da-DK-HelleRUS` |
| `de-AT` | Tyska (Österrike) | Man | `de-AT-Michael` |
| `de-CH` | Tyska (Schweiz) | Man | `de-CH-Karsten` |
| `de-DE` | Tyska (Tyskland) | Kvinna | `de-DE-HeddaRUS` |
| `de-DE` | Tyska (Tyskland) | Man | `de-DE-Stefan` |
| `el-GR` | Grekiska (Grekland) | Man | `el-GR-Stefanos` |
| `en-AU` | Engelska (Australien) | Kvinna | `en-AU-Catherine` |
| `en-AU` | Engelska (Australien) | Kvinna | `en-AU-HayleyRUS` |
| `en-CA` | Engelska (Kanada) | Kvinna | `en-CA-HeatherRUS` |
| `en-CA` | Engelska (Kanada) | Kvinna | `en-CA-Linda` |
| `en-GB` | Engelska (Storbritannien) | Man | `en-GB-George` |
| `en-GB` | Engelska (Storbritannien) | Kvinna | `en-GB-HazelRUS` |
| `en-GB` | Engelska (Storbritannien) | Kvinna | `en-GB-Susan` |
| `en-IE` | Engelska (Irland) | Man | `en-IE-Sean` |
| `en-IN` | Engelska (Indien) | Kvinna | `en-IN-Heera` |
| `en-IN` | Engelska (Indien) | Kvinna | `en-IN-PriyaRUS` |
| `en-IN` | Engelska (Indien) | Man | `en-IN-Ravi` |
| `en-US` | Engelska (USA) | Man | `en-US-BenjaminRUS` |
| `en-US` | Engelska (USA) | Man | `en-US-GuyRUS` |
| `en-US` | Engelska (USA) | Kvinna | `en-US-JessaRUS` |
| `en-US` | Engelska (USA) | Kvinna | `en-US-ZiraRUS` |
| `es-ES` | Spanska (Spanien) | Kvinna | `es-ES-HelenaRUS` |
| `es-ES` | Spanska (Spanien) | Kvinna | `es-ES-Laura` |
| `es-ES` | Spanska (Spanien) | Man | `es-ES-Pablo` |
| `es-MX` | Spanska (Mexiko) | Kvinna | `es-MX-HildaRUS` |
| `es-MX` | Spanska (Mexiko) | Man | `es-MX-Raul` |
| `fi-FI` | Finska (Finland) | Kvinna | `fi-FI-HeidiRUS` |
| `fr-CA` | Franska (Kanada) | Kvinna | `fr-CA-Caroline` |
| `fr-CA` | Franska (Kanada) | Kvinna | `fr-CA-HarmonieRUS` |
| `fr-CH` | Franska (Schweiz) | Man | `fr-CH-Guillaume` |
| `fr-FR` | Franska (Frankrike) | Kvinna | `fr-FR-HortenseRUS` |
| `fr-FR` | Franska (Frankrike) | Kvinna | `fr-FR-Julie` |
| `fr-FR` | Franska (Frankrike) | Man | `fr-FR-Paul` |
| `he-IL` | Hebreiska (Israel) | Man | `he-IL-Asaf` |
| `hi-IN` | Hindi (Indien) | Man | `hi-IN-Hemant` |
| `hi-IN` | Hindi (Indien) | Kvinna | `hi-IN-Kalpana` |
| `hr-HR` | Kroatiska (Kroatien) | Man | `hr-HR-Matej` |
| `hu-HU` | Ungerska (Ungern) | Man | `hu-HU-Szabolcs` |
| `id-ID` | Indonesiska (Indonesien) | Man | `id-ID-Andika` |
| `it-IT` | Italienska (Italien) | Man | `it-IT-Cosimo` |
| `it-IT` | Italienska (Italien) | Kvinna | `it-IT-LuciaRUS` |
| `ja-JP` | Japanska (Japan) | Kvinna | `ja-JP-Ayumi` |
| `ja-JP` | Japanska (Japan) | Kvinna | `ja-JP-HarukaRUS` |
| `ja-JP` | Japanska (Japan) | Man | `ja-JP-Ichiro` |
| `ko-KR` | Koreanska (Korea) | Kvinna | `ko-KR-HeamiRUS` |
| `ms-MY` | Malajiska (Malaysia) | Man | `ms-MY-Rizwan` |
| `nb-NO` | Norska, bokmål (Norge) | Kvinna | `nb-NO-HuldaRUS` |
| `nl-NL` | Nederländska (Nederländerna) | Kvinna | `nl-NL-HannaRUS` |
| `pl-PL` | Polska (Polen) | Kvinna | `pl-PL-PaulinaRUS` |
| `pt-BR` | Portugisiska (Brasilien) | Man | `pt-BR-Daniel` |
| `pt-BR` | Portugisiska (Brasilien) | Kvinna | `pt-BR-HeloisaRUS` |
| `pt-PT` | Portugisiska (Portugal) | Kvinna | `pt-PT-HeliaRUS` |
| `ro-RO` | Rumänska (Rumänien) | Man | `ro-RO-Andrei` |
| `ru-RU` | Ryska (Ryssland) | Kvinna | `ru-RU-EkaterinaRUS` |
| `ru-RU` | Ryska (Ryssland) | Kvinna | `ru-RU-Irina` |
| `ru-RU` | Ryska (Ryssland) | Man | `ru-RU-Pavel` |
| `sk-SK` | Slovakiska (Slovakien) | Man | `sk-SK-Filip` |
| `sl-SI` | Slovenska (Slovenien) | Man | `sl-SI-Lado` |
| `sv-SE` | Svenska (Sverige) | Kvinna | `sv-SE-HedvigRUS` |
| `ta-IN` | Tamiliska (Indien) | Man | `ta-IN-Valluvar` |
| `te-IN` | Telugu (Indien) | Kvinna | `te-IN-Chitra` |
| `th-TH` | Thailändska (Thailand) | Man | `th-TH-Pattara` |
| `tr-TR` | Turkiska (Turkiet) | Kvinna | `tr-TR-SedaRUS` |
| `vi-VN` | Vietnamesiska (Vietnam) | Man | `vi-VN-An` |
| `zh-CN` | Mandariner (förenklad kinesiska, Kina) | Kvinna | `zh-CN-HuihuiRUS` |
| `zh-CN` | Mandariner (förenklad kinesiska, Kina) | Man | `zh-CN-Kangkang` |
| `zh-CN` | Mandariner (förenklad kinesiska, Kina) | Kvinna | `zh-CN-Yaoyao` |
| `zh-HK` | Kantonesiska (traditionell kinesiska, Hongkong) | Man | `zh-HK-Danny` |
| `zh-HK` | Kantonesiska (traditionell kinesiska, Hongkong) | Kvinna | `zh-HK-TracyRUS` |
| `zh-TW` | Mandariner (traditionell kinesiska, Taiwan) | Kvinna | `zh-TW-HanHanRUS` |
| `zh-TW` | Mandariner (traditionell kinesiska, Taiwan) | Kvinna | `zh-TW-Yating` |
| `zh-TW` | Mandariner (traditionell kinesiska, Taiwan) | Man | `zh-TW-Zhiwei` |

> [!IMPORTANT]
> `en-US-Jessa`Rösten har ändrats till `en-US-Aria` . Om du använde "Jessa" tidigare, konvertera till "Aria".

> [!TIP]
> Du kan fortsätta att använda den fullständiga tjänst namns mappningen, t. ex. "Microsoft Server Speech Text till tal Voice (en-US, AriaRUS)" i din begäran om tal syntes.

### <a name="customization"></a>Anpassning

Röst anpassning är tillgänglig för,,,,,, `de-DE` `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` och `zh-CN` . Välj rätt språk version som matchar de tränings data du behöver för att träna en anpassad röst modell. Om till exempel de inspelnings data du har talas på engelska med en brittisk accent väljer du `en-GB` .

> [!NOTE]
> Vi stöder inte dubbelriktad modell utbildning i anpassad röst, förutom för Chinese-English bi-språk. Välj "kinesiskt kinesiskt tvåspråkig" om du vill träna en kinesisk röst som även kan tala med engelska. Röstträning i alla språk börjar med en data uppsättning av 2000 + yttranden, med undantag för `en-US` och `zh-CN` där du kan börja med valfri storlek på tränings data.

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
|zh-CN    |Kinesiska (mandariner, förenklad)|    Saknas|    ja|    ja|
|de-DE    |Tyska (Tyskland)    |Saknas    |ja    |ja|
|en-GB    |Engelska (Storbritannien)    |Saknas    |ja    |ja|
|fr-FR    |Franska (Frankrike)    |Saknas    |ja    |ja|
|en – AU    |Engelska (Australien)    |Saknas    |ja    |ja|
|en-CA    |Engelska (Kanada)    |Saknas|    ja|    ja|
|fr-CA    |Franska (Kanada)    |Saknas    |ja|    ja|
|it-IT    |Italienska|    Saknas    |ja|    ja|
|es-ES|    Spanska (Spanien)    |Saknas    |ja|    ja|
|ES – MX    |Spanska (Mexiko)    |Saknas|    ja|    ja|
|ja-JP|    Japanska    |Saknas    |ja    |ja|
|pt-BR|    Portugisiska (Brasilien)|    Saknas|    ja|    ja|

## <a name="next-steps"></a>Nästa steg

* [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/)
* [Se känna igen tal i C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
