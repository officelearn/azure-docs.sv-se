---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: e0c2554162d54af6c0a483e26f708838c3045a03
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986249"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera Tal-SDK. Beroende på din plattform använder du följande instruktioner:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Körtid<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln inkluderar du följande importsatser högst upp i skriptet.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Om du vill anropa taltjänsten med tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Den här klassen innehåller information om din prenumeration, till exempel din nyckel och associerade region, slutpunkt, värd eller auktoriseringstoken.

> [!NOTE]
> Oavsett om du utför taligenkänning, talsyntes, översättning eller avsiktsigenkänning skapar du alltid en konfiguration.

Det finns några sätt som du [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)kan initiera en:

* Med en prenumeration: skicka in en nyckel och den associerade regionen.
* Med en slutpunkt: passera i en taltjänstslutpunkt. En nyckel eller auktoriseringstoken är valfri.
* Med en värd: skicka in en värdadress. En nyckel eller auktoriseringstoken är valfri.
* Med en auktoriseringstoken: skicka in en auktoriseringstoken och den associerade regionen.

I det här exemplet [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) skapar du en med hjälp av en prenumerationsnyckel och region. Se [regionens supportsida](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regionidentifierare. Du kan också skapa en del grundläggande standardkod som ska användas för resten av den här artikeln, som du ändrar för olika anpassningar.

```java
public class Program 
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetisera tal till en fil

Därefter skapar du [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) ett objekt som kör text-till-tal-konverteringar och utdata till högtalare, filer eller andra utdataströmmar. Accepterar [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) som params [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) objektet som skapats i [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) föregående steg och ett objekt som anger hur utdataresultat ska hanteras.

Starta genom att `AudioConfig` skapa en för att `.wav` automatiskt `fromWavFileOutput()` skriva utdata till en fil med hjälp av den statiska funktionen.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Därefter instansierar `SpeechSynthesizer` du `speechConfig` ett passerande objekt och `audioConfig` objektet som params. Att sedan köra talsyntes och skriva till en `SpeakText()` fil är lika enkelt som att köra med en textsträng.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Kör programmet och en syntetiserad `.wav` fil skrivs till den plats du angav. Detta är ett bra exempel på den mest grundläggande användningen, men nästa du tittar på att anpassa utdata och hantera utdatasvaret som en in-memory ström för att arbeta med anpassade scenarier.

## <a name="synthesize-to-speaker-output"></a>Syntetisera till högtalarutgången

I vissa fall kanske du vill direkt mata ut syntetiserat tal direkt till en högtalare. För att göra detta, `AudioConfig` instansiera med hjälp av den `fromDefaultSpeakerOutput()` statiska funktionen. Detta matas ut till den aktuella aktiva utdataenheten.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Få resultat som en ström i minnet

För många scenarier i talprogramutveckling behöver du förmodligen de resulterande ljuddata som en in-memory-ström i stället för att direkt skriva till en fil. Detta gör att du kan bygga anpassade beteenden inklusive:

* Abstrakt den resulterande bytematrisen som en sök-kan ström för anpassade underordnade tjänster.
* Integrera resultatet med andra API: er eller tjänster.
* Ändra ljuddata, skriva `.wav` anpassade rubriker, etc.

Det är enkelt att göra den här ändringen från föregående exempel. Ta först `AudioConfig` bort blocket, eftersom du hanterar utdatabeteendet manuellt från och med nu för ökad kontroll. Passera `null` sedan `AudioConfig` för `SpeechSynthesizer` konstruktören. 

> [!NOTE]
> Om `null` du `AudioConfig`passerar för – i stället för att utelämna det som i högtalarutmatningsexempelet ovan spelas inte ljudet som standard på den aktuella aktiva utmatningsenheten.

Den här gången sparar [`SpeechSynthesisResult`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-java-stable) du resultatet i en variabel. Funktionen `SpeechSynthesisResult.getAudioData()` returnerar `byte []` en av utdata. Du kan arbeta `byte []` med detta manuellt eller [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) använda klassen för att hantera in-memory-strömmen. I det här `AudioDataStream.fromResult()` exemplet använder du den statiska funktionen för att hämta en ström från resultatet.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    
    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

Härifrån kan du implementera alla anpassade `stream` beteenden med det resulterande objektet.

## <a name="customize-audio-format"></a>Anpassa ljudformat

I följande avsnitt visas hur du anpassar ljudutdataattribut, inklusive:

* Filtyp för ljud
* Samplingsfrekvens
* Bitdjup

Om du vill ändra ljudformatet `setSpeechSynthesisOutputFormat()` `SpeechConfig` använder du funktionen på objektet. Den här `enum` funktionen [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable)förväntar sig en typ som du använder för att välja utdataformat. Se referensdokumenten för en [lista över tillgängliga ljudformat.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet)

Det finns olika alternativ för olika filtyper beroende på dina behov. Observera att råformat som `Raw24Khz16BitMonoPcm` per definition inte innehåller ljudrubriker. Använd endast råformat när du vet att implementeringen nedströms kan avkoda en rå bitström, eller om du planerar att manuellt skapa rubriker baserat på bitdjup, samplingsfrekvens, antal kanaler etc.

I det här exemplet anger du ett `Riff24Khz16BitMonoPcm` RIFF-format med hög återgivning genom att ange `SpeechSynthesisOutputFormat` objektets `SpeechConfig` på objektet. I likhet med exemplet i föregående [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) avsnitt använder du för att hämta en in-memory-ström av resultatet och sedan skriva det till en fil.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
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

Därefter måste du ändra begäran om talsyntes för att referera till XML-filen. Begäran är oftast densamma, men i `SpeakText()` stället för `SpeakSsml()`att använda funktionen använder du . Den här funktionen förväntar sig en XML-sträng, så först skapar du en funktion för att läsa in en XML-fil och returnera den som en sträng.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

Härifrån är resultatobjektet exakt detsamma som tidigare exempel.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
