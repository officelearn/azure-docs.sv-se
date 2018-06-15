---
title: Skrivfel riktlinjer för tal utbildning | Microsoft Docs
description: Lär dig hur du förbereder text för att anpassa ljud och språk modeller och röst teckensnitt för tjänsten tal.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 93ab7c81a773f692b2b970bb1901d82b7aceb5a2
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356153"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Skrivfel riktlinjer för att använda tal service

Anpassa **tal till Text** eller **Text till tal**, du måste ange text tillsammans med tal. Varje rad i texten som motsvarar en enskild utterance. Texten som kallas en *betyg*, och du måste skapa den i ett specifikt format.

Dikterings-tjänsten utför vissa normalizations du för att göra texten konsekvent. Andra normalisering uppgifter måste utföras innan texten skickas för utbildning. 

Den här artikeln beskriver båda typerna av normalizations. Riktlinjerna kan variera för olika språk.

## <a name="us-english-en-us"></a>Engelska (en-US)

Text-data som överförs till den här tjänsten ska skrivas i oformaterad text med endast ASCII-teckenuppsättningen. Varje rad i filen ska innehålla texten för en enskild utterance.

Det är viktigt att undvika användningen av utökade (Latin-1) eller Unicode skiljetecken. Dessa tecken kan ingå oavsiktligt när du förbereder data i ett ordbehandlingsprogram eller skrapning data från webbsidor. Ersätta dessa tecken med lämpliga ASCII-ändringar. Exempel:

| Tecken för att undvika | Ersättning |
|----- | ----- |
| ”Hello world” (öppnar och stänger dubbla citattecken) | ”Hello world” (dubbla citattecken) |
| Johns dag (enkelt citattecken) | Johns dag (apostrof) |
| Det var bra – Nej, det var bra! (tankstreck) | Det var bra--inte, det var bra! (bindestreck) |

### <a name="text-normalization-performed-by-the-service"></a>Text Normalisering utförs av tjänsten

Dikterings-tjänsten utför följande text normalisering text betyg.

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

### <a name="text-normalization-you-must-perform"></a>Text normalisering måste du

Gäller följande normalisering text-betyg.

*   Förkortningar ska skrivas i ord återspeglar talade formulär
*   Icke-standard numeriska strängar (till exempel vissa datum eller redovisning formulär) ska skrivas i ord
*   Ord med icke-alfabetiska tecken eller blandade alfanumeriska tecken som ska vara sätt som uttalas
*   Lämna förkortningar uttalas som ord vara orört. Till exempel polärdiagram, laserskrivare, RAM-minne, NATO och Mr.
*   Skriva förkortningar uttalas som separata siffror, bokstäver avgränsade med blanksteg. Till exempel, IBM, CPU, FBI, TBD, NaN. 

Här följer några exempel:

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| 14 NE 3 Dr. | 14 northeast tredje enhet |
| Dr. Strangelove | Doctor Strangelove |
| James avkastningen 007 | James Bond dubbla OJ sju |
| KE$ hög tillgänglighet | Kesha |
| Hur lång tid är 2 x 4 | Hur lång tid är två av fyra |
| Mötet går från 1-15: 00 | Mötet går från en till tre pm |
| Min blodgrupp är O + | Min blodgrupp är positivt O |
| Vattenstämplar är H20 | Vattenstämplar är H 2 O |
| spela upp OU812 av Van Halen | spela upp O U 8 1 2 av Van Halen |
| UTF-8 med BOM | U T F 8 med BOM |

## <a name="chinese-zh-cn"></a>Kinesiska (zh-CN)

Textdata som överförts till anpassad tal tjänsten ska använda UTF-8-kodning med byte-ordning markör. Varje rad i filen ska innehålla texten för en enskild utterance.

Det är viktigt att undvika användningen av halv bredd skiljetecken. Dessa tecken kan ingå oavsiktligt när du förbereder data i ett ordbehandlingsprogram eller skrapning data från webbsidor. Ersätta dem med lämpliga full bredd ändringar. Exempel:

| Tecken för att undvika | Ersättning |
|----- | ----- |
| ”你好” (inledande och avslutande dubbla citattecken) | ”你好” (citattecken) |
| 需要什么帮助? (frågetecken) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-service"></a>Text Normalisering utförs av tjänsten

Dikterings-tjänsten utför följande text normalisering text betyg.

*   Ta bort alla skiljetecken
*   Expandera siffror till talade formulär
*   Konvertera full bredd bokstäver till halv bredd bokstäver
*   Övre skiftläge alla svenska ord

Här följer några exempel.

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| 3,1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-you-must-perform"></a>Text normalisering måste du

Använd följande normalisering innan du importerar den.

*   Förkortningar ska skrivas i ord återspeglar talade formulär
*   Den här tjänsten omfattar inte alla numeriska kvantiteter. Det är mer tillförlitlig för att skriva numeriska strängar i talade form.

Här följer några exempel.

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Andra språk

Textdata har överförts till den **tal till Text** tjänsten måste använda UTF-8-kodning med byte-ordning markör. Varje rad i filen ska innehålla texten för en enskild utterance.

> [!NOTE]
> Dessa exempel används tyska. Men gäller dessa riktlinjer för alla språk som inte är engelska (USA) eller kinesiska.

### <a name="text-normalization-performed-by-the-service"></a>Text Normalisering utförs av tjänsten

Dikterings-tjänsten utför följande text normalisering text betyg.

*   Lägre skiftläge alla text
*   Ta bort alla skiljetecken, inklusive olika typer av citattecken (”test”, ”test”, ”test” eller «testa» är ok)
*   Tar bort en rad som innehåller specialtecken från uppsättningen ^ ¢ £ ¤ ¥... § © ª ¬® ° + ² µ × ÿ Ø¬¬
*   Utökningen av siffror till word-format, inklusive dollar eller euro belopp
*   Umlauts accepteras endast för en, o u; andra ersätts av: ”e” eller tas bort

Här följer några exempel

| Ursprungliga texten | Efter normalisering |
|----- | ----- |
| Ring Frankfurter | ring frankfurter |
| ”Hallo Mama”! sagt dör Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |
| DAS macht $10 | DAS macht zehn dollar |

### <a name="text-normalization-you-must-perform"></a>Text normalisering måste du

Använd följande normalisering innan du importerar den.

*   Decimaltecknet ska vara ”,” och inte ””.: 2,3 och inte 2.3%
*   Tidsavgränsare mellan timmar och minuter måste vara ”:” och inte ””.: 12:00 Uhr
*   Förkortningar, till exempel 'ca.', 'bzw'. ersätts inte. Vi rekommenderar att du använder den fullständiga formen.
*   De fem huvudsakliga matematiska operatörerna tas bort: +, -, \*, /. Vi rekommenderar att ersätta dem med sin literal form: plus minus a geteilt.
*   Detsamma gäller för jämförelseoperatorer (=, <>,) - gleich kleiner sensorn, grösser sensorn
*   Använd bråk, till exempel 3/4 i word-format (till exempel 'drei viertel' i stället för ¾)
*   Ersätt € symbol med formuläret ordet ”Euro”

Här följer några exempel.

| Ursprungliga texten | När användarens normalisering | När systemet normalisering
|--------  | ----- | -------- |
| ES ist 12.23Uhr | ES ist 12:23Uhr | ES ist zwölf uhr drei och zwanzig uhr |
| {12,45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner sensorn 5 | drei kleiner sensorn vier |
| 2 + 3 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| DAS macht 12€ | DAS macht 12 euro | DAS macht zwölf euro |

## <a name="next-steps"></a>Nästa steg

- [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
- [Identifiera tal i C#](quickstart-csharp-windows.md)
