---
title: Skrivfel riktlinjerna i anpassade tal Service i Azure | Microsoft Docs
description: Lär dig hur du förbereder data för anpassad tal Service i kognitiva Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 2785a35ac7583ac3d9503cb721d10078d86aa365
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351831"
---
# <a name="transcription-guidelines"></a>Skrivfel riktlinjer
Följande riktlinjer för skrivfel ska följas för att säkerställa bästa användning av textdata för acoustic och språk modellen anpassning. Dessa riktlinjer är språkspecifika.

## <a name="text-normalization"></a>Text normalisering

För optimal användning i acoustic eller språk modellen för anpassning av måste textdata normaliseras, vilket innebär att omvandlas till en standard, entydigt form som kan läsas av systemet. Det här avsnittet beskriver text Normalisering utförs av anpassade tal tjänsten när data importeras och normalisering texten som användaren måste utföra före import av data.

## <a name="inverse-text-normalization"></a>Inverterade text normalisering

Att konvertera ”raw” oformaterad text till formaterad text, d.v.s. med versaler och skiljetecken, kallas inverterade text normalisering (ITN). ITN utförs på resultat som returneras av Microsoft kognitiva Services tal-API. En anpassad slutpunkt som distribueras med anpassade tal tjänsten använder samma ITN som Microsoft kognitiva Services tal-API. Men stöder den här tjänsten för närvarande inte anpassade ITN så att nya villkoren som introducerades av en anpassad språkmodell inte formateras i resultat.

## <a name="transcription-guidelines-for-en-us"></a>Skrivfel riktlinjer för en-US

Textdata överförs till den här tjänsten ska skrivas i oformaterad text med endast ASCII utskrivbara teckenuppsättningen. Varje rad i filen ska innehålla texten för en enskild utterance.

Det är viktigt att undvika användningen av Unicode skiljetecken. Detta kan inträffa av misstag om förbereder data i ett word bearbetning av programmet eller skrapning data från webbsidor. Ersätta dessa tecken med lämpliga ASCII-ändringar. Exempel:

| Unicode att undvika | ASCII-ersättning |
|----- | ----- |
| ”Hello world” (öppnar och stänger dubbla citattecken) | ”Hello world” (dubbla citattecken) |
| Johns dag (enkelt citattecken) | Johns dag (apostrof) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text Normalisering utförs av tjänsten anpassad tal

Den här tjänsten utför följande text normalisering på data som importeras som en datauppsättning för språk eller transcriptions för ett ljud datamängden. Detta inkluderar

*   Lägre skiftläge alla text
*   Ta bort alla skiljetecken utom word-interna apostrofer
*   Utökningen av siffror till talade formulär, inklusive kronor

Här följer några exempel

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| Starbucks kaffe | Starbucks kaffe |
| ”Heliga ko”! Dessa Batman. | Heliga ko SA batman |
| ”Vad”? Dessa Batman sidekick, Robin. | vilka nämnda batman sidekick robin |
| Gå get - em! | Gå get em |
| Jag är double-jointed | i är dubbla skala |
| 104 Main gata | en OJ fyra huvudsakliga gata |
| Ställa in 102.7 | finjustera till en OJ två punkt sju |
| Pi är ungefär 3,14 | Pi är cirka tre punkt en fyra |
| Det kostar $3,14 | Det kostar tre 14 |

### <a name="text-normalization-required-by-users"></a>Text normalisering krävs av användare

För att säkerställa att dina data på bästa sätt, bör följande normalisering regler tillämpas på dina data innan du importerar den.

*   Förkortningar ska skrivas i ord återspeglar talade formulär
*   Icke-standard numeriska strängar ska skrivas i ord
*   Ord med icke-alfabetiska tecken eller blandade alfanumeriska tecken som ska vara sätt som uttalas
*   Vanliga förkortningar kan lämnas som en enda enhet utan punkter eller blanksteg mellan bokstäverna, men alla andra förkortningar ska skrivas i separata bokstäver med varje bokstav avgränsade med ett blanksteg

Här följer några exempel

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| 14 NE 3 Dr. | 14 northeast tredje enhet |
| Dr. Strangelove | Doctor Strangelove |
| James avkastningen 007 | James koppla samman dubbla OJ sju |
| KE$ hög tillgänglighet | Kesha |
| Hur lång tid är 2 x 4 | Hur lång tid är två av fyra |
| Mötet går från 1-15: 00 | Mötet går från en till tre pm |
| Min blodgrupp är O + | Min blodgrupp är positivt O |
| Vattenstämplar är H20 | Vattenstämplar är H 2 O |
| spela upp OU812 av Van Halen | spela upp O U 8 1 2 av Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>Skrivfel riktlinjer för zh-CN

Textdata som överförts till anpassad tal tjänsten ska använda **UTF-8-kodning (inklusive BOM)**. Varje rad i filen ska innehålla texten för en enskild utterance.

Det är viktigt att undvika användningen av halv bredd skiljetecken. Detta kan inträffa av misstag om förbereder data i ett word bearbetning av programmet eller skrapning data från webbsidor. Ersätta dessa tecken med lämpliga full bredd ändringar. Exempel:

| Unicode att undvika | ASCII-ersättning |
|----- | ----- |
| ”你好” (inledande och avslutande dubbla citattecken) | ”你好” (citattecken) |
| 需要什么帮助? (frågetecken) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text Normalisering utförs av tjänsten anpassad tal

Tjänsten tal utför följande text normalisering på data som importeras som en datauppsättning för språk eller transcriptions för ett ljud datamängden. Detta inkluderar

*   Ta bort alla skiljetecken
*   Utökningen av talen i talade formulär
*   Konvertera full bredd bokstäver till halv bredd bokstäver.
*   Övre skiftläge alla svenska ord

Här följer några exempel:

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| 3,1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>Text normalisering krävs av användare

För att säkerställa bästa användningen av dina data, bör följande normalisering regler tillämpas till dina data _tidigare_ att importera den.

*   Förkortningar ska skrivas i ord återspeglar talade formulär
*   Den här tjänsten omfatta inte alla numeriska kvantiteter. Det är mer tillförlitlig för att skriva numeriska strängar i talade form

Här följer några exempel

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Skrivfel riktlinjer för de-DE

Textdata som överförts till anpassad tal tjänsten bör endast använda **UTF-8-kodning (inklusive BOM)**. Varje rad i filen ska innehålla texten för en enskild utterance.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text Normalisering utförs av tjänsten anpassad tal

Den här tjänsten utför följande text normalisering på data som importeras som en datauppsättning för språk eller transcriptions för ett ljud datamängden. Detta inkluderar

*   Lägre skiftläge alla text
*   Ta bort alla skiljetecken, inklusive engelska eller tyska citattecken (”test”, ”test”, ”test” eller «testa» är ok)
*   Ta bort en rad som innehåller alla tecken inklusive: ^ ¢ £ ¤ ¥... § © ª ¬® ° + ² µ × ÿ Ø¬¬
*   Utökningen av siffror till word-format, inklusive dollar eller euro belopp
*   Accepterar endast umlauts för en, o u; andra ska ersättas med ”th” eller tas bort

Här följer några exempel

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| Ring Frankfurter | ring frankfurter |
| ”Hallo Mama”! sagt dör Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |
| DAS macht $10 | DAS macht zehn dollar |


### <a name="text-normalization-required-by-users"></a>Text normalisering krävs av användare

För att säkerställa att dina data på bästa sätt, bör följande normalisering regler tillämpas på dina data innan du importerar den.

*   Decimaltecknet ska vara och inte. t.ex. 2,3 och inte 2.3%
*   Tidsavgränsare mellan timmar och minuter ska vara: och inte., t.ex. 12:00 Uhr
*   Förkortningar, till exempel 'ca.', 'bzw'. ersätts inte. Vi rekommenderar för att använda den fullständiga formen för att få rätt uttal.
*   De fem huvudsakliga matematiska operatörerna tas bort: +, -, \*, /.
 Vi rekommenderar för att ersätta dem med sin literal form plus minus a geteilt.
*   Detsamma gäller för de komparatorer (=, <>,) - gleich kleiner sensorn, grösser sensorn
*   Använd bråk, till exempel 3/4 i word-formulär drei viertel i stället för ¾
*   Ersätt € symbol med formuläret ordet ”Euro”


Här följer några exempel

| Ursprungliga texten | När användarens normalisering | När systemet normalisering
|--------  | ----- | -------- |
| ES ist 12.23Uhr | ES ist 12:23Uhr | ES ist zwölf uhr drei och zwanzig uhr |
| {12,45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner sensorn 5 | drei kleiner sensorn vier |
| 2 + 3 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| DAS macht 12€ | DAS macht 12 euro | DAS macht zwölf euro |



## <a name="next-steps"></a>Nästa steg
* [Hur du använder en anpassad till text till tal-slutpunkt](cognitive-services-custom-speech-create-endpoint.md)
* Noggrannhet kan förbättras med din [anpassade ljud modellen](cognitive-services-custom-speech-create-acoustic-model.md)
* Noggrannhet kan förbättras med en [anpassade språk modellen](cognitive-services-custom-speech-create-language-model.md)
