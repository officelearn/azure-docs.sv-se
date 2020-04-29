---
title: SSML (Speech syntes Markup Language) – tal tjänsten
titleSuffix: Azure Cognitive Services
description: Använd tal syntes märknings språk för att kontrol lera uttal och prosody i text-till-tal.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: dc11d26c73c52b5e6c4d8e05cc27dd6ebce0c5d8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399825"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Förbättra syntesen med SSML (Speech syntes Markup Language)

SSML (Speech syntes Markup Language) är ett XML-baserat kodspråk som låter utvecklare ange hur indata ska konverteras till syntetiskt tal med hjälp av text till tal-tjänsten. Jämfört med oformaterad text gör SSML att utvecklare kan finjustera färgdjup, uttal, tal frekvens, volym och annat text till tal-utdata. Normal interpunktion, till exempel pausa efter en punkt eller med rätt intonation när en mening slutar med ett frågetecken, hanteras automatiskt.

Tal tjänst implementeringen av SSML baseras på World Wide Web Consortiumens [tal syntess språk Version 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Kinesiska, japanska och koreanska tecken räknas som två tecken för fakturering. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard, neurala och anpassade röster

Välj mellan standard-och neurala röster eller skapa en egen anpassad röst som är unik för din produkt eller ditt varumärke. 75 + standard röster är tillgängliga på över 45 språk och nationella inställningar, och 5 neurala-röster är tillgängliga på fyra språk och nationella inställningar. En fullständig lista över språk som stöds, nationella inställningar och röster (neurala och standard) finns i [språk stöd](language-support.md).

Mer information om standard-, neurala-och anpassade röster finns i [text till tal-översikt](text-to-speech.md).

## <a name="special-characters"></a>Specialtecken

När du använder SSML bör du tänka på att specialtecken, till exempel citat tecken, apostrofer och hakparenteser måste föregås. Mer information finns i [Extensible Markup Language (XML) 1,0: bilaga D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>SSML-element som stöds

Varje SSML-dokument skapas med SSML-element (eller taggar). Dessa element används för att justera bredd, prosody, volym och mycket annat. Följande avsnitt innehåller information om hur varje element används och när ett element krävs eller är valfritt.  

> [!IMPORTANT]
> Glöm inte att använda dubbla citat tecken runt attributvärden. Standarder för välformulerad, giltig XML kräver att attributvärden omges av dubbla citat tecken. Till exempel `<prosody volume="90">` är ett välformulerat, giltigt element, men `<prosody volume=90>` det är inte. SSML kan inte identifiera attributvärden som inte är inom citat tecken.

## <a name="create-an-ssml-document"></a>Skapa ett SSML-dokument

`speak`är rot elementet och **krävs** för alla SSML-dokument. - `speak` Elementet innehåller viktig information, till exempel version, språk och definition av terminologi för markering.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `version` | Anger den version av SSML-specifikationen som används för att tolka dokument markeringen. Den aktuella versionen är 1,0. | Krävs |
| `xml:lang` | Anger språket för rot dokumentet. Värdet får innehålla gemener, gemener och versaler (t. ex. `en`) eller språk koden och land/region (t. ex. `en-US`). | Krävs |
| `xmlns` | Anger den URI till dokumentet som definierar ord listan (element typerna och attributnamnet) för SSML-dokumentet. Aktuell URI är http://www.w3.org/2001/10/synthesis. | Krävs |

## <a name="choose-a-voice-for-text-to-speech"></a>Välj röst för text till tal

`voice` Elementet måste anges. Den används för att ange vilken röst som används för text till tal.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `name` | Identifierar rösten som används för text till tal-utdata. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech). | Krävs |

**Exempel**

> [!NOTE]
> I `en-US-AriaRUS` det här exemplet används rösten. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Använd flera röster

I- `speak` elementet kan du ange flera röster för text till tal-utdata. Dessa röster kan vara på olika språk. För varje röst måste texten vara figursatt i ett `voice` -element. 

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `name` | Identifierar rösten som används för text till tal-utdata. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech). | Krävs |

> [!IMPORTANT]
> Flera röster är inkompatibla med ord gränsens funktion. Ord gränsen måste inaktive ras för att flera röster ska kunna användas.

### <a name="disable-word-boundary"></a>Inaktivera ord gränser

Beroende på språket Speech SDK anger du `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` egenskapen till `false` på en instans av `SpeechConfig` objektet.

# <a name="c"></a>[C #](#tab/csharp)

Mer information finns i <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Mer information finns i <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Mer information finns i <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Mer information finns i <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Mer information finns i <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Mer information finns i <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Mer information finns i <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ändra tal format

> [!IMPORTANT]
> Justeringen av tal format fungerar bara med neurala-röster.

Som standard används text-till-tal-tjänsten för att syntetisera text med ett neutralt tal format för både standard-och neurala-röster. Med neurala-röster kan du ändra tal formatet till Express cheerfulness, empati eller sentiment med `<mstts:express-as>` elementet. Detta är ett valfritt element som är unikt för tal tjänsten.

För närvarande stöds anpassning av format justeringar för dessa neurala-röster:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`

Ändringarna tillämpas på menings nivå och format varierar med röst. Om en stil inte stöds returnerar tjänsten tal i standardformat för neutralt tal.

**Syntax**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `style` | Anger tal formatet. För närvarande är det röst alternativ att tala om format. | Krävs om du justerar tal formatet för en neurala röst. Om du `mstts:express-as`använder, måste format tillhandahållas. Om ett ogiltigt värde har angetts ignoreras det här elementet. |

Använd den här tabellen för att avgöra vilka tal format som stöds för varje neurala röst.

| Röst                   | Format                     | Beskrivning                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast"`        | Uttrycker en formell och professionell ton för nya berättarröstinspelningar |
|                         | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kund support  |
|                         | `style="chat"`            | Uttrycker en vardaglig och avslappnad ton                         |
|                         | `style="cheerful"`        | Uttrycker en positiv och trevlig ton                         |
|                         | `style="empathetic"`      | Uttrycker en uppfattning om Caring och förståelse               |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Uttrycker en formell och professionell ton för nya berättarröstinspelningar |
|                         | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kund support  |
|                         | `style="assistant"`       | Uttrycker en varm och avslappnad ton för digitala assistenter    |
|                         | `style="lyrical"`         | Uttrycker känslor i ett Melodic-och sentimental-sätt         |

**Exempel**

Det här SSML-kodfragmentet illustrerar hur `<mstts:express-as>` elementet används för att ändra tal formatet `cheerful`till.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Lägg till eller ta bort en paus/pausa

Använd `break` elementet för att infoga pauser (eller brytningar) mellan ord, eller förhindra att pauser automatiskt läggs till av tjänsten text till tal.

> [!NOTE]
> Använd det här elementet om du vill åsidosätta standard beteendet för text till tal (TTS) för ett ord eller en fras om det syntetiska talet för ordet eller frasen låter unnaturlig. Ställ `strength` in `none` på för att förhindra en prosodic rast, som infogas automatiskt av text till tal-tjänsten.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `strength` | Anger den relativa varaktigheten för en paus med något av följande värden:<ul><li>ingen</li><li>x-svaga</li><li>svaga</li><li>medel (standard)</li><li>kraftfull</li><li>x – stark</li></ul> | Valfri |
| `time` | Anger den absoluta varaktigheten för en paus på några sekunder eller millisekunder. Exempel på giltiga värden är `2s` och`500` | Valfri |

| Styrka                      | Beskrivning |
|-------------------------------|-------------|
| Ingen, eller om inget värde anges | 0 MS        |
| x-svaga                        | 250 MS      |
| svaga                          | 500 ms      |
| medel                        | 750 ms      |
| kraftfull                        | 1000 MS     |
| x – stark                      | 1250 MS     |

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Ange stycken och meningar

`p`och `s` element används för att ange stycken respektive meningar. Om dessa element saknas bestämmer text till tal-tjänsten automatiskt strukturen för SSML-dokumentet.

`p` `audio`Elementet kan innehålla text och följande element:, `break`, `phoneme` `prosody` `say-as` `sub` `mstts:express-as`,,,, och. `s`

`s` Elementet kan innehålla text och följande element `audio`:, `break`, `phoneme` `prosody` `say-as` `mstts:express-as`,,, och. `sub`

**Syntax**

```XML
<p></p>
<s></s>
```

**Exempel**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
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

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `alphabet` | Anger det fonetiska alfabetet som ska användas vid syntetiskt uttal av strängen i `ph` attributet. Strängen som anger alfabetet måste anges med små bokstäver. Följande är de möjliga alfabet som du kan ange.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Internationellt fonetiskt <span class="docon docon-navigate-external x-hidden-focus"></span> alfabet</a></li><li>`sapi`&ndash; [Fonetiskt alfabet i Speech service](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Universal Phone-uppsättning</li></ul><br>Alfabetet gäller endast `phoneme` i-elementet.. | Valfri |
| `ph` | En sträng som innehåller telefoner som anger uttal av ordet i `phoneme` elementet. Om den angivna strängen innehåller okända telefoner avvisar tjänsten text till tal (TTS) hela SSML-dokumentet och genererar ingen av tal utmatningen som anges i dokumentet. | Krävs om du använder fonem. |

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Använd anpassat lexikon för att förbättra uttal

Ibland kan inte TTS uttala ett ord, till exempel ett företags-eller främmande namn. Utvecklare kan definiera läsningen av dessa entiteter i SSML `phoneme` med `sub` och tagga eller definiera läsningen av flera entiteter genom att referera till en anpassad lexikon `lexicon` fil med hjälp av taggen.

**Syntax**

```XML
<lexicon uri="string"/>
```

**Attribut**

| Attribut | Beskrivning                               | Obligatorisk/valfri |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Adressen för det externa PLS-dokumentet. | Krävs.           |

**Användning**

Steg 1: definiera anpassat lexikon 

Du kan definiera hur entiteter ska läsas av en lista med anpassade lexikon objekt, lagrade som en XML-eller pls-fil.

**Exempel**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Varje `lexeme` element är ett lexikon objekt. `grapheme`innehåller text som beskriver orthograph för `lexeme`. Utskicks form kan anges `alias`som. Det går att ange telefon sträng `phoneme` i elementet.

`lexicon` Elementet innehåller minst ett `lexeme` -element. Varje `lexeme` -element innehåller minst ett `grapheme` element och ett eller flera `grapheme`element `alais`, och `phoneme` . `grapheme` Elementet innehåller text som beskriver <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">Orthography. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> `alias` Elementen används för att ange uttal av en akronym eller en förkortad term. `phoneme` Elementet innehåller text som beskriver hur `lexeme` uttalas.

Mer information om den anpassade lexikon filen finns i avsnittet uttal av ord listan [(pls) Version 1,0](https://www.w3.org/TR/pronunciation-lexicon/) på W3C-webbplatsen.

Steg 2: Ladda upp den anpassade lexikon filen som skapades i steg 1 online kan du lagra den var som helst, och vi föreslår att du lagrar den i Microsoft Azure, till exempel [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Steg 3: referera till den anpassade lexikon filen i SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" kommer att läsas som "på väg". "Oskadligi" kommer att läsas med den tillhandahållna IPA "bɛ ˈ ni ː Nji".  

**Begränsning**
- Fil storlek: den maximala storleks gränsen för den anpassade fil storleken är 100 KB, om den överskrider den här storleken kommer syntes förfrågan att Miss förväntas.
- Uppdatering av lexikon-cache: anpassat lexikon cachelagras med URI som nyckel på TTS-tjänst när den läses in första gången. Det går inte att läsa in ett lexikon med samma URI inom 15 minuter, så den anpassade lexikon ändringen måste vänta högst 15 minuter innan den börjar gälla.

**Fonetiska uppsättningar för tal tjänst**

I exemplet ovan använder vi det internationella fonetiska alfabetet, även kallat IPA telefon uppsättning. Vi rekommenderar att utvecklare använder IPA, eftersom det är den internationella standarden. Med tanke på att IPA inte är lätt att komma ihåg definierar tal tjänsten en fonetisk uppsättning för sju språk (`en-US`, `fr-FR`, `de-DE` `es-ES` `ja-JP` `zh-CN`,,, och `zh-TW`).

Du kan använda `sapi` as-Vale för `alphabet` attributet med anpassade lexikon som visas nedan:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Mer information om den detaljerade röst tjänsten fonetiskt alfabet finns i [röst tjänstens fonetiska uppsättningar](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Justera prosody

`prosody` Elementet används för att ange ändringar av bredd, profil, intervall, hastighet, varaktighet och volym för text till tal-utdata. `prosody` `audio`Elementet kan innehålla text och följande element:, `break`, `p` `phoneme` `prosody` `say-as` `sub`,,,, och. `s`

Eftersom prosodic-attributvärden kan variera över ett brett intervall, tolkar tal igenkännings verktyget de tilldelade värdena som ett förslag på vad de faktiska prosodic-värdena för den valda rösten ska vara. Text till tal-tjänsten begränsar eller byter ut värden som inte stöds. Exempel på värden som inte stöds är ett färgdjup på 1 MHz eller en volym på 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `pitch` | Anger textens bas linje bredd. Du kan uttrycka bredden som:<ul><li>Ett absolut värde, uttryckt som ett tal följt av "Hz" (Hertz). Till exempel 600 Hz.</li><li>Ett relativt värde, uttryckt som ett tal som föregås av "+" eller "-" och följt av "Hz" eller "St", som anger ett belopp för att ändra bredden. Till exempel: + 80 Hz eller-2st. "St" anger att ändrings enheten är semitone, som är hälften av en ton (ett halv steg) i standard skalan för diatonic.</li><li>Ett konstant värde:<ul><li>x-låg</li><li>börjar</li><li>medel</li><li>hög</li><li>x – hög</li><li>standard</li></ul></li></ul>. | Valfri |
| `contour` | Kontur stöds inte för neurala-röster. Kontur representerar ändringar i färgdjup. Dessa ändringar visas som en matris med mål vid angivna tids positioner i tal utmatningen. Varje mål definieras av uppsättningar av parameter par. Ett exempel: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Det första värdet i varje parameter uppsättning anger platsen för bredd ändringen som en procent andel av längden på texten. Det andra värdet anger hur mycket du vill höja eller sänka avståndet med ett relativt värde eller ett uppräknings värde för färgdjup (se `pitch`). | Valfri |
| `range` | Ett värde som representerar text områdets avstånd. Du kan uttrycka `range` använda samma absoluta värden, relativa värden eller uppräknings värden som används för att `pitch`beskriva. | Valfri |
| `rate` | Anger textens tal hastighet. Du kan uttrycka `rate` som:<ul><li>Ett relativt värde, uttryckt som ett tal som fungerar som en multiplikator för standardvärdet. Värdet *1* resulterar till exempel i ingen ändring av hastigheten. Värdet *0,5* resulterar i en halving av hastigheten. Värdet *3* resulterar i en rese frekvens.</li><li>Ett konstant värde:<ul><li>x – långsam</li><li>långsam</li><li>medel</li><li>snabb</li><li>x-fast</li><li>standard</li></ul></li></ul> | Valfri |
| `duration` | Tids perioden som ska förflyta när tal syntes tjänsten läser texten, i sekunder eller millisekunder. Till exempel *2s* eller *1800ms*. | Valfri |
| `volume` | Anger volym nivån för tal rösten. Du kan uttrycka volymen som:<ul><li>Ett absolut värde, uttryckt som ett tal i intervallet 0,0 till 100,0, från *tyst* till *högsta*. Till exempel 75. Standardvärdet är 100,0.</li><li>Ett relativt värde, uttryckt som ett tal som föregås av "+" eller "-" som anger ett belopp för att ändra volymen. Till exempel + 10 eller-5,5.</li><li>Ett konstant värde:<ul><li>trafiken</li><li>x-Soft</li><li>programvaru</li><li>medel</li><li>starka</li><li>x-högt</li><li>standard</li></ul></li></ul> | Valfri |

### <a name="change-speaking-rate"></a>Ändra tal hastighet

Tal frekvensen kan tillämpas på standard-röster på ord-eller menings nivå. Tal frekvensen kan endast tillämpas på neurala-röster på menings nivå.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Ändra avstånd

Du kan ändra bredden på standard-röster på ord-eller menings nivå. Förändringar av förändringar kan bara tillämpas på neurala-röster på menings nivå.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Ändra bredd för kontur

> [!IMPORTANT]
> Höjd kon tur ändringar stöds inte med neurala röster.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>Säg som-element

`say-as`är ett valfritt element som anger innehålls typen (t. ex. antal eller datum) för elementets text. Detta ger vägledning till tal syntes motorn om hur du uttalar texten.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `interpret-as` | Anger innehålls typen för elementets text. En lista med typer finns i tabellen nedan. | Krävs |
| `format` | Innehåller ytterligare information om den exakta formateringen av elementets text för innehålls typer som kan ha tvetydiga format. SSML definierar format för innehålls typer som använder dem (se tabellen nedan). | Valfri |
| `detail` | Anger detalj nivån som ska läsas. Det här attributet kan till exempel begära att tal syntes motorn uttalar skiljetecken. Inga standard värden har definierats för `detail`. | Valfri |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Följande är de innehålls typer som stöds för `interpret-as` attributen `format` och. Inkludera endast `format` attributet om `interpret-as` har angetts till datum och tid.

| tolka som | format | Tolkning |
|--------------|--------|----------------|
| `address` | | Texten talas som en adress. Tal syntes motorn uttalar:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Som "Jag är på 150th-domstolen norra östra Redmond Washington". |
| `cardinal`, `number` | | Texten talas som ett kardinal nummer. Tal syntes motorn uttalar:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Det finns tre alternativ. " |
| `characters`, `spell-out` | | Texten talas som enskilda bokstäver (rättstavade). Tal syntes motorn uttalar:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />T. ex. "T E S." |
| `date` | DMY, MDÅ, YMD, ådm, YM, My, MD, DM, d, m, y | Texten talas som ett datum. `format` Attributet anger datumets format (*d = dag, m = månad och y = år*). Tal syntes motorn uttalar:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Som "idag är den Nineteenth oktober 2016". |
| `digits`, `number_digit` | | Texten talas som en sekvens med enskilda siffror. Tal syntes motorn uttalar:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Som "1 2 3 4 5 6 7 8 9". |
| `fraction` | | Texten talas som ett bråk tals tal. Tal syntes motorn uttalar:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Som "tre åttondelar av en tum". |
| `ordinal` | | Texten talas som ett ordnings tal. Tal syntes motorn uttalar:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Som "Välj det tredje alternativet". |
| `telephone` | | Texten talas som ett telefonnummer. `format` Attributet får innehålla siffror som representerar en landskod. Till exempel "1" för USA eller "39" för Italien. Tal syntes motorn kan använda den här informationen för att vägleda sitt uttal av ett telefonnummer. Telefonnumret kan också innehålla lands koden, och i så fall prioriteras lands koden i `format`. Tal syntes motorn uttalar:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />As "My Number är rikt nummer 8 8 8 5 5 5 1 2 1 2". |
| `time` | hms12, hms24 | Texten talas som en tid. `format` Attributet anger om tiden anges med en 12-timmarsformat (hms12) eller en 24-timmarsklocka (hms24). Använd kolon för att avgränsa tal som representerar timmar, minuter och sekunder. Följande är giltiga tids exempel: 12:35, 1:14:32, 08:15 och 02:50:45. Tal syntes motorn uttalar:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Som "tåget är i en del av fyra A M." |

**Användning**

`say-as` Elementet får bara innehålla text.

**Exempel**

Tal syntes motorn läser följande exempel som "din första förfrågan var för ett rum den Nineteenth oktober 20 10 med tidig ankomst till 12 35 PM."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Lägg till inspelat ljud

`audio`är ett valfritt element som gör att du kan infoga MP3-ljud i ett SSML-dokument. Bröd texten i ljud elementet kan innehålla oformaterad text eller SSML som talas om ljud filen inte är tillgänglig eller kan spelas upp. Dessutom kan `audio` `audio`elementet innehålla text och följande element:, `break`, `p` `s` `phoneme` `prosody` `say-as`,,,, och. `sub`

Alla ljud som ingår i SSML-dokumentet måste uppfylla följande krav:

* MP3-filen måste vara värd för en HTTPS-slutpunkt som är tillgänglig för Internet. HTTPS krävs, och den domän som är värd för MP3-filen måste presentera ett giltigt betrott TLS/SSL-certifikat.
* MP3-filen måste vara en giltig MP3-fil (MPEG v2).
* Bit hastigheten måste vara 48 kbit/s.
* Samplings frekvensen måste vara 16 000 Hz.
* Den sammanlagda tiden för alla text-och ljudfiler i ett enskilt svar får inte överstiga 90 (90) sekunder.
* MP3-filen får inte innehålla kundspecifik eller annan känslig information.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Attribut**

| Attribut | Beskrivning                                   | Obligatorisk/valfri                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Anger ljud filens plats/URL. | Krävs om du använder ljud elementet i ditt SSML-dokument. |

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Lägg till bakgrunds ljud

Med `mstts:backgroundaudio` -elementet kan du lägga till bakgrunds ljud till dina SSML-dokument (eller blanda en ljudfil med text till tal). Med `mstts:backgroundaudio` kan du repetera en ljudfil i bakgrunden, tona in i början av text till tal och tona ut i slutet av text till tal.

Om bakgrunds ljudet som tillhandahålls är kortare än text-till-tal-eller övertoningen, kommer den att upprepas. Om det är längre än text till tal, stoppas det när toningen är färdig.

Endast en bakgrunds ljud fil tillåts per SSML-dokument. Du kan dock blanda `audio` Taggar i `voice` elementet för att lägga till ytterligare ljud till ditt SSML-dokument.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `src` | Anger plats/URL för bakgrunds ljud filen. | Krävs om du använder bakgrunds ljud i ditt SSML-dokument. |
| `volume` | Anger bakgrunds ljud filens volym. **Godkända värden**: `0` till `100` inklusiv. Standardvärdet är `1`. | Valfri |
| `fadein` | Anger bakgrunds ljudets varaktighet "tona in" som millisekunder. Standardvärdet är `0`, vilket motsvarar ingen toning i. **Godkända värden**: `0` till `10000` inklusiv.  | Valfri |
| `fadeout` | Anger bakgrunds ljudets varaktighet tonar ut i millisekunder. Standardvärdet är `0`, vilket motsvarar ingen toning. **Godkända värden**: `0` till `10000` inklusiv.  | Valfri |

**Exempel**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Nästa steg

* [Språk stöd: röster, nationella inställningar, språk](language-support.md)
