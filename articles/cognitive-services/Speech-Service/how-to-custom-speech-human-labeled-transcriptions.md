---
title: Riktlinjer för humanmärkta transkriptioner - Taltjänst
titleSuffix: Azure Cognitive Services
description: Om du vill förbättra taligenkänningsnoggrannheten, till exempel när ord tas bort eller ersätts felaktigt, kan du använda avskrifter med mänskligt märkta transkriptioner tillsammans med dina ljuddata. Mänskligt märkta transkriptioner är ord för ord, ordagrant transkriptioner av en ljudfil.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806070"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Hur man skapar mänskligt märkta transkriptioner

Om du vill förbättra igenkänningsprecisionen, särskilt problem som uppstår när ord tas bort eller ersätts felaktigt, vill du använda avskrifter som betecknas som livsmedel tillsammans med dina ljuddata. Vad är mänskligt märkta transkriptioner? Det är lätt, de är ord för ord, ordagrant transkriptioner av en ljudfil.

Ett stort urval av transkriptionsdata krävs för att förbättra igenkänningen, föreslår vi att man tillhandahåller mellan 10 och 1 000 timmars transkriptionsdata. På den här sidan granskar vi riktlinjer som är utformade för att hjälpa dig att skapa transkriptioner av hög kvalitet. Denna guide är uppdelad efter språk, med sektioner för amerikansk engelska, mandarin kinesiska och tyska.

## <a name="us-english-en-us"></a>Amerikansk engelska (en-US)

Mänskligt märkta transkriptioner för engelskt ljud måste tillhandahållas som oformaterad text, endast med ASCII-tecken. Undvik användning av latin-1- eller Unicode-skiljetecken. Dessa tecken läggs ofta av misstag till när text kopieras från ett ordbehandlingsprogram eller skrapar data från webbsidor. Om dessa tecken finns, se till att uppdatera dem med lämplig ASCII substitution.

Några exempel:

| Tecken för att undvika | Ersättning | Anteckningar |
| ------------------- | ------------ | ----- |
| "Hej världen" | "Hej världen" | De inledande och avslutande citattecknen har ersatts med lämpliga ASCII-tecken. |
| Johns dag | Johns dag | Apostrofen har ersatts med lämpligt ASCII-tecken. |
| det var bra – nej, det var toppen! | Det var bra - nej, det var bra! | Em-strecket ersattes med två bindestreck. |

### <a name="text-normalization-for-us-english"></a>Textnormalisering för amerikansk engelska

Textnormalisering är omvandlingen av ord till ett konsekvent format som används när en modell tränas. Vissa normaliseringsregler tillämpas på text automatiskt, men vi rekommenderar att du använder dessa riktlinjer när du förbereder dina humanmärkta transkriptionsdata:

- Skriv ut förkortningar i ord.
- Skriv ut numeriska strängar som inte är standard i ord (t.ex. redovisningstermer).
- Icke-alfabetiska tecken eller blandade alfanumeriska tecken bör transkriberas som uttalade.
- Förkortningar som uttalas som ord bör inte redigeras (till exempel "radar", "laser", "RAM" eller "NATO").
- Skriv ut förkortningar som uttalas som separata bokstäver med varje bokstav avgränsad med ett blanksteg.

Här är några exempel på normalisering som du bör utföra på transkriptionen:

| Originaltext               | Text efter normalisering              |
| --------------------------- | ------------------------------------- |
| Dr Bruce Banner            | Läkare Bruce Banner                   |
| James förbindelse, 007             | James Bond, dubbel oh sju           |
| Ke $ha (Ke$ha)                       | Kesha                                 |
| Hur länge är 2x4         | Hur länge är de två med fyra           |
| Mötet går från 13:00 till 15:00 | Mötet går från en till tre pm |
| Min blodgrupp är O+         | Min blodgrupp är O-positiv           |
| Vatten är H20                | Vatten är H 2 O                        |
| Spela OU812 av Van Halen     | Spela O U 8 1 2 av Van Halen           |
| UTF-8 med BOM              | U T F 8 med strukturlista                      |

Följande normaliseringsregler tillämpas automatiskt på transkriptioner:

- Använd gemener.
- Ta bort alla interpunktion utom apostrofer i ord.
- Expandera tal till ord/talat formulär, till exempel dollarbelopp.

Här är några exempel på normalisering som automatiskt utförs på transkriptionen:

| Originaltext                          | Text efter normalisering          |
| -------------------------------------- | --------------------------------- |
| "Heliga ko!" sade Batman.               | helig ko sa Batman              |
| "Vad?" Sa Batmans medhjälpare, Robin. | vad sa Batmans sidekick robin |
| Hämta dem!                            | gå och hämta dem                         |
| Jag är dubbel-ledad                     | Jag är dubbel ledad                |
| 104 Elm gatan                         | en oh fyra Elm gata            |
| Ställ in 102,7                          | ställa in en oh två punkt sju    |
| Pi är ca 3,14                       | pi är ungefär tre punkt ett fyra  |
| Det \$kostar 3,14                        | det kostar tre fjorton           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin kinesiska (zh-CN)

Human-märkta transkriptioner för mandarin kinesiska ljud måste UTF-8 kodas med en byte-order markör. Undvik att använda halvbreddstecken. Dessa tecken kan inkluderas oavsiktligt när du förbereder data i ett ordbehandlingsprogram eller skrapar data från webbsidor. Om dessa tecken finns, se till att uppdatera dem med lämplig full bredd ersättning.

Några exempel:

| Tecken för att undvika | Ersättning   | Anteckningar |
| ------------------- | -------------- | ----- |
| "中ン" | "中ン" | De inledande och avslutande citattecknen har ersatts med lämpliga tecken. |
| 中ンンンン? | 中ンンンン?| Frågetecknet har ersatts med lämplig karaktär. |

### <a name="text-normalization-for-mandarin-chinese"></a>Textnormalisering för mandarin kinesiska

Textnormalisering är omvandlingen av ord till ett konsekvent format som används när en modell tränas. Vissa normaliseringsregler tillämpas på text automatiskt, men vi rekommenderar att du använder dessa riktlinjer när du förbereder dina humanmärkta transkriptionsdata:

- Skriv ut förkortningar i ord.
- Skriv ut numeriska strängar i talad form.

Här är några exempel på normalisering som du bör utföra på transkriptionen:

| Originaltext | Text efter normalisering |
| ------------- | ------------------------ |
| 21 | 中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン |
| 3 中ン 504 | 三号 楼 五 零 四 |

Följande normaliseringsregler tillämpas automatiskt på transkriptioner:

- Ta bort alla skiljetecken
- Expandera tal till talat formulär
- Konvertera helbreddsbokstäver till halvbreddsbokstäver
- Använda versaler för alla engelska ord

Här är några exempel på normalisering som automatiskt utförs på transkriptionen:

| Originaltext | Text efter normalisering |
| ------------- | ------------------------ |
| 3.1415 | Det är inte så att jag inte kommer att |
| 3,5 . | Det är inte så att jag |
| w f y z (f y z) | W F Y Z |
| 1992 中 8 中 8 中 | Det är inte så mycket som |
| 中ンンン? | 你 吃饭 了 吗 |
| 5:00 中ンン | 下午 五点 的 航班 |
| 21 21 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Tyska (de-DE) och andra språk

Human-märkta transkriptioner för tyska ljud (och andra icke-engelska eller mandarin kinesiska språk) måste VARA UTF-8 kodas med en byte-order markör. En mänsklig märkt utskrift bör tillhandahållas för varje ljudfil.

### <a name="text-normalization-for-german"></a>Textnormalisering för tyska

Textnormalisering är omvandlingen av ord till ett konsekvent format som används när en modell tränas. Vissa normaliseringsregler tillämpas på text automatiskt, men vi rekommenderar att du använder dessa riktlinjer när du förbereder dina humanmärkta transkriptionsdata:

- Skriv decimaler som "" och inte ".".
- Skriv tidsavgränsare som ":" och inte "." (till exempel: 12:00 Uhr).
- Förkortningar som "ca". ersätts inte. Vi rekommenderar att du använder hela det talade formuläret.
- De fyra huvudsakliga matematiska operatorerna (+, -, \*och /) tas bort. Vi rekommenderar att ersätta dem med den skriftliga formen: "plus", "minus", "mal" och "geteilt".
- Jämförelseoperatorer tas bort (=, < och >). Vi rekommenderar att ersätta dem med "gleich", "kleiner als" och "grösser als."
- Skriv bråk, till exempel 3/4, i skriftlig form (till exempel: "drei viertel" istället för 3/4).
- Ersätt symbolen "€" med den skriftliga formen "Euro".

Här är några exempel på normalisering som du bör utföra på transkriptionen:

| Originaltext    | Text efter normalisering av användare | Text efter systemnormalisering       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12,23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

Följande normaliseringsregler tillämpas automatiskt på transkriptioner:

- Använd gemener för all text.
- Ta bort alla skiljetecken, inklusive olika typer av citattecken ("test", "test", "test" och «test» är OK).
- Kasta rader med några specialtecken från denna uppsättning: ¢ ¥ ¦ § © ª ¬ ® ° ± ² μ × ÿ Ø ¬¬.
- Expandera tal till talad form, inklusive belopp i dollar eller euro.
- Acceptera omljud bara för a, o, och du. Andra kommer att ersättas av "th" eller kasseras.

Här är några exempel på normalisering som automatiskt utförs på transkriptionen:

| Originaltext    | Text efter normalisering |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| Jag vet inte vad du är med om.     | eine frage (eten)               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Efterföljande moment

- [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
- [Granska dina data](how-to-custom-speech-inspect-data.md)
- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
- [Träna din modell](how-to-custom-speech-train-model.md)
- [Distribuera din modell](how-to-custom-speech-deploy-model.md)
