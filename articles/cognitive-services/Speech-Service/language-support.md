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
ms.date: 11/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 26cab7ba3ed864382ae5511755fee09c3826702c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580183"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Språk-och region stöd för tal tjänsterna

Olika språk stöds för olika funktioner i tal tjänster. Följande tabeller sammanfattar språk stöd.

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech SDK och REST API stöder följande språk (lokala). För att förbättra noggrannheten erbjuds anpassning för en delmängd av språken genom uppladdning av ljud + mänskligt avskrifter eller relaterad text: meningar.  Uttal-anpassning är för närvarande bara tillgänglig för en-US och de-DE. Läs mer om anpassning [här](how-to-custom-speech.md).

  Nationell inställning | Språk | Stöds | Anpassningsbar | Containerstöd
 ------|------------|-----------|--------------|--------------
 ar-tex | Arabiska (Egypten), modern standard | ✔️ | ✔️ | ✔️
 ar-SA | Arabiska (Saudiarabien) | ✔️ | ✔️ | ❌
 ar-AE | Arabiska (Förenade Arabemiraten) | ✔️ | ✔️ | ❌
 ar-KW | Arabiska (Kuwait) | ✔️ | ✔️ | ❌
 ar-frågor och svar | Arabiska (Qatar) | ✔️ | ✔️ | ❌
 ca-ES | Katalanska | ✔️ | ❌ | ✔️
 da-DK | Danska (Danmark) | ✔️ | ❌ | ✔️
 de-DE | Tyska (Tyskland) | ✔️ | ✔️ | ✔️
 en – AU | Engelska (Australien) | ✔️ | ✔️ | ✔️
 en-CA | Engelska (Kanada) | ✔️ | ✔️ | ✔️
 en-GB | Engelska (Storbritannien) | ✔️ | ✔️ | ✔️
 en-IN | English (India) | ✔️ | ✔️ | ✔️
 en-NZ | Engelska (Nya Zeeland) | ✔️ | ✔️ | ✔️
 en-US | Engelska (USA) | ✔️ | ✔️ | ✔️
 es-ES | Spanska (Spanien) | ✔️ | ✔️ | ✔️
 ES – MX | Spanska (Mexiko) | ✔️ | ✔️ | ✔️
 fi-FI | Finska (Finland) | ✔️ | ❌ | ✔️
 fr-CA | Franska (Kanada) | ✔️ | ✔️ | ✔️
 fr-FR | Franska (Frankrike) | ✔️ | ✔️ | ✔️
 Gu – IN | Gujarati (indiska) | ✔️ | ✔️ | ❌
 Hi-IN | Hindi (Indien) | ✔️ | ✔️ | ✔️
 det – IT | Italienska (Italien) | ✔️ | ✔️ | ✔️
 ja-JP | Japanska (Japan) | ✔️ | ✔️ | ✔️
 ko-KR | Koreanska (Korea) | ✔️ | ✔️ | ✔️
 Mr-IN | Marathi (Indien) | ✔️ | ✔️ | ❌
 NB-nej | Norska (bokmål) (Norge) | ✔️ | ❌ | ✔️
 nl-NL | Nederländska (Nederländerna) | ✔️ | ✔️ | ✔️
 PL-PL | Polska (Polen) | ✔️ | ❌ | ✔️
 pt-BR | Portugisiska (Brasilien) | ✔️ | ✔️ | ✔️
 pt-PT | Portugisiska (Portugal) | ✔️ | ✔️ | ✔️
 ru-RU | Ryska (Ryssland) | ✔️ | ✔️ | ✔️
 sa-SE | Svenska (Sverige) | ✔️ | ❌ | ✔️
 ta med | Tamiliska (Indien) | ✔️ | ✔️ | ❌
 te-IN | Telugu (Indien) | ✔️ | ✔️ | ❌
 zh-CN | Kinesiska (mandariner, förenklad) | ✔️ | ✔️ | ✔️
 zh-HK | Kinesiska (kantonesiska, traditionell) | ✔️ | ✔️ | ✔️
 zh-TW | Kinesiska (Taiwan-mandariner) | ✔️ | ✔️ | ✔️
 Th-TH | Thailändska (Thailand) | ✔️ | ❌ | ✔️
 TR-TR | Turkiet | ✔️ | ✔️ | ❌


## <a name="text-to-speech"></a>Text till tal

Både Microsoft Speech SDK och REST API stöder dessa röster, som var och en har stöd för ett särskilt språk och dialekt, som identifieras av nationella inställningar.

> [!IMPORTANT]
> Prissättningen varierar för standard-, anpassade och neurala röster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Neurala-röster

Neurala text till tal är en ny typ av tal syntes som drivs av djup neurala nätverk. När du använder en neurala-röst är syntetiskt tal nästan lätt att skilja från de mänskliga inspelningarna.

Neurala röster kan användas för att göra interaktioner med chattrobotar och röst assistenter mer naturligt och engagerande, konvertera digitala texter som e-böcker till Audiobooks och förbättra navigerings systemen i bilen. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när användarna interagerar med AI-system.

En fullständig lista över neurala-röster och regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

Nationell inställning | Språk | Kön | Fullständig tjänst namns mappning | Kort röst namn
--------|----------|--------|---------|------------
de-DE | Tyska (Tyskland) | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, KatjaNeural)" | "de-KatjaNeural"
en-US | English (US) | Man | "Microsoft Server Speech Text till tal Voice (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | English (US) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, JessaNeural)" | "en-US-JessaNeural"
det – IT | Italienska (Italien) | Kvinna |"Microsoft Server Speech Text till tal Voice (IT-IT, ElsaNeural)" | "IT-ElsaNeural"
zh-CN | Kinesiska (fast landet) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Du kan antingen använda den fullständiga tjänst namns mappningen eller det korta röst namnet i ditt tal syntes begär Anden.

### <a name="standard-voices"></a>Standard röster

Det finns fler än 75 standard röster i över 45 språk och nationella inställningar, vilket gör att du kan konvertera text till syntetiskt tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

Nationell inställning | Språk | Kön | Fullständig tjänst namns mappning | Kort namn | Stöd för containrar
-------|----------|---------|----------|----------|------
ar-tex\* | Arabiska (Egypten) | Kvinna | "Microsoft Server Speech Text till tal Voice (ar-tex, Hoda)" | "ar-tex-Hoda" | ✔️
ar-SA | Arabiska (Saudiarabien) | Man | "Microsoft Server Speech Text till tal Voice (ar-SA, Naayf)" | "ar-SA-Naayf" | ✔️
BG-BG | Bulgariska | Man | "Microsoft Server Speech Text till tal Voice (BG-BG, Ivan)" | "BG-BG-Ivan" | ✔️
ca-ES | Katalanska (Spanien) | Kvinna | "Microsoft Server Speech Text till tal Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" | ✔️
CS-CZ | Tjeckiska | Man | "Microsoft Server Speech Text till tal Voice (CS-CZ, Jakub)" | "CS-CZ-Jakub" | ✔️
da-DK | Danska | Kvinna | "Microsoft Server Speech Text till tal Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" | ✔️
avinstallation | Tyska (Österrike) | Man | "Microsoft Server Speech Text till tal Voice (avinstallations, Michael)" | "avinstallation-Michael" | ✔️
de-CH | Tyska (Schweiz) | Man | "Microsoft Server Speech Text till tal Voice (de-CH, Karsten)" | "de-CH-Karsten" | ✔️
de-DE | Tyska (Tyskland) | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, Hedda)" | "de-Hedda" | ❌
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (de-DE, HeddaRUS)" | "de-HeddaRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (de-DE, Stefan, Apollo)" | "avApollo-Stefan-" | ✔️
El – GR | Grekiska | Man | "Microsoft Server Speech Text till tal Voice (El-GR, Stefanos)" | "El-GR-Stefanos" | ✔️
en – AU | Engelska (Australien) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-AU, Catherine)" | "en-AU-Catherine" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" | ✔️
en-CA | Engelska (Kanada) | Kvinna | "Microsoft Server Speech Text till tal Voice (sv-CA, Linda)" | "en-CA-Linda" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" | ✔️
en-GB | English (UK) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo" | ✔️
en-IE | Engelska (Irland) | Man | "Microsoft Server Speech Text till tal Voice (en-IE, Stefan)" | "en-IE-Stefan" | ✔️
en-IN | English (India) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" | ✔️
en-US | English (US) | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, JessaRUS)" | "en-US-JessaRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" | ✔️
es-ES | Spanska (Spanien) |Kvinna | "Microsoft Server Speech Text till tal Voice (es-ES, Lisa, Apollo)" | "es-ES-Lisa-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" | ✔️
ES – MX | Spanska (Mexiko) | Kvinna | "Microsoft Server Speech Text till tal Voice (ES-MX, HildaRUS)" | "es-MX-HildaRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (ES-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" | ✔️
fi-FI | Finska | Kvinna | "Microsoft Server Speech Text till tal Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" | ✔️
fr-CA | Franska (Kanada) |Kvinna | "Microsoft Server Speech Text till tal Voice (fr-CA, Caroline)" | "fr-CA-Caroline" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" | ✔️
Frankrike-CH | Franska (Schweiz)| Man | "Microsoft Server Speech Text till tal Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume" | ✔️
fr-FR | Franska (Frankrike)| Kvinna | "Microsoft Server Speech Text till tal Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (fr-FR, Johan, Apollo)" | "fr-FR-Paul-Apollo" | ✔️
He-IL| Hebreiska (Israel) | Man| "Microsoft Server Speech Text till tal Voice (HE-IL, Asaf)" | "he-IL-Asaf" | ✔️
Hi-IN | Hindi (Indien) | Kvinna | "Microsoft Server Speech Text till tal Voice (Hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo" | ✔️
| | |Kvinna | "Microsoft Server Speech Text till tal Voice (Hi-IN, Kalpana)" | "Hi-IN-Kalpana" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (Hi-IN, Hemant)" | "Hi-IN-Hemant" | ✔️
HR – HR | Kroatiska | Man | "Microsoft Server Speech Text till tal Voice (HR-HR, Matej)" | "HR-HR-Matej" | ✔️
hu – HU | Ungerska | Man | "Microsoft Server Speech Text till tal Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" | ✔️
ID-ID | Indonesiska| Man | "Microsoft Server Speech Text till tal Voice (ID-ID, andika)" | "ID-ID-andika" | ✔️
det – IT | Italienska | Man | "Microsoft Server Speech Text till tal Voice (IT-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (IT-IT, LuciaRUS)" | "IT-LuciaRUS" | ✔️
ja-JP | Japanska | Kvinna | "Microsoft Server Speech Text till tal Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" | ✔️
ko-KR | Koreanska | Kvinna | "Microsoft Server Speech Text till tal Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" | ✔️
MS-MY | Malajiska | Man | "Microsoft Server Speech Text till tal Voice (MS-MY, Rizwan)" | "MS-MY-Rizwan" | ✔️
NB-nej | Norska | Kvinna | "Microsoft Server Speech Text till tal Voice (NB-NO, HuldaRUS)" | "NB-NO-HuldaRUS" | ✔️
nl-NL | Nederländska | Kvinna | "Microsoft Server Speech Text till tal Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS" | ✔️
PL-PL | Polska | Kvinna | "Microsoft Server Speech Text till tal Voice (PL-PL, PaulinaRUS)" | "PL-PL-PaulinaRUS" | ✔️
pt-BR | Portugisiska (Brasilien) | Kvinna | "Microsoft Server Speech Text till tal Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" | ✔️
| | | Man |"Microsoft Server Speech Text till tal Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" | ✔️
pt-PT | Portugisiska (Portugal) | Kvinna | "Microsoft Server Speech Text till tal Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" | ✔️
RO-RO | Rumänska | Man | "Microsoft Server Speech Text till tal Voice (RO-RO, tillhör)" | "RO-RO-tillhör" | ✔️
ru-RU |Ryska| Kvinna | "Microsoft Server Speech Text till tal Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS | ✔️
sk-SK | Slovakiska | Man | "Microsoft Server Speech Text till tal Voice (sk-SK, Filip)" | "sk-SK-Filip" | ✔️
SL-SI | Slovenska | Man | "Microsoft Server Speech Text till tal Voice (SL-SI, Lado)" | "SL-SI-Lado" | ✔️
sa-SE | Svenska | Kvinna | "Microsoft Server Speech Text till tal Voice (sa-SE, HedvigRUS)" | "sa-SE-HedvigRUS" | ✔️
ta med | Tamiliska (Indien) | Man | "Microsoft Server Speech Text till tal Voice (ta-IN, Valluvar)" | "ta-IN-Valluvar" | ✔️
te-IN | Telugu (Indien) | Kvinna | "Microsoft Server Speech Text till tal Voice (te-IN, Chitra)" | "te-IN-Chitra" | ✔️
Th-TH | Thai | Man | "Microsoft Server Speech Text till tal Voice (th-TH, Pattara)" | "th-Pattara" | ✔️
TR-TR | Turkiska | Kvinna | "Microsoft Server Speech Text till tal Voice (tr-TR, SedaRUS)" | "TR-TR-SedaRUS" | ✔️
Vi – VN | Vietnamesiska | Man | "Microsoft Server Speech Text till tal Voice (vi-VN, a)" | "vi-VN-a" | ✔️
zh-CN | Kinesiska (fast landet) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" | ✔️
zh-HK | Kinesiska (Hongkong) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" | ✔️
zh-TW | Kinesiska (Taiwan) | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" | ✔️
| | | Kvinna | "Microsoft Server Speech Text till tal Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" | ✔️
| | | Man | "Microsoft Server Speech Text till tal Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" | ✔️

\* *ar-tex stöder modern standard arabiska (MSA).*

> [!NOTE]
> Du kan antingen använda den fullständiga tjänst namns mappningen eller det korta röst namnet i ditt tal syntes begär Anden.

### <a name="customization"></a>Anpassning

Röst anpassning är tillgänglig för de-DE, en-GB, en-i, en-US, ES-MX, fr-FR, IT-IT, pt-BR och zh-CN. Välj rätt språk version som matchar de tränings data du behöver för att träna en anpassad röst modell. Om till exempel de inspelnings data du har talas på engelska med en brittisk accent väljer du en-GB.  

> [!NOTE]
> Vi har inte stöd för dubbelriktad modell utbildning i anpassad röst, förutom kinesiska-engelska bi-språk. Välj kinesiskt "kinesiskt" (tvåspråkig) "om du vill träna en kinesisk röst som även kan tala med engelska. Röst träning i alla lokala platser börjar med en data uppsättning av 2000 + yttranden, med undantag för en-US och zh-CN där du kan börja med valfri storlek på tränings data.

## <a name="speech-translation"></a>Talöversättning

API: et för **tal översättning** stöder olika språk för översättning av tal till tal och tal till text. Käll språket måste alltid vara från språk tabellen för tal till text. Vilka mål språk som är tillgängliga beror på om översättnings målet är tal eller text. Du kan översätta inkommande tal till fler än [60 språk](https://www.microsoft.com/translator/business/languages/). Det finns en delmängd av dessa språk för [tal syntes](language-support.md#text-languages).

### <a name="text-languages"></a>Text språk

| Text språk    | Språkkod |
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
| Fijian      | `fj`          |
| Filipino      | `fil`          |
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
| Swahili      | `sw`          |
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
| Samoan      | `sm`          |
| Serbiska (kyrillisk)      | `sr-Cyrl`          |
| Serbiska (latinsk)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Svenska      | `sv`          |
| Tahitian      | `ty`          |
| Tamilska      | `ta`          |
| Thai      | `th`          |
| Tonganska      | `to`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Urdu      | `ur`          |
| Vietnamesiska      | `vi`          |
| Walesiska      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech Services](https://azure.microsoft.com/try/cognitive-services/)
* [Se känna igen tal i c #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
