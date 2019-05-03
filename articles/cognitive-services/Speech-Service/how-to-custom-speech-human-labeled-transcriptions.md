---
title: Riktlinjer för mänskligt etikett avskrifter – Speech Services
titlesuffix: Azure Cognitive Services
description: Om du behöver för att förbättra noggrannheten, särskilt problem som orsakas när ord tas bort eller felaktigt ersätta kommer du vill använda mänskliga etikett avskrifter tillsammans med din ljuddata. Vad är mänskliga etikett avskrifter? Det är lätt, de är ord för ord, ordagrant avskrifter av en ljudfil.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7f0b467284872f3d936984741c6d092705008a5a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025928"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Så här skapar du mänskliga etikett avskrifter

Om du behöver för att förbättra noggrannheten, särskilt problem som orsakas när ord tas bort eller felaktigt ersätta kommer du vill använda mänskliga etikett avskrifter tillsammans med din ljuddata. Vad är mänskliga etikett avskrifter? Det är lätt, de är ord för ord, ordagrant avskrifter av en ljudfil.

Ett stort urval av avskrift data krävs för att förbättra taligenkänning, föreslår vi att tillhandahålla mellan 10 och 1 000 timmar efter avskrift data. Vi ska gå igenom riktlinjer som hjälper dig att skapa högkvalitativa avskrifter på den här sidan. Den här guiden har delats upp av nationell inställning med avsnitt för engelska (USA), Mandarin kinesiska och tyska.

## <a name="us-english-en-us"></a>Amerikansk engelska (en-US)

Du måste tillhandahålla mänskliga etikett avskrifter för engelska ljud som oformaterad text, endast med ASCII-tecken. Undvik att använda Latin-1 eller Unicode skiljetecken. De här tecknen läggs ofta oavsiktligt när du kopierar text från ett ordbehandlingsprogram eller skrapning data från webbsidor. Om de här tecknen finns, se till att uppdatera dem med lämplig ASCII ersättningen.

Några exempel:

| Tecken för att undvika | Ersättning | Anteckningar |
|---------------------|--------------|-------|
| ”Hello world” | ”Hello world” | De inledande och avslutande citattecken har ersatts med rätt ASCII-tecken. |
| Johns dag | Johns dag | Har bytts ut mot apostrofen med rätt ASCII-tecken. |
| Det var bra – Nej, det var bra! | Det var bra--inte, det var bra! | Tankstrecket har ersatts med två bindestreck. |

### <a name="text-normalization-for-us-english"></a>Text normalisering för engelska (USA)

Text normalisering är omvandlingen av orden i ett enhetligt format som används för att träna en modell. Vissa normalisering reglerna tillämpas på text automatiskt, men vi rekommenderar att använda dessa riktlinjer när du förbereder dina mänskliga etikett avskrift data:

* Skriva ut förkortningar i orden.
* Skriva ut inte är standard numeriska strängar med ord (till exempel redovisning villkor).
* Icke-alfabetiska tecken eller blandade alfanumeriska tecken ska transkriberas som uttalas.
* Förkortningar som uttalas ord bör inte redigeras (till exempel ”webbplats”, ”laserskrivare”, ”RAM” eller ”NATO”).
* Skriv ut förkortningar som uttalas separat bokstäver med varje bokstav avgränsade med blanksteg.

Här följer några exempel på normalisering som du bör utföra på utskrift:

| Originaltexten | Text efter normalisering |
|---------------|--------------------------|
| Dr. Bruce Banner | Läkare Bruce banderoll |
| James Bond, 007 | James Bond dubbelklicka ojsan sju |
| KE$ hög tillgänglighet | Kesha |
| Hur lång är 2 × 4 | Hur lång är två av fyra |
| Mötets går från 1-15: 00 | Mötets går från en till tre pm |
| Min blodgrupp är O + | Min blodgrupp är positivt O |
| Water är H20 | Water är H 2 O |
| spela upp OU812 genom Van Halen | spela upp O U 8 1 2 av Van Halen |
| UTF-8 med BOM | U T F 8 med BOM |

Följande normalisering regler tillämpas automatiskt på avskrifter:

* Använd gemena bokstäver.
* Ta bort alla skiljetecken utom apostrofer inom orden.
* Expandera siffror till ord/sägs format, till exempel kronor.

Här följer några exempel på Normalisering utförs automatiskt på utskrift:

| Originaltexten | Text efter normalisering |
|---------------|--------------------------|
| ”Heliga ko”! Dessa Batman. | Heliga ko SA batman |
| ”Vad”? said Batman's sidekick, Robin. | vilka nämnda batman sidekick robin |
| Gå få - em! | Gå get em |
| Jag är double-jointed | Jag är dubbelt sammankopplade |
| 104 Elm Street | en ojsan fyra alm gata |
| Justera till 102.7 | Justera till en ojsan två sju |
| Pi är ungefär 3,14 | Pi är cirka tre punkt en fyra |
Det kostar $3,14| Det kostar tre fjorton |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin kinesiska (zh-cn)

Human etikett avskrifter för Mandarin kinesiska ljud måste vara kodad med en byte-ordningsmarkering markör UTF-8. Undvik att använda halv bredd skiljetecken. Dessa tecken kan ingå oavsiktligt när du förbereder data i ett ordbehandlingsprogram eller skrapa data från webbsidor. Om de här tecknen finns, se till att uppdatera dem med lämplig full bredd ersättningen.

Några exempel:

| Tecken för att undvika | Ersättning | Anteckningar |
|---------------------|--------------|-------|
| "你好" | "你好" | De inledande och avslutande citattecken har ersatts med rätt tecken. |
| 需要什么帮助? | 需要什么帮助？ | Har bytts ut mot frågetecknet med rätt tecken. |

### <a name="text-normalization-for-mandarin-chinese"></a>Text normalisering Mandarin kinesiska

Text normalisering är omvandlingen av orden i ett enhetligt format som används för att träna en modell. Vissa normalisering reglerna tillämpas på text automatiskt, men vi rekommenderar att använda dessa riktlinjer när du förbereder dina mänskliga etikett avskrift data:

* Skriva ut förkortningar i orden.
* Skriva ut numeriska strängar i talat form.

Här följer några exempel på normalisering som du bör utföra på utskrift:

| Originaltexten | Text efter normalisering |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

Följande normalisering regler tillämpas automatiskt på avskrifter:

* Ta bort alla skiljetecken
* Expandera siffror i talat formulär
* Konvertera full bredd bokstäver till halv bredd bokstäver
* Med versaler för alla svenska ord

Här följer några exempel på Normalisering utförs automatiskt på utskrift:

| Originaltexten | Text efter normalisering |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Tyska (de-DE) och andra språk

Human etikett avskrifter tyska ljud (och andra icke-engelska eller Mandarin kinesiska språk) måste vara kodad med en byte-ordningsmarkering markör UTF-8. En etikett mänskliga avskrift bör anges för varje ljudfil.

### <a name="text-normalization-for-german"></a>Text normalisering för tyska

Text normalisering är omvandlingen av orden i ett enhetligt format som används för att träna en modell. Vissa normalisering reglerna tillämpas på text automatiskt, men vi rekommenderar att använda dessa riktlinjer när du förbereder dina mänskliga etikett avskrift data:

*   Write decimal points as "," and not ".".
*   Skriva tidsavgränsare som ”:” och inte ””. (till exempel: 12:00 Uhr).
*   Förkortningar, till exempel ”ca”. ersätts inte. Vi rekommenderar att du använder den fullständiga talat formen.
*   De fyra huvudsakliga matematiska operatörerna (+, -, \*, och /) tas bort. Vi rekommenderar att ersätta dem med skriftlig form: ”plus”, ”minus”, ”skadlig” och ”geteilt”.
*   Jämförelseoperatorer tas bort (=, <, och >). Vi rekommenderar att ersätta dem med ”gleich” ”, kleiner sensorn”, och ”grösser sensorn”.
*   Skriva bråkdelar, till exempel 3 och 4 i skriftlig form (till exempel: ”drei viertel” i stället för 3 och 4).
*   Ersätt symbolen ”€” med dess skriftlig form ”Euro”.

Här följer några exempel på normalisering som du bör utföra på utskrift:

| Originaltexten | Text efter användaren normalisering | Text efter system normalisering |
|---------------|-------------------------------|---------------------------------|
| ES ist 12.23 Uhr | ES ist 12:23 Uhr | ES ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

Följande normalisering regler tillämpas automatiskt på avskrifter:

* Använd gemener för all text.
* Ta bort alla skiljetecken, inklusive olika typer av citattecken (”test”, 'test', ”test” och «testa» är OK).
* Ta bort rader med specialtecken från den här uppsättningen: ¢ ¤ ¥... § © ª ¬® ° + ² µ × ÿ Ø¬¬.
* Expandera siffror i talat formulär, inklusive dollar eller Euro tillsammans.
* Acceptera umlauts endast för en, o och du. Andra kommer att ersättas av ”e” eller tas bort.

Här följer några exempel på Normalisering utförs automatiskt på utskrift:

| Originaltexten | Text efter normalisering |
|---------------|--------------------------|
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |

## <a name="next-steps"></a>Nästa steg

* [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
