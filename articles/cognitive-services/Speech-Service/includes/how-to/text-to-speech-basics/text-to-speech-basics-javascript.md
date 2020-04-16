---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399571"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript <span class="docon docon-navigate-external x-hidden-focus"> </span>Speech SDK </a>. Beroende på din plattform använder du följande instruktioner:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbläsare<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Beroende på målmiljön används dessutom något av följande:

# <a name="import"></a>[Importera](#tab/import)

```javascript
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

Mer information `import`om finns i <a href="https://javascript.info/import-export" target="_blank">exportera och importera <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="require"></a>[Kräver](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Mer information `require`om finns i <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">vad som krävs? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[-skriptet](#tab/script)

Hämta och extrahera filen <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span> Speech SDK</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* och placera den i en mapp som är tillgänglig för HTML-filen.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Om du riktar in dig på `<script>` en webbläsare och använder taggen. prefixet `sdk` behövs inte. Prefixet `sdk` är ett alias `require` som används för att namnge modulen.

---

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Om du vill anropa taltjänsten med tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest). Den här klassen innehåller information om din prenumeration, till exempel din nyckel och associerade region, slutpunkt, värd eller auktoriseringstoken.

> [!NOTE]
> Oavsett om du utför taligenkänning, talsyntes, översättning eller avsiktsigenkänning skapar du alltid en konfiguration.

Det finns några sätt som du [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)kan initiera en:

* Med en prenumeration: skicka in en nyckel och den associerade regionen.
* Med en slutpunkt: passera i en taltjänstslutpunkt. En nyckel eller auktoriseringstoken är valfri.
* Med en värd: skicka in en värdadress. En nyckel eller auktoriseringstoken är valfri.
* Med en auktoriseringstoken: skicka in en auktoriseringstoken och den associerade regionen.

I det här exemplet [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) skapar du en med hjälp av en prenumerationsnyckel och region. Se [regionens supportsida](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regionidentifierare. Du kan också skapa en del grundläggande standardkod som ska användas för resten av den här artikeln, som du ändrar för olika anpassningar.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetisera tal till en fil

Därefter skapar du [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) ett objekt som kör text-till-tal-konverteringar och utdata till högtalare, filer eller andra utdataströmmar. Accepterar [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) som params [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) objektet som skapats i [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) föregående steg och ett objekt som anger hur utdataresultat ska hanteras.

Starta genom att `AudioConfig` skapa en för att `.wav` automatiskt `fromAudioFileOutput()` skriva utdata till en fil med hjälp av den statiska funktionen.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Därefter instansierar `SpeechSynthesizer` du `speechConfig` ett passerande objekt och `audioConfig` objektet som params. Att sedan köra talsyntes och skriva till en `speakTextAsync()` fil är lika enkelt som att köra med en textsträng. Resultatet motringning är ett `synthesizer.close()`bra ställe att ringa , i själva verket - detta samtal behövs för att syntesen ska fungera korrekt.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        }
    },
    error => {
        console.log(error);
        synthesizer.close();
    });
}
```

Kör programmet och en syntetiserad `.wav` fil skrivs till den plats du angav. Detta är ett bra exempel på den mest grundläggande användningen, men nästa du tittar på att anpassa utdata och hantera utdatasvaret som en in-memory ström för att arbeta med anpassade scenarier.

## <a name="synthesize-to-speaker-output"></a>Syntetisera till högtalarutgången

I vissa fall kanske du vill direkt mata ut syntetiserat tal direkt till en högtalare. För att göra detta, `AudioConfig` instansiera med hjälp av den `fromDefaultSpeakerOutput()` statiska funktionen. Detta matas ut till den aktuella aktiva utdataenheten.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Få resultat som en ström i minnet

För många scenarier i talprogramutveckling behöver du förmodligen de resulterande ljuddata som en in-memory-ström i stället för att direkt skriva till en fil. Detta gör att du kan bygga anpassade beteenden inklusive:

* Abstrakt den resulterande bytematrisen som en sök-kan ström för anpassade underordnade tjänster.
* Integrera resultatet med andra API: er eller tjänster.
* Ändra ljuddata, skriva `.wav` anpassade rubriker, etc.

Det är enkelt att göra den här ändringen från föregående exempel. Ta först `AudioConfig` bort blocket, eftersom du hanterar utdatabeteendet manuellt från och med nu för ökad kontroll. Passera `undefined` sedan `AudioConfig` för `SpeechSynthesizer` konstruktören. 

> [!NOTE]
> Om `undefined` du `AudioConfig`passerar för – i stället för att utelämna det som i högtalarutmatningsexempelet ovan spelas inte ljudet som standard på den aktuella aktiva utmatningsenheten.

Den här gången sparar [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) du resultatet i en variabel. Egenskapen `SpeechSynthesisResult.audioData` returnerar en `ArrayBuffer` av utdata. Du kan arbeta `ArrayBuffer` med detta manuellt.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Härifrån kan du implementera alla anpassade `ArrayBuffer` beteenden med det resulterande objektet.

## <a name="customize-audio-format"></a>Anpassa ljudformat

I följande avsnitt visas hur du anpassar ljudutdataattribut, inklusive:

* Filtyp för ljud
* Samplingsfrekvens
* Bitdjup

Om du vill ändra ljudformatet använder du egenskapen `speechSynthesisOutputFormat` på `SpeechConfig` objektet. Den här `enum` egenskapen [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)förväntar sig en typ , som du använder för att välja utdataformat. Se referensdokumenten för en [lista över tillgängliga ljudformat.](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)

Det finns olika alternativ för olika filtyper beroende på dina behov. Observera att råformat som `Raw24Khz16BitMonoPcm` per definition inte innehåller ljudrubriker. Använd endast råformat när du vet att implementeringen nedströms kan avkoda en rå bitström, eller om du planerar att manuellt skapa rubriker baserat på bitdjup, samplingsfrekvens, antal kanaler etc.

I det här exemplet anger du ett `Riff24Khz16BitMonoPcm` RIFF-format med hög återgivning genom att ange `speechSynthesisOutputFormat` objektets `SpeechConfig` på objektet. I likhet med exemplet i föregående `ArrayBuffer` avsnitt hämtar du ljuddata och interagerar med dem.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Om du kör programmet `.wav` igen skrivs en fil till den angivna sökvägen.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Använda SSML för att anpassa talegenskaper

Med SSML (Speech Synthesis Markup Language) kan du finjustera tonhöjden, uttalet, talhastigheten, volymen och mer av text-till-tal-utdata genom att skicka in dina begäranden från ett XML-schema. Det här avsnittet visar några praktiska användningsexempel, men för en mer detaljerad guide finns i [artikeln SSML how-to](../../../speech-synthesis-markup.md).

Om du vill börja använda SSML för anpassning gör du en enkel ändring som växlar rösten.
Skapa först en ny XML-fil för SSML-config i `ssml.xml`rotprojektkatalogen i det här exemplet . Rotelementet är `<speak>`alltid , och om `<voice>` du radbryts i `name` ett element kan du ändra rösten med hjälp av param. Det här exemplet ändrar rösten till en manlig engelsk (UK) röst. Observera att denna röst är en **vanlig** röst, som har olika prissättning och tillgänglighet än **neurala** röster. Se [hela listan över](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) **standardröster** som stöds.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Därefter måste du ändra begäran om talsyntes för att referera till XML-filen. Begäran är oftast densamma, men i `speakTextAsync()` stället för `speakSsmlAsync()`att använda funktionen använder du . Den här funktionen förväntar sig en XML-sträng, så först skapar du en funktion för att läsa in en XML-fil och returnera den som en sträng.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Mer information `readFileSync`om finns i <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">nod.js filsystem<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Härifrån är resultatobjektet exakt detsamma som tidigare exempel.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Utdata fungerar, men det några enkla ytterligare ändringar du kan göra för att hjälpa det låter mer naturligt. Den totala talhastigheten är lite för snabb, `<prosody>` så vi lägger till en tagg och minskar hastigheten till **90%** av standardhastigheten. Dessutom är pausen efter kommatecknet i meningen lite för kort och onaturligt klingande. Lös problemet genom att `<break>` lägga till en tagg för att fördröja talet och ställa in tidsparamen till **200 ms**. Kör syntesen igen för att se hur dessa anpassningar påverkade utdata.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Neurala röster

Neurala röster är talsyntes algoritmer som drivs av djupa neurala nätverk. När du använder en neural röst, syntetiserade tal är nästan omöjlig att skilja från de mänskliga inspelningarna. Med den människoliknande naturliga prosodien och tydliga artikulation av ord minskar neurala röster avsevärt lyssningströttheten när användare interagerar med AI-system.

För att byta till en `name` neural röst, ändra till en av de [neurala röstalternativ](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Lägg sedan till ett `mstts`XML-namnområde för `<mstts:express-as>` och radbryta texten i taggen. Använd `style` param för att anpassa talstilen. Det här `cheerful`exemplet används , `customerservice` `chat` men försök att ställa in det på eller för att se skillnaden i talstil.

> [!IMPORTANT]
> Neurala röster stöds **endast** för talresurser som skapats i *östra USA,* *Sydostasien*och *Västeuropa.*

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
