---
title: Talsyntesmarkeringsspråk (SSML) - Taltjänst
titleSuffix: Azure Cognitive Services
description: Använda markeringsspråket talsyntes för att styra uttal och prosody i text-till-tal.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: dc11d26c73c52b5e6c4d8e05cc27dd6ebce0c5d8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399825"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Förbättra syntesen med talsyntesmarkeringsspråk (SSML)

SSML (Speech Synthesis Markup Language) är ett XML-baserat markeringsspråk som gör att utvecklare kan ange hur indatatext konverteras till syntetiserat tal med hjälp av text-till-tal-tjänsten. Jämfört med oformaterad text tillåter SSML utvecklare att finjustera tonhöjden, uttalet, talhastigheten, volymen och mer av text-till-tal-utdata. Normal interpunktion, till exempel paus efter en period, eller använda rätt intonation när en mening slutar med ett frågetecken hanteras automatiskt.

Taltjänstens implementering av SSML baseras på World Wide Web Consortiums [talsyntes markupspråk version 1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Kinesiska, japanska och koreanska tecken räknas som två tecken för fakturering. Mer information finns i [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard, neurala och anpassade röster

Välj mellan standard och neurala röster, eller skapa din egen anpassade röst som är unik för din produkt eller ditt varumärke. 75+ standardröster finns på mer än 45 språk och språk, och 5 neurala röster finns på fyra språk och språk. En fullständig lista över språk, språk och röster som stöds (neurala och standardiserade) finns i [språkstöd](language-support.md).

Mer information om standardröster, neurala och anpassade röster finns i [Översikt över text till tal](text-to-speech.md).

## <a name="special-characters"></a>Specialtecken

När du använder SSML bör du tänka på att specialtecken, till exempel citattecken, apostrofer och hakparenteser, måste vara förrymda. Mer information finns i [XML 1.0:Tillägg D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>SSML-element som stöds

Varje SSML-dokument skapas med SSML-element (eller taggar). Dessa element används för att justera tonhöjd, prosodi, volym med mera. I följande avsnitt beskrivs hur varje element används och när ett element krävs eller är valfritt.  

> [!IMPORTANT]
> Glöm inte att använda dubbla citattecken runt attributvärden. Standarder för välformad, giltig XML kräver att attributvärden omges av dubbla citattecken. Till exempel `<prosody volume="90">` är ett välformulerad, `<prosody volume=90>` giltigt element, men är det inte. SSML kanske inte känner igen attributvärden som inte anges.

## <a name="create-an-ssml-document"></a>Skapa ett SSML-dokument

`speak`är rotelementet och **krävs** för alla SSML-dokument. Elementet `speak` innehåller viktig information, till exempel version, språk och kodordförrådsdefinitionen.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `version` | Anger vilken version av SSML-specifikationen som används för att tolka dokumentmarkeringen. Den aktuella versionen är 1.0. | Krävs |
| `xml:lang` | Anger språket i rotdokumentet. Värdet kan innehålla en gemen, språkkod med `en`två bokstäver (till exempel ) eller språkkoden `en-US`och versaler land/region (till exempel). | Krävs |
| `xmlns` | Anger URI:n för dokumentet som definierar markeringsordlistan (elementtyperna och attributnamnen) i SSML-dokumentet. Den aktuella http://www.w3.org/2001/10/synthesisURI är . | Krävs |

## <a name="choose-a-voice-for-text-to-speech"></a>Välj en röst för text-till-tal

Elementet `voice` krävs. Den används för att ange den röst som används för text-till-tal.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `name` | Identifierar den röst som används för text-till-tal-utdata. En fullständig lista över röster som stöds finns i [Språkstöd](language-support.md#text-to-speech). | Krävs |

**Exempel**

> [!NOTE]
> I det `en-US-AriaRUS` här exemplet används rösten. En fullständig lista över röster som stöds finns i [Språkstöd](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Använda flera röster

I `speak` elementet kan du ange flera röster för text-till-tal-utdata. Dessa röster kan vara på olika språk. För varje röst måste texten radbrytas i ett `voice` element. 

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `name` | Identifierar den röst som används för text-till-tal-utdata. En fullständig lista över röster som stöds finns i [Språkstöd](language-support.md#text-to-speech). | Krävs |

> [!IMPORTANT]
> Flera röster är inkompatibla med ordgränsfunktionen. Word-gränsfunktionen måste inaktiveras för att kunna använda flera röster.

### <a name="disable-word-boundary"></a>Inaktivera ordgräns

Beroende på tal-SDK-språket ställer `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` du `false` in egenskapen `SpeechConfig` på en förekomst av objektet.

# <a name="c"></a>[C #](#tab/csharp)

Mer information finns <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Mer information finns <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Mer information finns <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Mer information finns <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Mer information finns <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Mer information finns <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Mer information finns <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

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

## <a name="adjust-speaking-styles"></a>Justera talstilar

> [!IMPORTANT]
> Justeringen av talstilar fungerar bara med neurala röster.

Som standard syntetiserar text-till-tal-tjänsten text med ett neutralt talformat för både standardröster och neurala röster. Med neurala röster kan du justera talstilen för att uttrycka `<mstts:express-as>` glädje, empati eller känsla med elementet. Detta är ett valfritt element som är unikt för taltjänsten.

För närvarande talar stil justeringar stöds för dessa neurala röster:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`

Ändringar tillämpas på meningsnivå och formatet varierar beroende på röst. Om ett format inte stöds returnerar tjänsten tal i standardformatet neutralt tal.

**Syntax**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `style` | Anger talformatet. För närvarande talar stilar är röst-specifika. | Krävs om du justerar talstilen för en neural röst. Om `mstts:express-as`du använder måste formatet anges. Om ett ogiltigt värde anges ignoreras det här elementet. |

Använd den här tabellen för att avgöra vilka talande format som stöds för varje neural röst.

| Röst                   | Format                     | Beskrivning                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast"`        | Uttrycker en formell och professionell ton för att berätta nyheter |
|                         | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kundsupport  |
|                         | `style="chat"`            | Uttrycker en avslappnad och avslappnad ton                         |
|                         | `style="cheerful"`        | Uttrycker en positiv och glad ton                         |
|                         | `style="empathetic"`      | Uttrycker en känsla av omsorg och förståelse               |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Uttrycker en formell och professionell ton för att berätta nyheter |
|                         | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kundsupport  |
|                         | `style="assistant"`       | Uttrycker en varm och avslappnad ton för digitala assistenter    |
|                         | `style="lyrical"`         | Uttrycker känslor på ett melodiskt och sentimentalt sätt         |

**Exempel**

Det här SSML-kodavsnittet `<mstts:express-as>` illustrerar hur elementet används `cheerful`för att ändra talformatet till .

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

## <a name="add-or-remove-a-breakpause"></a>Lägga till eller ta bort en paus

Använd `break` elementet för att infoga pauser (eller brytningar) mellan ord, eller förhindra pauser som automatiskt läggs till av text-till-tal-tjänsten.

> [!NOTE]
> Använd det här elementet om du vill åsidosätta standardbeteendet för text-till-tal (TTS) för ett ord eller en fras om det syntetiserade talet för det ordet eller frasen låter onaturligt. Ställ `strength` `none` in på för att förhindra en prosodisk brytning, som automatiskt infogas av text-till-tal-tjänsten.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `strength` | Anger den relativa varaktigheten för en paus med något av följande värden:<ul><li>ingen</li><li>x-svag</li><li>Svag</li><li>medium (standard)</li><li>Stark</li><li>x-stark</li></ul> | Valfri |
| `time` | Anger den absoluta varaktigheten för en paus i sekunder eller millisekunder. Exempel på giltiga `2s` värden är och`500` | Valfri |

| Styrka                      | Beskrivning |
|-------------------------------|-------------|
| Ingen, eller om inget värde anges | 0 ms        |
| x-svag                        | 250 ms      |
| Svag                          | 500 ms      |
| medel                        | 750 ms      |
| Stark                        | 1000 ms     |
| x-stark                      | 1250 ms     |

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Ange stycken och meningar

`p`och `s` element används för att beteckna stycken respektive meningar. I avsaknad av dessa element bestämmer text-till-tal-tjänsten automatiskt strukturen i SSML-dokumentet.

`p` Elementet kan innehålla text och `audio` `break`följande `phoneme`element: `sub` `mstts:express-as`, `s`, , `prosody` `say-as`, , , och .

Elementet `s` kan innehålla text och `audio` `break`följande `phoneme` `prosody`element: `mstts:express-as`, `sub`, , `say-as`, och .

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

## <a name="use-phonemes-to-improve-pronunciation"></a>Använd fonem för att förbättra uttalet

Elementet `ph` används för att för fonetiska uttal i SSML-dokument. Elementet `ph` kan bara innehålla text, inga andra element. Alltid ge människoläsbara tal som en återgång.

Fonetiska alfabet består av telefoner, som består av bokstäver, siffror eller tecken, ibland i kombination. Varje telefon beskriver ett unikt ljud av tal. Detta står i kontrast till det latinska alfabetet, där en bokstav kan representera flera talade ljud. Tänk på de olika uttalen av bokstaven "c" i orden "godis" och "upphöra", eller de olika uttalen av bokstaven kombinationen "th" i orden "sak" och "de".

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `alphabet` | Anger det fonetiska alfabetet som ska användas när uttalet `ph` av strängen syntetiseras i attributet. Strängen som anger alfabetet måste anges med gemener. Följande är de möjliga alfabet som du kan ange.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Internationellt fonetiskt alfabet <span class="docon docon-navigate-external x-hidden-focus"></span> </a></li><li>`sapi`&ndash; [Taltjänst fonetiskt alfabet](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Universell telefonuppsättning</li></ul><br>Alfabetet gäller `phoneme` endast för elementet i elementet.. | Valfri |
| `ph` | En sträng som innehåller telefoner som anger uttalet `phoneme` av ordet i elementet. Om den angivna strängen innehåller okända telefoner avvisar TTS-tjänsten (text-to-speech) hela SSML-dokumentet och ingen av de talutdata som anges i dokumentet. | Krävs om du använder fonem. |

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

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Använd anpassat lexikon för att förbättra uttalet

Ibland kan TTS inte uttala ett ord korrekt, till exempel ett företag eller utländskt namn. Utvecklare kan definiera läsningen av dessa entiteter i SSML med hjälp av `phoneme` och `sub` taggen, `lexicon` eller definiera läsning av flera entiteter genom att referera till en anpassad lexikonfil med taggen.

**Syntax**

```XML
<lexicon uri="string"/>
```

**Attribut**

| Attribut | Beskrivning                               | Obligatoriskt/tillval |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Adressen till det externa PLS-dokumentet. | Krävs.           |

**Användning**

Steg 1: Definiera anpassat lexikon 

Du kan definiera läsningen av entiteter med en lista över anpassade lexikonobjekt som lagras som en XML- eller .pls-fil.

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

Varje `lexeme` element är ett lexikonobjekt. `grapheme`innehåller text som beskriver ortografen i `lexeme`. Avläsningsformulär kan `alias`anges som . Telefonsträngen kan `phoneme` anges i elementet.

Elementet `lexicon` innehåller minst `lexeme` ett element. Varje `lexeme` element innehåller minst `grapheme` ett element `grapheme`och `alais`ett `phoneme` eller flera , och element. Elementet `grapheme` innehåller text som beskriver <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">rättskrivningskonsten <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Elementen `alias` används för att ange uttalet av en akronym eller en förkortad term. Elementet `phoneme` innehåller text som `lexeme` beskriver hur uttalas.

Mer information om anpassad lexikonfil finns i [UTTAL Lexicon Specification (PLS) Version 1.0](https://www.w3.org/TR/pronunciation-lexicon/) på W3C:s webbplats.

Steg 2: Ladda upp anpassad lexikonfil som skapats i steg 1 online, du kan lagra den var som helst och vi föreslår att du lagrar den i Microsoft Azure, till exempel [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Steg 3: Se anpassad lexikonfil i SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" kommer att läsas som "Förresten". "Benigni" kommer att läsas med försedda IPA "bɛˈniːnji".  

**Begränsning**
- Filstorlek: anpassad lexikon filstorlek maximal gräns är 100KB, om utöver denna storlek, kommer syntes begäran misslyckas.
- Lexicon cache uppdatering: anpassade lexikon kommer att cachelagras med URI som nyckel på TTS-tjänst när den först laddas. Lexicon med samma URI kommer inte att laddas om inom 15 minuter, så anpassade lexikon förändring måste vänta högst 15 minuter för att träda i kraft.

**Fonetiska uppsättningar för taltjänst**

I exemplet ovan använder vi det internationella fonetiska alfabetet, även känt som IPA-telefonuppsättningen. Vi föreslår utvecklare använder IPA, eftersom det är den internationella standarden. Med tanke på att IPA inte är lätt att komma ihåg definierar`en-US` `fr-FR`taltjänsten en fonetisk uppsättning för sju språk ( , , `de-DE`, `es-ES`, `ja-JP` `zh-CN`, och `zh-TW`).

Du kan `sapi` använda som vale `alphabet` för attributet med anpassade lexikon som visas nedan:

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

Mer information om det detaljerade taltjänstens fonetiska alfabet finns i [taltjänstens fonetiska uppsättningar](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Justera prosodi

Elementet `prosody` används för att ange ändringar i tonhöjd, kontur, intervall, hastighet, varaktighet och volym för text-till-tal-utdata. `prosody` Elementet kan innehålla text och `audio` `break`följande `p`element: `say-as` `sub`, `s`, , `phoneme` `prosody`, , , och .

Eftersom prosodic attributvärden kan variera över ett brett spektrum tolkar talre recognizern de tilldelade värdena som ett förslag på vilka de faktiska prosodic värdena för den valda rösten ska vara. Servicebegränsningarna för text-till-tal-tjänsten eller ersätter värden som inte stöds. Exempel på värden som inte stöds är en tonhöjd på 1 MHz eller en volym på 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `pitch` | Anger baslinjeutdelningen för texten. Du kan uttrycka planen som:<ul><li>Ett absolut värde, uttryckt som ett tal följt av "Hz" (Hertz). Till exempel 600 Hz.</li><li>Ett relativt värde, uttryckt som ett tal som föregås av "+" eller "-" och följt av "Hz" eller "st", som anger ett belopp som ska ändra tonhöjden. Till exempel: +80 Hz eller -2st. "st" anger att förändringsenheten är halvton, vilket är hälften av en ton (ett halvt steg) på standarddiatonisk skala.</li><li>Ett konstant värde:<ul><li>x-låg</li><li>Låg</li><li>medel</li><li>hög</li><li>x-hög</li><li>standard</li></ul></li></ul>. | Valfri |
| `contour` | Contour stöds inte för neurala röster. Konturen representerar förändringar i tonhöjd. Dessa ändringar representeras som en matris med mål vid angivna tidspositioner i talutdata. Varje mål definieras av uppsättningar med parameterpar. Ett exempel: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Det första värdet i varje uppsättning parametrar anger platsen för tonhöjdsändringen i procent av textens varaktighet. Det andra värdet anger det belopp som ska höjas eller sänka tonhöjden, `pitch`med hjälp av ett relativt värde eller ett uppräkningsvärde för tonhöjd (se ). | Valfri |
| `range` | Ett värde som representerar uttitchintervallet för texten. Du kan `range` uttrycka med samma absoluta värden, relativa värden eller `pitch`uppräkningsvärden som används för att beskriva . | Valfri |
| `rate` | Anger textens talfrekvens. Du kan `rate` uttrycka som:<ul><li>Ett relativt värde, uttryckt som ett tal som fungerar som en multiplikator av standardvärdet. Ett värde på *1* resulterar till exempel i ingen förändring av kursen. Värdet *0,5* resulterar i en halvering av kursen. Värdet *3* resulterar i en tredubbling av kursen.</li><li>Ett konstant värde:<ul><li>x-långsam</li><li>Långsam</li><li>medel</li><li>snabb</li><li>x-snabb</li><li>standard</li></ul></li></ul> | Valfri |
| `duration` | Den tidsperiod som ska förflytas medan talsyntesen (TTS) läser texten, i sekunder eller millisekunder. Till exempel *2s* eller *1800ms*. | Valfri |
| `volume` | Anger talröstens volymnivå. Du kan uttrycka volymen som:<ul><li>Ett absolut värde, uttryckt som ett tal i intervallet 0,0 till 100,0, från *tystaste* till *mest högljudda*. Till exempel 75. Standardvärdet är 100,0.</li><li>Ett relativt värde, uttryckt som ett tal som föregås av "+" eller "-" som anger ett belopp som ska ändra volymen. Till exempel +10 eller -5,5.</li><li>Ett konstant värde:<ul><li>Tyst</li><li>x-mjuk</li><li>Mjuk</li><li>medel</li><li>Högt</li><li>x-högt</li><li>standard</li></ul></li></ul> | Valfri |

### <a name="change-speaking-rate"></a>Ändra talfrekvens

Talhastighet kan tillämpas på standardröster på ord- eller meningsnivå. Medan talhastighet endast kan tillämpas på neurala röster på meningsnivå.

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

Volymändringar kan tillämpas på standardröster på ord- eller meningsnivå. Medan volymförändringar endast kan tillämpas på neurala röster på meningsnivå.

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

### <a name="change-pitch"></a>Ändra tonhöjd

Tonhöjdsändringar kan tillämpas på standardröster på ord- eller meningsnivå. Medan tonhöjdsändringar endast kan tillämpas på neurala röster på meningsnivå.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Ändra tonhöjdskontur

> [!IMPORTANT]
> Pitch kontur förändringar stöds inte med neurala röster.

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
## <a name="say-as-element"></a>säga-som-element

`say-as`är ett valfritt element som anger innehållstypen (t.ex. nummer eller datum) för elementets text. Detta ger vägledning till talsyntesmotorn om hur texten ska uttalas.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `interpret-as` | Anger innehållstypen för elementets text. En lista över typer finns i tabellen nedan. | Krävs |
| `format` | Ger ytterligare information om den exakta formateringen av elementets text för innehållstyper som kan ha tvetydiga format. SSML definierar format för innehållstyper som använder dem (se tabellen nedan). | Valfri |
| `detail` | Anger vilken detaljnivå som ska sägas upp. Det här attributet kan till exempel begära att talsyntesmotorn uttalar skiljetecken. Det finns inga standardvärden definierade för `detail`. | Valfri |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Följande är de innehållstyper `interpret-as` `format` som stöds för attributen och. Inkludera `format` attributet `interpret-as` endast om det är inställt på datum och tid.

| tolka-som | format | Tolkning |
|--------------|--------|----------------|
| `address` | | Texten visas som en adress. Talsyntesmotorn uttalar:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Som "Jag är vid 150: e domstolen nordöstra Redmond Washington." |
| `cardinal`, `number` | | Texten visas som ett kardinalnummer. Talsyntesmotorn uttalar:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Som "Det finns tre alternativ." |
| `characters`, `spell-out` | | Texten är uppläst som enskilda bokstäver (utredad). Talsyntesmotorn uttalar:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Som "T E S T." |
| `date` | dmy, mdy, ymd, ydm, ym, min, md, dm, d, m, y | Texten visas som ett datum. Attributet `format` anger datumets format (*d=day, m=month och y=year*). Talsyntesmotorn uttalar:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Som "Idag är oktober nittonde två tusen sexton." |
| `digits`, `number_digit` | | Texten visas som en sekvens med enskilda siffror. Talsyntesmotorn uttalar:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Som "1 2 3 4 5 6 7 8 9." |
| `fraction` | | Texten sägs upp som ett bråktal. Talsyntesmotorn uttalar:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Som "tre åttondelar av en tum." |
| `ordinal` | | Texten visas som ett ordningstal. Talsyntesmotorn uttalar:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Som "Välj det tredje alternativet". |
| `telephone` | | Texten är uppläst som ett telefonnummer. Attributet `format` kan innehålla siffror som representerar en landskod. Till exempel "1" för USA eller "39" för Italien. Talsyntesmotorn kan använda den här informationen för att styra uttalet av ett telefonnummer. Telefonnumret kan också innehålla landskoden och i så fall har företräde `format`framför landskoden i . Talsyntesmotorn uttalar:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Som "Mitt nummer är riktnummer åtta åtta åtta fem fem fem en två en två." |
| `time` | hms12, hms24 | Texten talars upp som en tid. Attributet `format` anger om tiden anges med en 12-timmarsklocka (hms12) eller en 24-timmarsklocka (hms24). Använd ett kolon för att avgränsa tal som representerar timmar, minuter och sekunder. Följande är giltiga tidsexempel: 12:35, 1:14:32, 08:15 och 02:50:45. Talsyntesmotorn uttalar:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Som "Tåget avgår vid fyra A M." |

**Användning**

Elementet `say-as` får bara innehålla text.

**Exempel**

Talsyntesen motorn talar följande exempel som "Din första begäran var för ett rum på oktober nittonde tjugo tio med tidig ankomst till tolv trettiofem PM."
 
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

`audio`är ett valfritt element som gör att du kan infoga MP3-ljud i ett SSML-dokument. Ljudelementets brödtext kan innehålla oformaterad text eller SSML-markering som är uppläst om ljudfilen inte är tillgänglig eller ospelbar. Dessutom kan `audio` elementet innehålla text och `audio`följande `break`element: `phoneme` `prosody`, `say-as`, `sub` `p`, `s`, , , och .

Alla ljud som ingår i SSML-dokumentet måste uppfylla dessa krav:

* MP3 måste finnas på en HTTPS-slutpunkt som är tillgänglig för Internet. HTTPS krävs och domänen som är värd för MP3-filen måste visa ett giltigt, betrott TLS/SSL-certifikat.
* MP3 måste vara en giltig MP3-fil (MPEG v2).
* Bithastigheten måste vara 48 kbit/s.
* Provfrekvensen måste vara 16 000 Hz.
* Den sammanlagda totala tiden för alla text- och ljudfiler i ett enda svar får inte överstiga nittio (90) sekunder.
* MP3 får inte innehålla någon kundspecifik eller annan känslig information.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Attribut**

| Attribut | Beskrivning                                   | Obligatoriskt/tillval                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Anger ljudfilens plats/URL. | Krävs om du använder ljudelementet i SSML-dokumentet. |

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

## <a name="add-background-audio"></a>Lägga till bakgrundsljud

Med `mstts:backgroundaudio` elementet kan du lägga till bakgrundsljud i SSML-dokumenten (eller blanda en ljudfil med text-till-tal). Med `mstts:backgroundaudio` dig kan du loopa en ljudfil i bakgrunden, tona in i början av text-till-tal och tona ut i slutet av text-till-tal.

Om bakgrundsljudet som tillhandahålls är kortare än text-till-tal eller tona ut, kommer det att loopa. Om den är längre än text-till-tal stoppas den när uttoningen är klar.

Endast en bakgrundsljudfil tillåts per SSML-dokument. Du kan dock intersperse `audio` `voice` taggar i elementet för att lägga till ytterligare ljud i ditt SSML-dokument.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/tillval |
|-----------|-------------|---------------------|
| `src` | Anger platsen/URL:en för bakgrundsljudfilen. | Krävs om du använder bakgrundsljud i SSML-dokumentet. |
| `volume` | Anger volymen på bakgrundsljudfilen. **Godkända**värden `0` `100` : till inkluderande. Standardvärdet är `1`. | Valfri |
| `fadein` | Anger varaktigheten för bakgrundsljudet "tona in" som millisekunder. Standardvärdet är `0`, vilket motsvarar ingen toning i. **Godkända**värden `0` `10000` : till inkluderande.  | Valfri |
| `fadeout` | Anger hur länge bakgrundsljudet tonas ut i millisekunder. Standardvärdet är `0`, vilket motsvarar ingen toning ut. **Godkända**värden `0` `10000` : till inkluderande.  | Valfri |

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

* [Språkstöd: röster, språk, språk](language-support.md)
