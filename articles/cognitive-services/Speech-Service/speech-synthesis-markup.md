---
title: SSML (Speech syntes Markup Language) – tal tjänsten
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
ms.openlocfilehash: 1bd1882218630aca0707a792d120045c06dea127
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552687"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

SSML (Speech syntes Markup Language) är ett XML-baserat kodspråk som låter utvecklare ange hur indata ska konverteras till syntetiskt tal med hjälp av text till tal-tjänsten. Jämfört med oformaterad text gör SSML att utvecklare kan finjustera färgdjup, uttal, tal frekvens, volym och annat text till tal-utdata. Normal interpunktion, till exempel pausa efter en punkt eller med rätt intonation när en mening slutar med ett frågetecken, hanteras automatiskt.

SSML-implementeringen för tal tjänster är baserad på World Wide Web Consortiumens [tal syntess språk Version 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Kinesiska, japanska och koreanska tecken räknas som två tecken för fakturering. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard, neurala och anpassade röster

Välj mellan standard-och neurala röster eller skapa en egen anpassad röst som är unik för din produkt eller ditt varumärke. 75 + standard röster är tillgängliga på över 45 språk och nationella inställningar och 5 neurala-röster är tillgängliga på 4 språk och nationella inställningar. En fullständig lista över språk som stöds, nationella inställningar och röster (neurala och standard) finns i [språk stöd](language-support.md).

Mer information om standard-, neurala-och anpassade röster finns i [text till tal-översikt](text-to-speech.md).

## <a name="supported-ssml-elements"></a>SSML-element som stöds

Varje SSML-dokument skapas med SSML-element (eller taggar). Dessa element används för att justera bredd, prosody, volym och mycket annat. Följande avsnitt innehåller information om hur varje element används och när ett element krävs eller är valfritt.  

> [!IMPORTANT]
> Glöm inte att använda dubbla citat tecken runt attributvärden. Standarder för välformulerad, giltig XML kräver att attributvärden omges av dubbla citat tecken. Till exempel `<prosody volume="90">` är ett välformulerat, giltigt element, men `<prosody volume=90>` det är inte. SSML kan inte identifiera attributvärden som inte är inom citat tecken.

## <a name="create-an-ssml-document"></a>Skapa ett SSML-dokument

`speak`är rot elementet och **krävs** för alla SSML-dokument. \- `speak` Elementet innehåller viktig information, till exempel version, språk och definition av terminologi för markering.

**Syntax**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| version | Anger den version av SSML-specifikationen som används för att tolka dokument markeringen. Den aktuella versionen är 1,0. | Obligatorisk |
| XML: lang | Anger språket för rot dokumentet. Värdet får innehålla gemener och versaler, två bokstäver (till exempel **en**) eller språk koden och det stora landet/regionen (till exempel **en-US**). | Obligatorisk |
| xmlns | Anger den URI till dokumentet som definierar ord listan (element typerna och attributnamnet) för SSML-dokumentet. Aktuell URI är https://www.w3.org/2001/10/synthesis. | Obligatorisk |

## <a name="choose-a-voice-for-text-to-speech"></a>Välj röst för text till tal

`voice` Elementet måste anges. Den används för att ange vilken röst som används för text till tal.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| name | Identifierar rösten som används för text till tal-utdata. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech). | Obligatorisk |

**Exempel**

> [!NOTE]
> I `en-US-Jessa24kRUS` det här exemplet används rösten. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Använda flera röster

I- `speak` elementet kan du ange flera röster för text till tal-utdata. Dessa röster kan vara på olika språk. För varje röst måste texten vara figursatt i ett `voice` -element.

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| name | Identifierar rösten som används för text till tal-utdata. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech). | Obligatorisk |

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

## <a name="adjust-speaking-styles"></a>Ändra tal format

> [!IMPORTANT]
> Den här funktionen fungerar bara med neurala-röster.

Som standard används text-till-tal-tjänsten för att syntetisera text med ett neutralt tal format för både standard-och neurala-röster. Med neurala-röster kan du ändra tal formatet till Express cheerfulness, empati eller sentiment med `<mstts:express-as>` elementet. Detta är ett valfritt element som är unikt för Azure Speech Services.

För närvarande stöds anpassning av format justeringar för dessa neurala-röster:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Ändringarna tillämpas på menings nivå och format varierar med röst. Om en stil inte stöds returnerar tjänsten tal i standardformat för neutralt tal.

**Syntax**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| type | Anger tal formatet. För närvarande är det bara röst alternativ att tala om format. | Krävs om du justerar tal formatet för en neurala röst. Om du `mstts:express-as`använder måste typen anges. Om ett ogiltigt värde har angetts ignoreras det här elementet. |

Använd den här tabellen för att avgöra vilka tal format som stöds för varje neurala röst.

| Röst | type | Beskrivning |
|-------|------|-------------|
| `en-US-JessaNeural` | typ =`cheerful` | Uttrycker en känslo som är positiv och glad |
| | typ =`empathy` | Uttrycker en uppfattning om Caring och förståelse |
| `zh-CN-XiaoxiaoNeural` | typ =`newscast` | Uttrycker en formell ton som liknar nyhets sändningar |
| | typ =`sentiment` | Förmedlar ett touch-meddelande eller en berättelse |

**Exempel**

Det här SSML-kodfragmentet illustrerar hur `<mstts:express-as>` elementet används för att ändra tal formatet till. `cheerful`

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Lägg till eller ta bort en paus/pausa

`break` Använd elementet för att infoga pauser (eller brytningar) mellan ord, eller förhindra att pauser automatiskt läggs till av tjänsten text till tal.

> [!NOTE]
> Använd det här elementet om du vill åsidosätta standard beteendet för text till tal (TTS) för ett ord eller en fras om det syntetiska talet för ordet eller frasen låter unnaturlig. Ställ `strength` in `none` på för att förhindra en prosodic rast, som infogas automatiskt av text till tal-tjänsten.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| styrka | Anger den relativa varaktigheten för en paus med något av följande värden:<ul><li>inga</li><li>x-svaga</li><li>låg</li><li>medel (standard)</li><li>stark</li><li>x-strong</li></ul> | Valfritt |
| time | Anger den absoluta varaktigheten för en paus på några sekunder eller millisekunder. Exempel på giltiga värden är 2s och 500 | Valfritt |

| Styrka | Beskrivning |
|----------|-------------|
| Ingen, eller om inget värde anges | 0 MS |
| x-svaga | 250 MS |
| låg | 500 ms |
| medel | 750 ms |
| stark | 1000 MS |
| x-strong | 1250 MS |


**Exempel**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Ange stycken och meningar

`p`och `s` element används för att ange stycken respektive meningar. Om dessa element saknas bestämmer text till tal-tjänsten automatiskt strukturen för SSML-dokumentet.

`prosody` `break` `phoneme` `audio` Elementetkan`say-as`innehålla text och följande element: ,,`mstts:express-as`,,,, och .`s` `sub` `p`

`phoneme` `break` `prosody` Elementetkan`mstts:express-as`innehålla text och följande element: `audio`, ,`say-as`,,, och `sub`. `s`

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

## <a name="use-phonemes-to-improve-pronunciation"></a>Använd fonem för att förbättra uttal

`ph` Elementet används för fonetiskt uttal i SSML-dokument. `ph` Elementet får bara innehålla text, inga andra element. Tillhandahålla alltid läsliga tal som reserv.

Fonetiska alfabet består av telefoner, som består av bokstäver, siffror eller tecken, ibland i kombination. Varje telefon beskriver ett unikt ljud av tal. Detta är i motsats till det latinska alfabetet, där en bokstav kan representera flera talade ljud. Överväg de olika uttalna av bokstaven "c" i orden "Candy" och "upphör", eller de olika uttal av bokstavs kombinationen "th" i orden "sak" och "de".

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| ordning | Anger det fonetiska alfabetet som ska användas vid syntetiskt uttal av strängen i `ph` attributet. Strängen som anger alfabetet måste anges med små bokstäver. Följande är de möjliga alfabet som du kan ange.<ul><li>IPA &ndash; internationellt fonetiskt alfabet</li><li>Speech API &ndash; telefon uppsättning för SAPI</li><li>UPS &ndash; Universal Phone-uppsättning</li></ul>Alfabetet gäller endast för fonem i elementet. Mer information finns i [fonetiska alfabet referenser](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Valfritt |
| pH | En sträng som innehåller telefoner som anger uttal av ordet i `phoneme` elementet. Om den angivna strängen innehåller okända telefoner avvisar tjänsten text till tal (TTS) hela SSML-dokumentet och genererar ingen av tal utmatningen som anges i dokumentet. | Krävs om du använder fonem. |

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

`prosody` Elementet används för att ange ändringar av bredd, countour, intervall, hastighet, varaktighet och volym för text till tal-utdata. `phoneme` `break` `p` `audio` Elementetkan`prosody`innehålla text och följande element: ,,`sub`,,,, och .`s` `say-as` `prosody`

Eftersom prosodic-attributvärden kan variera över ett brett intervall, tolkar tal igenkännings verktyget de tilldelade värdena som ett förslag på vad de faktiska prosodic-värdena för den valda rösten ska vara. Text till tal-tjänsten begränsar eller byter ut värden som inte stöds. Exempel på värden som inte stöds är ett färgdjup på 1 MHz eller en volym på 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| röst | Anger textens bas linje bredd. Du kan uttrycka bredden som:<ul><li>Ett absolut värde, uttryckt som ett tal följt av "Hz" (Hertz). Till exempel 600Hz.</li><li>Ett relativt värde, uttryckt som ett tal som föregås av "+" eller "-" och följt av "Hz" eller "St", som anger ett belopp för att ändra bredden. Till exempel: + 80Hz eller-2st. "St" anger att ändrings enheten är semitone, som är hälften av en ton (ett halv steg) i standard skalan för diatonic.</li><li>Ett konstant värde:<ul><li>x-låg</li><li>börjar</li><li>medel</li><li>högt</li><li>x – hög</li><li>standard</li></ul></li></ul>. | Valfritt |
| höjd | Kontur stöds inte för neurala-röster. Kontur representerar ändringar i bredden för tal innehåll som en matris med mål vid angivna tids positioner i tal utmatningen. Varje mål definieras av uppsättningar av parameter par. Exempel: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Det första värdet i varje parameter uppsättning anger platsen för bredd ändringen som en procent andel av längden på texten. Det andra värdet anger hur mycket du vill höja eller sänka avståndet med ett relativt värde eller ett uppräknings värde för färgdjup (se `pitch`). | Valfritt |
| intervall  | Ett värde som representerar text områdets avstånd. Du kan uttrycka `range` använda samma absoluta värden, relativa värden eller uppräknings värden som används för att `pitch`beskriva. | Valfritt |
| antal  | Anger textens tal hastighet. Du kan uttrycka `rate` som:<ul><li>Ett relativt värde, uttryckt som ett tal som fungerar som en multiplikator för standardvärdet. Värdet *1* resulterar till exempel i ingen ändring av hastigheten. Värdet *0,5* resulterar i en halving av hastigheten. Värdet *3* resulterar i en rese frekvens.</li><li>Ett konstant värde:<ul><li>x – långsam</li><li>långsam</li><li>medel</li><li>snabbväxande</li><li>x-fast</li><li>standard</li></ul></li></ul> | Valfritt |
| duration  | Tids perioden som ska förflyta när tal syntes tjänsten läser texten, i sekunder eller millisekunder. Till exempel *2s* eller *1800ms*. | Valfritt |
| volym  | Anger volym nivån för tal rösten. Du kan uttrycka volymen som:<ul><li>Ett absolut värde, uttryckt som ett tal i intervallet 0,0 till 100,0, från *tyst* till *högsta*. Till exempel 75. Standardvärdet är 100,0.</li><li>Ett relativt värde, uttryckt som ett tal som föregås av "+" eller "-" som anger ett belopp för att ändra volymen. Till exempel + 10 eller-5,5.</li><li>Ett konstant värde:<ul><li>trafiken</li><li>x-soft</li><li>programvaru</li><li>medel</li><li>starka</li><li>x-högt</li><li>standard</li></ul></li></ul> | Valfritt |

### <a name="change-speaking-rate"></a>Engelsktalande förändringstakten

Tal frekvensen kan tillämpas på standard-röster på ord-eller menings nivå. Tal frekvensen kan endast tillämpas på neurala-röster på menings nivå.

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

Volym ändringar kan tillämpas på standard-röster på ord-eller menings nivå. Volym ändringar kan bara tillämpas på neurala-röster på menings nivå.

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

Du kan ändra bredden på standard-röster på ord-eller menings nivå. Förändringar av förändringar kan bara tillämpas på neurala-röster på menings nivå.

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
> Höjd kon tur ändringar stöds inte med neurala röster.

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

## <a name="add-recorded-audio"></a>Lägg till inspelat ljud

`audio`är ett valfritt element som gör att du kan infoga MP3-ljud i ett SSML-dokument. Bröd texten i ljud elementet kan innehålla oformaterad text eller SSML som talas om ljud filen inte är tillgänglig eller kan spelas upp. `p` `s` `audio` `break` `phoneme` Dessutomkanelementetinnehålla`prosody`text och följande element:,, `sub`,,, ,och.`say-as` `audio`

Alla ljud som ingår i SSML-dokumentet måste uppfylla följande krav:

* MP3-filen måste vara värd för en HTTPS-slutpunkt som är tillgänglig för Internet. HTTPS krävs och den domän som är värd för MP3-filen måste presentera ett giltigt, betrott SSL-certifikat.
* MP3-filen måste vara en giltig MP3-fil (MPEG v2).
* Bit hastigheten måste vara 48 kbit/s.
* Samplings frekvensen måste vara 16000 Hz.
* Den sammanlagda tiden för alla text-och ljudfiler i ett enskilt svar får inte överstiga 90 (90) sekunder.
* MP3-filen får inte innehålla kundspecifik eller annan känslig information.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| src | Anger ljud filens plats/URL. | Krävs om du använder ljud elementet i ditt SSML-dokument. |

**Exempel**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>Lägg till bakgrunds ljud

Med `mstts:backgroundaudio` -elementet kan du lägga till bakgrunds ljud till dina SSML-dokument (eller blanda en ljudfil med text till tal). Med `mstts:backgroundaudio` kan du repetera en ljudfil i bakgrunden, tona in i början av text till tal och tona ut i slutet av text till tal.

Om bakgrunds ljudet som tillhandahålls är kortare än text-till-tal-eller övertoningen, kommer den att upprepas. Om det är längre än text till tal, stoppas det när toningen är färdig.

Endast en bakgrunds ljud fil tillåts per SSML-dokument. Du kan dock blanda `audio` Taggar `voice` i elementet för att lägga till ytterligare ljud till ditt SSML-dokument.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Dokumentattribut**

| Attribut | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| src | Anger plats/URL för bakgrunds ljud filen. | Krävs om du använder bakgrunds ljud i ditt SSML-dokument. |
| volym | Anger bakgrunds ljud filens volym. **Godkända värden**: `0` till `100` inklusiv. Standardvärdet är `1`. | Valfritt |
| tona in | Anger varaktigheten för bakgrunds ljudet tonar in. **Godkända värden**: `0` till `10000` inklusiv.  | Valfritt |
| tona bort | Anger hur länge bakgrunds ljudet tonas ut. **Godkända värden**: `0` till `10000` inklusiv.  | Valfritt |

**Exempel**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Nästa steg

* [Språk stöd: röster, nationella inställningar, språk](language-support.md)
