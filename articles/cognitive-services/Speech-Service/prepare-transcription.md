---
title: Transkription riktlinjer för Speech Service-utbildning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du förbereder text anpassa- och språkdata och språkmodeller rösttyper för Speech-tjänsten.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 07b90942c8d231418888491fd47a9be3caff77ea
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467743"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>Transkription riktlinjer för att använda Speech-tjänsten

Anpassa **tal till Text** eller **Text till tal**, måste du ange text tillsammans med tal. Varje rad i texten motsvarar en enda uttryck. Texten måste matcha tal så nära som möjligt. Texten kallas en *avskrift*, och du måste skapa den i ett visst format.

Med Taltjänsten normaliserar indata för att bevara text. 

Den här artikeln beskrivs båda typerna av normalizations. Riktlinjerna kan variera för olika språk.

## <a name="us-english-en-us"></a>Amerikansk engelska (en-us)

Textdata ska skrivas en uttryck per rad, i oformaterad text, med hjälp av endast ASCII-teckenuppsättningen.

Undvik att använda utökade (Latin-1) eller Unicode skiljetecken. Dessa tecken kan ingå oavsiktligt när du förbereder data i ett ordbehandlingsprogram eller skrapa data från webbsidor. Ersätt tecknen med lämpliga ASCII-ändringar. Exempel:

| Tecken för att undvika | Ersättning |
|----- | ----- |
| ”Hello world” (inledande och avslutande citattecken) | ”Hello world” (dubbla citattecken) |
| Johns dag (enkelt citattecken) | Johns dag (apostrof) |
| Det var bra – Nej, det var bra! (tankstreck) | Det var bra--inte, det var bra! (bindestreck) |

### <a name="text-normalization-rules-for-english"></a>Text normalisering regler för engelska

Speech-tjänsten utför följande normalisering regler:

* Med gemener för all text
* Ta bort alla skiljetecken utom word-internt apostrofer
* Utöka siffror till talat formulär, inklusive kronor

Här följer några exempel:

| Originaltexten | Efter normalisering |
|----- | ----- |
| ”Heliga ko”! Dessa Batman. | Heliga ko SA batman |
| ”Vad”? Dessa Batman sidekick, (round robin). | vilka nämnda batman sidekick robin |
| Gå få - em! | Gå get em |
| Jag är double-jointed | Jag är dubbelt sammankopplade |
| 104 alm gata | en ojsan fyra alm gata |
| Justera till 102.7 | Justera till en ojsan två sju |
| Pi är ungefär 3,14 | Pi är cirka tre punkt en fyra |
| Det kostar $3,14 | Det kostar tre fjorton |

Gäller följande normalisering för text-avskrifter:

* Förkortningar ska skrivas ut i orden.
* Inte är standard numeriska strängar (till exempel vissa datum eller redovisning formulär) ska skrivas ut i orden.
* Vara bör transkriberas ord med icke-alfabetiska tecken eller blandade alfanumeriska tecken som uttalas.
* Lämna förkortningar som är uttalas ord vara orört (till exempel ”webbplats”, ”laserskrivare”, ”RAM” eller ”NATO”).
* Skriva förkortningar som uttalas som separata bokstäver med bokstäver som är avgränsade med blanksteg (till exempel ”IBM”, ”processor”, ”FBI: S”, ”TBD” eller ”NaN”). 

Här följer några exempel:

| Originaltexten | Efter normalisering |
|----- | ----- |
| 14 NE 3 Dr. | fjorton nordöst tredje enhet |
| Dr. Bruce banderoll | Läkare Bruce banderoll |
| James Bond, 007 | James Bond dubbelklicka ojsan sju |
| KE$ hög tillgänglighet | Kesha |
| Hur lång är 2 × 4 | Hur lång är två av fyra |
| Mötets går från 1-15: 00 | Mötets går från en till tre pm |
| Min blodgrupp är O + | Min blodgrupp är positivt O |
| Water är H20 | Water är H 2 O |
| spela upp OU812 genom Van Halen | spela upp O U 8 1 2 av Van Halen |
| UTF-8 med BOM | U T F 8 med BOM |

## <a name="chinese-zh-cn"></a>Kinesiska (zh-cn)

Textdata som har överförts till Custom Speech Service ska använda UTF-8-kodning med en byte-ordningsmarkering markör. Filen ska skrivas en uttryck per rad.

Undvik att använda halv bredd skiljetecken. Dessa tecken kan ingå oavsiktligt när du förbereder data i ett ordbehandlingsprogram eller skrapa data från webbsidor. Ersätt dem med lämpliga ändringar i full bredd. Exempel:

| Tecken för att undvika | Ersättning |
|----- | ----- |
| ”你好” (inledande och avslutande citattecken) | ”你好” (dubbla citattecken) |
| 需要什么帮助? (frågetecken) | 需要什么帮助? |

### <a name="text-normalization-rules-for-chinese"></a>Text normalisering regler för kinesiska

Speech-tjänsten utför följande normalisering regler:

* Ta bort alla skiljetecken
* Expandera siffror i talat formulär
* Konvertera full bredd bokstäver till halv bredd bokstäver
* Med versaler för alla svenska ord

Här följer några exempel:

| Originaltexten | Efter normalisering |
|----- | ----- |
| 3,1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Innan du importerar texten bör du använda följande normalisering för den:

* Förkortningar ska skrivas ut i ord (som i talat formulär).
* Skriva ut numeriska strängar i talat form.

Här följer några exempel:

| Originaltexten | Efter normalisering |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Andra språk

Textdata som överförs till den **tal till Text** -tjänsten måste använda UTF-8-kodning med en byte-ordningsmarkering markör. Filen ska skrivas en uttryck per rad.

> [!NOTE]
> I följande exempel används tyska. Riktlinjerna gäller dock för alla språk som inte är engelska (USA) eller kinesiska.

### <a name="text-normalization-rules-for-german"></a>Text normalisering regler för tyska

Speech-tjänsten utför följande normalisering regler:

* Med gemener för all text
* Ta bort alla skiljetecken, inklusive olika typer av citattecken (”test”, 'test', ”test” och «testa» är OK)
* Tar bort rader med specialtecken från set-¢ ¤ ¥... § © ª ¬® ° + ² µ × ÿ Ø¬¬
* Utöka siffror till ordform, inklusive dollar eller Euro tillsammans
* Acceptera umlauts endast för en, o och. andra kommer att ersättas av ”e” eller tas bort

Här följer några exempel:

| Originaltexten | Efter normalisering |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |

Innan du importerar texten bör du använda följande normalisering för den:

* Punkter ska vara ”,” och inte ””.
* Tidsavgränsare mellan många timmar och minuter som ska vara ”:” och inte ””. (exempel: 12:00 Uhr).
* Förkortningar, till exempel ”ca”. ersätts inte. Vi rekommenderar att du använder den fullständiga formen.
* De fyra huvudsakliga matematiska operatörerna (+, -, \*, och /) tas bort. Vi rekommenderar att ersätta dem med deras literal form: ”plus”, ”minus”, ”skadlig” och ”geteilt”.
* Samma sak gäller jämförelseoperatorer (=, <, och >). Vi rekommenderar att ersätta dem med ”gleich” ”, kleiner sensorn”, och ”grösser sensorn”.
* Använd delar, till exempel 3 och 4 i word-format (till exempel ”drei viertel” i stället för ¾).
* Ersätt € symbolen med ordform ”Euro”.

Här följer några exempel:

| Originaltexten | Efter användarens normalisering | När du har system normalisering
|--------  | ----- | -------- |
| ES ist 12.23 Uhr | ES ist 12:23 Uhr | ES ist zwölf uhr drei und zwanzig uhr |
| {12,45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 – 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech-tjänsten](https://azure.microsoft.com/try/cognitive-services/)
- [Identifiera tal i C#](quickstart-csharp-dotnet-windows.md)
