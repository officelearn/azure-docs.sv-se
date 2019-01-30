---
title: Transkription riktlinjerna i Custom Speech Service på Azure | Microsoft Docs
description: Lär dig hur du förbereder data för Custom Speech Service i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 7d9b8b5a2ad8ccb447462ef7d34a8c25e5af6ef4
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225031"
---
# <a name="transcription-guidelines"></a>Riktlinjer för transkription

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Följande riktlinjer för taltranskription ska följas för att säkerställa bästa användning av din textdata för- och språkdata modellanpassning. Dessa riktlinjer är språkspecifika.

## <a name="text-normalization"></a>Text normalisering

För optimal användning i modellanpassning akustiska eller språk, måste textdata normaliseras, vilket innebär att omvandlas till en standard, entydiga form som kan läsas av systemet. Det här avsnittet beskriver text Normalisering utförs av Custom Speech Service när data importeras och normalisering text som användaren måste utföra innan du importera data.

## <a name="inverse-text-normalization"></a>Inverterade text normalisering

Att konvertera ”rådata” oformaterad text tillbaka till formaterad text, till exempel med versaler och skiljetecken, kallas inverterade text normalisering (ITN). ITN utförs på resultat som returneras av Microsoft Cognitive Services tal-API. En anpassad slutpunkt som distribueras med Custom Speech Service använder samma ITN som Microsoft Cognitive Services tal-API. Men stöder den här tjänsten för närvarande inte anpassade ITN så att nya villkoren som introducerades av en anpassad språkmodell inte formaterade i igenkänning av resultaten.

## <a name="transcription-guidelines-for-en-us"></a>Transkription riktlinjer för en-US

Textdata som överförs till den här tjänsten måste skrivas i oformaterad text med endast de utskrivbara ASCII-teckenuppsättningen. Varje rad i filen ska innehålla texten för en enda uttryck.

Det är viktigt att undvika användningen av Unicode skiljetecken. Detta kan inträffa av misstag om förbereder data i ett word bearbetning av programmet eller skrapning data från webbsidor. Ersätt följande tecken med lämpliga ASCII-ändringar. Exempel:

| Unicode att undvika | ASCII-ersättning |
|----- | ----- |
| ”Hello world” (öppnar och stänger dubbla citattecken) | ”Hello world” (dubbla citattecken) |
| Johns dag (enkelt citattecken) | Johns dag (apostrof) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalisering av Custom Speech Service

Följande text normalisering utför den här tjänsten på data som importeras som en datauppsättning för språk eller avskrifter för en akustisk datauppsättning. Detta inkluderar

*   Lägre skiftläge all text
*   Ta bort alla skiljetecken utom word-internt apostrofer
*   Expansion av talen i talat formulär, inklusive kronor

Här följer några exempel

| Originaltexten | Efter normalisering |
|----- | ----- |
| Starbucks kaffe | Starbucks kaffe |
| ”Heliga ko”! Dessa Batman. | Heliga ko SA batman |
| ”Vad”? Dessa Batman sidekick, (round robin). | vilka nämnda batman sidekick robin |
| Gå få - em! | Gå get em |
| Jag är double-jointed | i är dubbelt skala |
| 104 Main gata | en ojsan fyra huvudsakliga gata |
| Justera till 102.7 | Justera till en ojsan två punkt sju |
| Pi är ungefär 3,14 | Pi är cirka tre punkt en fyra |
| Det kostar $3,14 | Det kostar tre fjorton |

### <a name="text-normalization-required-by-users"></a>Text normalisering som krävs av användare

För att säkerställa bästa användning av dina data, bör följande normalisering regler tillämpas på dina data innan du importerar den.

*   Förkortningar ska skrivas ut i ord att återspegla talat formulär
*   Inte är standard numeriska strängar som ska skrivas ut i ord
*   Vara bör transkriberas ord med icke-alfabetiska tecken eller blandade alfanumeriska tecken som förkortning
*   Vanliga förkortningar kan lämnas som en enda enhet utan punkter eller blanksteg mellan bokstäverna, men alla andra förkortningar ska skrivas ut i separata bokstäver med varje bokstav avgränsat med ett enda blanksteg

Här följer några exempel

| Originaltexten | Efter normalisering |
|----- | ----- |
| 14 NE 3 Dr. | fjorton nordöst tredje enhet |
| Dr. Strangelove | Läkare Strangelove |
| James Bond 007 | James koppla samman dubbla ojsan sju |
| KE$ hög tillgänglighet | Kesha |
| Hur lång är 2 × 4 | Hur lång är två av fyra |
| Mötets går från 1-15: 00 | Mötets går från en till tre pm |
| Min blodgrupp är O + | Min blodgrupp är positivt O |
| Water är H20 | Water är H 2 O |
| spela upp OU812 genom Van Halen | spela upp O U 8 1 2 av Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>Transkription riktlinjer för zh-CN

Textdata som överförs till Custom Speech Service ska använda **UTF-8-kodning (inklusive BOM)**. Varje rad i filen ska innehålla texten för en enda uttryck.

Det är viktigt att undvika användningen av halv bredd skiljetecken. Detta kan inträffa av misstag om förbereder data i ett word bearbetning av programmet eller skrapning data från webbsidor. Ersätt följande tecken med lämpliga ändringar i full bredd. Exempel:

| Unicode att undvika | ASCII-ersättning |
|----- | ----- |
| ”你好” (öppna och stänga dubbla citattecken) | ”你好” (dubbla citattecken) |
| 需要什么帮助? (frågetecken) | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalisering av Custom Speech Service

Den här speech-tjänsten utför följande text normalisering på data som importeras som en datauppsättning för språk eller avskrifter för en akustisk datauppsättning. Detta inkluderar

*   Ta bort alla skiljetecken
*   Expansion av talen i talat formulär
*   Konvertera full bredd bokstäver till halv bredd bokstäver.
*   Övre skiftläge alla engelska ord

Här följer några exempel:

| Originaltexten | Efter normalisering |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>Text normalisering som krävs av användare

För att säkerställa bästa användning av dina data, gäller följande regler för normalisering till dina data _tidigare_ för import av den.

*   Förkortningar ska skrivas ut i ord att återspegla talat formulär
*   Den här tjänsten täcker inte alla numeriska kvantiteter. Det är mer tillförlitlig för att skriva numeriska strängar i talat form

Här följer några exempel

| Originaltexten | Efter normalisering |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Transkription riktlinjer för de-DE

Textdata som överförs till Custom Speech Service bör endast använda **UTF-8-kodning (inklusive BOM)**. Varje rad i filen ska innehålla texten för en enda uttryck.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalisering av Custom Speech Service

Följande text normalisering utför den här tjänsten på data som importeras som en datauppsättning för språk eller avskrifter för en akustisk datauppsättning. Detta inkluderar

*   Lägre skiftläge all text
*   Ta bort alla skiljetecken, inklusive engelska eller tyska citattecken (”test”, 'test', ”test” eller «testa» är ok)
*   Ta bort alla rader som innehåller några specialtecken, inklusive: ^ ¢ £ ¤ ¥... § © ª ¬® ° + ² µ × ÿ Ø¬¬
*   Expansion av talen i word-formulär, inklusive dollar eller euro belopp
*   Vi accepterar endast umlauts för en, o u; andra kommer att ersättas med ”e” eller tas bort

Här följer några exempel

| Originaltexten | Efter normalisering |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ”Hallo Mama”! sagt dör Tochter. | hallo mama sagt chips tochter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |
| DAS macht $10 | DAS macht zehn dollar |


### <a name="text-normalization-required-by-users"></a>Text normalisering som krävs av användare

För att säkerställa bästa användning av dina data, bör följande normalisering regler tillämpas på dina data innan du importerar den.

*   Decimaltecknet ska vara, och inte. t.ex. 2,3% och inte 2.3%
*   Tidsavgränsare mellan många timmar och minuter bör vara: och inte., t.ex. 12:00 Uhr
*   Förkortningar, till exempel 'ca.', 'bzw ”. ersätts inte. Vi rekommenderar att du använder den fullständiga formen för att få korrekt uttal.
*   De fem viktigaste matematiska operatörerna tas bort: +, -, \*, /.
 Vi rekommenderar för att ersätta dem med deras literal form plus minus skadlig geteilt.
*   Detsamma gäller för de komparatorer (=, <>,) - gleich kleiner sensorn, grösser sensorn
*   Bråkdelar, till exempel 3 och 4 i ordform drei viertel i stället för ¾
*   Ersätt € symbolen med ordform ”Europa”


Här följer några exempel

| Originaltexten | Efter användarens normalisering | När du har system normalisering
|--------  | ----- | -------- |
| ES ist 12.23Uhr | ES ist 12:23Uhr | ES ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| DAS macht 12€ | DAS macht 12 euro | DAS macht zwölf euro |



## <a name="next-steps"></a>Nästa steg
* [Hur du använder en anpassad tal till text-slutpunkt](cognitive-services-custom-speech-create-endpoint.md)
* Förbättra noggrannheten med din [anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md)
* Förbättra noggrannheten med en [anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md)
