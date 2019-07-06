---
title: Speech Synthesis Markup Language (SSML) - Speech Services
titleSuffix: Azure Cognitive Services
description: Använder tal syntes Markup Language för att styra uttal och prosody i text till tal.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8285a76f8cd07863874f9c8e8eebe96f1cb968dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604825"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Tal syntes Markup Language (SSML) är en XML-baserade märkspråk som ger utvecklare möjlighet att ange hur indatatext konverteras till syntetiskt tal med hjälp av tjänsten text till tal. Jämfört med oformaterad text, hjälper SSML utvecklare att finjustera motivation uttal, talar hastighet, volym och flera av text till tal-utdata. Normal skiljetecken, till exempel pausar efter en tid eller med rätt intonation vid en mening som slutar med ett frågetecken hanteras automatiskt.

Taltjänster implementeringen av SSML baseras på World Wide Web Consortium [tal syntes Markup Language Version 1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Kinesiska, japanska och koreanska tecken räknas som två tecken för fakturering. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard neural och anpassade röster

Välj från standard- och neural röster eller skapa din egen anpassade röst unik för din produkt eller varumärke. 75 + standard röster är tillgängliga i mer än 45 språk och nationella inställningar och 5 neural röster är tillgängliga i 4 språk och nationella inställningar. En fullständig lista över språk som stöds, språk och röster (neural och standard), se [språkstöd](language-support.md).

Läs mer om neural, standard och anpassade röster i [text till tal-översikt](text-to-speech.md).

## <a name="supported-ssml-elements"></a>SSML-element som stöds

Varje SSML dokument har skapats med SSML element (eller taggar). De här elementen används för att justera försäljningsargument, prosody, volym och mer. Följande avsnitt beskriver hur varje element används och när ett element är obligatorisk eller valfri.  

> [!IMPORTANT]
> Glöm inte att använda dubbla citattecken runt attributvärden. Standarder för korrekt strukturerad, giltig XML kräver attributvärden omges i dubbla citattecken. Till exempel `<prosody volume="90">` är en korrekt strukturerad, giltigt element, men `<prosody volume=90>` är inte. SSML kanske inte identifiera attributvärden som inte är inom citattecken.

## <a name="create-an-ssml-document"></a>Skapa ett SSML-dokument

`speak` är rotelementet och **krävs** för alla SSML-dokument. Den `speak` elementet innehåller viktig information, till exempel version, språk och markup ordförråd definition.

**Syntax**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| version | Anger versionen av SSML-specifikationen som används för att tolka markup för dokumentet. Den aktuella versionen är 1.0. | Obligatoriskt |
| xml:lang | Anger språket dokumentets rot. Värdet kan innehålla en gemen, två bokstäver språkkod (till exempel **en**), eller språkkod och versaler land/region (till exempel **en-US**). | Obligatoriskt |
| xmlns | Anger URI för till dokumentet som definierar markup vokabulär (elementtyper och attributnamn) SSML-dokumentet. Den aktuella URI: N är https://www.w3.org/2001/10/synthesis. | Obligatoriskt |

## <a name="choose-a-voice-for-text-to-speech"></a>Välj en röst för text till tal

Den `voice` elementet krävs. Den används för att ange röst som används för text till tal.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| name | Identifierar röst som används för text till tal-utdata. En fullständig lista över stöds röster Se [språkstöd](language-support.md#text-to-speech). | Obligatoriskt |

**Exempel**

> [!NOTE]
> Det här exemplet används den `en-US-Jessa24kRUS` röst. En fullständig lista över stöds röster Se [språkstöd](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Använda flera röster

I den `speak` element, du kan ange flera röster för text till tal-utdata. Dessa röster kan finnas i olika språk. För varje röst texten måste inneslutas i en `voice` element.

**Attribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| name | Identifierar röst som används för text till tal-utdata. En fullständig lista över stöds röster Se [språkstöd](language-support.md#text-to-speech). | Obligatoriskt |

**Exempel**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Justera tala

> [!IMPORTANT]
> Den här funktionen fungerar bara med neural röster.

Som standard Syntetiserar tjänsten text till tal text med en neutral samtalsstil för både standard- och neural röster. Du kan justera samtalsstil för cheerfulness, empati eller åsikter med med neural röster den `<mstts:express-as>` element. Det här är ett valfritt element som är unika för Azure Speech Services.

För närvarande stöds engelsktalande style justeringar för dessa neural röster:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Ändringarna tillämpas på nivån mening och style variera med tal. Om ett format inte stöds, returnerar tjänsten tal i standard neutral tala.

**Syntax**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| type | Anger samtalsstil. Tala finns för närvarande röst specifika. | Krävs om justera samtalsstil för en neural röst. Om du använder `mstts:express-as`, typ måste anges. Om ett ogiltigt värde anges, ignoreras det här elementet. |

Använd den här tabellen för att avgöra vilka tala stöds för varje neural röst.

| Röst | Type | Beskrivning |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Representerar en känsla som är positiva och nöjd |
| | type=`empathy` | Representerar en uppfattning om sköta och förstå |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Representerar en formell ton liknar nyheter sändningar |
| | type=`sentiment` | Ger ett vidrör meddelande eller en historia |

**Exempel**

Den här SSML-kodavsnitt illustrerar hur `<mstts:express-as>` elementet används för att ändra samtalsstil till `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Lägg till eller ta bort en radbrytning/pausar

Använd den `break` element att infoga pauser (eller radbrytningar) mellan ord eller förhindra pauser läggs till automatiskt av tjänsten text till tal.

> [!NOTE]
> Använd det här elementet för att åsidosätta standardbeteendet för tal för ett ord eller fraser om syntetiskt tal för ordet eller frasen låter onaturlig. Ange `strength` till `none` att förhindra att en prosodic paus som infogas automatiskt av tjänsten tex-till-tal.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| styrka | Anger den relativa varaktigheten för en paus på något av följande värden:<ul><li>Ingen</li><li>x-weak</li><li>svag</li><li>medel (standard)</li><li>Stark</li><li>x-strong</li></ul> | Valfri |
| time | Anger den absoluta varaktigheten för en paus i sekunder eller millisekunder. Exempel på giltiga värden är 2s och 500 | Valfri |

| styrka | Beskrivning |
|----------|-------------|
| Ingen, eller om inget värde anges | 0 ms |
| x-weak | 250 ms |
| svag | 500 ms |
| medium | 750 ms |
| Stark | 1000 ms |
| x-strong | 1250 ms |


**Exempel**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Ange punkterna och meningar

`p` och `s` element för att ange meningar, stycken och respektive. Om dessa element anger text till tal-tjänsten automatiskt strukturen för SSML-dokument.

Den `p` element kan innehålla text och följande element: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`, och `s`.

Den `s` element kan innehålla text och följande element: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`, och `sub`.

**Syntax**

```XML
<p></p>
<s></s>
```

**Exempel**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Använda fonem för att förbättra uttal

Den `ph` element som används för Fonetiskt uttal i SSML dokument. Den `ph` elementet kan bara innehålla text, inga andra element. Alltid ange läsbara tal som reserv.

Fonetiska alfabet består av telefoner, som består av bokstäver, siffror eller tecken, ibland i kombination. Varje phone beskriver ett unikt ljud tal. Detta skiljer sig från det latinska alfabetet där alla bokstäver kan representera flera talat ljud. Överväg olika uttal bokstaven ”c” i orden ”godis” och ”cease” eller annat uttal av bokstav kombination ”e” i orden ”sak” och ”de”.

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| alphabet | Anger det fonetiska alfabetet ska användas när synthesizing uttal av strängen i den `ph` attribut. Den sträng som anger alfabetet måste anges i gemener. Här följer möjliga bokstäver som du kan ange.<ul><li>IPA &ndash; International fonetiska alfabetet</li><li>SAPI &ndash; Speech API Phone ange</li><li>UPS &ndash; Universal Phone ange</li></ul>Alfabetet gäller enbart för foném i elementet. Mer information finns i [fonetiska alfabetet referens](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Valfri |
| pH | En sträng som innehåller telefoner som anger uttal av ord i den `phoneme` element. Om den angivna strängen innehåller okända telefoner, text till tal (text till tal) tjänsten avvisar hela SSML dokumentet och ger ingen av talutdata som anges i dokumentet. | Krävs om du använder fonem. |

**Exempel**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Justera prosody

Den `prosody` elementet används för att ange ändringar i ton, countour, intervallet, frekvens, varaktighet och volym för text till tal-utdata. Den `prosody` element kan innehålla text och följande element: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`, och `s`.

Eftersom prosodic attributvärden kan variera över en mängd, tolkar taligenkänningens tilldelade värden som ett förslag på vad de faktiska prosodic värdena för rösten ska vara. Text till tal-tjänsten begränsar eller ersätter värden som inte stöds. Exempel på värden som inte stöds är ett avstånd av 1 MHz eller en volym på 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| försäljningsargument | Anger motivation baslinje för texten. Du kan uttrycka motivation som:<ul><li>Ett absolut värde, uttryckt i form av ett tal följt av ”Hz” (Hz). Till exempel 600Hz.</li><li>Ett relativ värde, uttryckt i form av ett tal som föregås av ”+” eller ”-” och följt av ”Hz” eller ”a”, som anger en del ändra motivation. Till exempel: + 80 Hz eller -2st. ”A” Anger ändra enhet är om halvtoner, vilket är hälften av en ton (en halv steg) på standard diatonic skala.</li><li>Ett konstant värde:<ul><li>x-low</li><li>Låg</li><li>medium</li><li>Hög</li><li>x-high</li><li>standard</li></ul></li></ul>. | Valfri |
| profil | Profil stöds inte för neurala röster. Profil representerar ändringar i frekvens för talat innehåll som en matris med mål vid angiven tid positioner i talutdata. Varje mål definieras av uppsättningar med parametern-par. Exempel: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Det första värdet i varje uppsättning parametrar anger platsen för försäljningsargument ändringen som en procentandel av varaktigheten för texten. Det andra värdet anger hur mycket höja eller sänka motivation, med ett relativ värde eller ett uppräkningsvärde för försäljningsargument (se `pitch`). | Valfri |
| Adressintervall  | Ett värde som representerar en uppsättning försäljningsargument efter text. Du kan uttrycka `range` med samma absoluta värden, relativa värden eller uppräkningsvärdena används för att beskriva `pitch`. | Valfri |
| Pris  | Anger engelsktalande frekvensen för texten. Du kan uttrycka `rate` som:<ul><li>Ett relativ värde, uttryckt i form av ett tal som fungerar som en multiplikator på standardvärdet. Till exempel värdet *1* leder till att ingen ändring i hastigheten. Värdet *.5* resulterar i en halving frekvensen. Värdet *3* resulterar i en tripling frekvensen.</li><li>Ett konstant värde:<ul><li>x-slow</li><li>långsam</li><li>medium</li><li>snabb</li><li>x-fast</li><li>standard</li></ul></li></ul> | Valfri |
| duration  | Tidsperioden som ska förflyta när tal syntes (text till tal)-tjänsten läser texten, i sekunder eller millisekunder. Till exempel *2s* eller *1800ms*. | Valfri |
| volym  | Anger volymen för rösten. Du kan uttrycka volymen som:<ul><li>Ett absolut värde, uttryckt i form av ett antal inom intervallet 0,0 till 100,0 från *quietest* till *starkaste*. Till exempel 75. Standardvärdet är 100,0.</li><li>Ett relativ värde, uttryckt i form av ett tal som föregås av ”+” eller ”-” som innehåller ett belopp att ändra volymen. Till exempel + 10 eller-5.5.</li><li>Ett konstant värde:<ul><li>tyst</li><li>x-soft</li><li>Mjuk</li><li>medium</li><li>högt</li><li>x-loud</li><li>standard</li></ul></li></ul> | Valfri |

### <a name="change-speaking-rate"></a>Engelsktalande förändringstakten

Talar hastighet kan tillämpas på standard röster på ett ord eller en mening på servernivå. Talar hastighet kan endast tillämpas på neural röster på nivån mening.

**Exempel**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Ändra volym

Volymändringar kan tillämpas på standard röster på ett ord eller en mening på servernivå. Volymändringar kan endast tillämpas på neural röster på nivån mening.

**Exempel**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Ändra försäljningsargument

Försäljningsargument ändringar kan tillämpas på standard röster på ett ord eller en mening på servernivå. Försäljningsargument ändringar kan endast tillämpas på neural röster på nivån mening.

**Exempel**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Ändra försäljningsargument profil

> [!IMPORTANT]
> Försäljningsargument sluten ändringar stöds inte med neural röster.

**Exempel**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="next-steps"></a>Nästa steg

* [Språkstöd: röster, språk, språk](language-support.md)
