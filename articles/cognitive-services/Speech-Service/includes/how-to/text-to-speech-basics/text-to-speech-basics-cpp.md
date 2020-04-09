---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: c5d954cc2bdda0b1fcb67801fa948e1f56fb0364
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986242"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera Tal-SDK. Beroende på din plattform använder du följande instruktioner:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">Macos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i `using` den här artikeln inkluderar du följande import och utdrag högst upp i skriptet.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Om du vill anropa taltjänsten med tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig). Den här klassen innehåller information om din prenumeration, till exempel din nyckel och associerade region, slutpunkt, värd eller auktoriseringstoken.

> [!NOTE]
> Oavsett om du utför taligenkänning, talsyntes, översättning eller avsiktsigenkänning skapar du alltid en konfiguration.

Det finns några sätt som du [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)kan initiera en:

* Med en prenumeration: skicka in en nyckel och den associerade regionen.
* Med en slutpunkt: passera i en taltjänstslutpunkt. En nyckel eller auktoriseringstoken är valfri.
* Med en värd: skicka in en värdadress. En nyckel eller auktoriseringstoken är valfri.
* Med en auktoriseringstoken: skicka in en auktoriseringstoken och den associerade regionen.

I det här exemplet [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) skapar du en med hjälp av en prenumerationsnyckel och region. Se [regionens supportsida](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regionidentifierare. Du kan också skapa en del grundläggande standardkod som ska användas för resten av den här artikeln, som du ändrar för olika anpassningar.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}
    
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetisera tal till en fil

Därefter skapar du [`SpeechSynthesizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer) ett objekt som kör text-till-tal-konverteringar och utdata till högtalare, filer eller andra utdataströmmar. Accepterar [`SpeechSynthesizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer) som params [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) objektet som skapats i [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) föregående steg och ett objekt som anger hur utdataresultat ska hanteras.

Starta en `AudioConfig` för att automatiskt skriva utdata till en `.wav` fil med hjälp av `FromWavFileOutput()` funktionen.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

Därefter instansierar `SpeechSynthesizer`du ett `config` , `audioConfig` passerar ditt objekt och objektet som params. Att sedan köra talsyntes och skriva till en `SpeakTextAsync()` fil är lika enkelt som att köra med en textsträng.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

Kör programmet och en syntetiserad `.wav` fil skrivs till den plats du angav. Detta är ett bra exempel på den mest grundläggande användningen, men nästa du tittar på att anpassa utdata och hantera utdatasvaret som en in-memory ström för att arbeta med anpassade scenarier.

## <a name="synthesize-to-speaker-output"></a>Syntetisera till högtalarutgången

I vissa fall kanske du vill direkt mata ut syntetiserat tal direkt till en högtalare. För att göra detta, `AudioConfig` helt enkelt `SpeechSynthesizer` utelämna param när du skapar i exemplet ovan. Detta matas ut till den aktuella aktiva utdataenheten.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Få resultat som en ström i minnet

För många scenarier i talprogramutveckling behöver du förmodligen de resulterande ljuddata som en in-memory-ström i stället för att direkt skriva till en fil. Detta gör att du kan bygga anpassade beteenden inklusive:

* Abstrakt den resulterande bytematrisen som en sök-kan ström för anpassade underordnade tjänster.
* Integrera resultatet med andra API: er eller tjänster.
* Ändra ljuddata, skriva `.wav` anpassade rubriker, etc.

Det är enkelt att göra den här ändringen från föregående exempel. Ta först `AudioConfig`bort , som du kommer att hantera utdatabeteendet manuellt från den här punkten och framåt för ökad kontroll. Passera `NULL` sedan `AudioConfig` för `SpeechSynthesizer` konstruktören. 

> [!NOTE]
> Om `NULL` du `AudioConfig`passerar för – i stället för att utelämna det som i högtalarutmatningsexempelet ovan spelas inte ljudet som standard på den aktuella aktiva utmatningsenheten.

Den här gången sparar [`SpeechSynthesisResult`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesisresult) du resultatet i en variabel. Gettern `GetAudioData` returnerar `byte []` en av utdata. Du kan arbeta `byte []` med detta manuellt eller [`AudioDataStream`](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) använda klassen för att hantera in-memory-strömmen. I det här `AudioDataStream.FromResult()` exemplet använder du den statiska funktionen för att hämta en ström från resultatet.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

Härifrån kan du implementera alla anpassade `stream` beteenden med det resulterande objektet.

## <a name="customize-audio-format"></a>Anpassa ljudformat

I följande avsnitt visas hur du anpassar ljudutdataattribut, inklusive:

* Filtyp för ljud
* Samplingsfrekvens
* Bitdjup

Om du vill ändra ljudformatet `SetSpeechSynthesisOutputFormat()` `SpeechConfig` använder du funktionen på objektet. Den här `enum` funktionen [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)förväntar sig en typ som du använder för att välja utdataformat. Se referensdokumenten för en [lista över tillgängliga ljudformat.](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)

Det finns olika alternativ för olika filtyper beroende på dina behov. Observera att råformat som `Raw24Khz16BitMonoPcm` per definition inte innehåller ljudrubriker. Använd endast råformat när du vet att implementeringen nedströms kan avkoda en rå bitström, eller om du planerar att manuellt skapa rubriker baserat på bitdjup, samplingsfrekvens, antal kanaler etc.

I det här exemplet anger du ett `Riff24Khz16BitMonoPcm` RIFF-format med hög återgivning genom att ange `SpeechSynthesisOutputFormat` objektets `SpeechConfig` på objektet. I likhet med exemplet i föregående [`AudioDataStream`](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) avsnitt använder du för att hämta en in-memory-ström av resultatet och sedan skriva det till en fil.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
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

Därefter måste du ändra begäran om talsyntes för att referera till XML-filen. Begäran är oftast densamma, men i `SpeakTextAsync()` stället för `SpeakSsmlAsync()`att använda funktionen använder du . Den här funktionen förväntar sig en XML-sträng, så du läser först in SSML-konfigurationen som en sträng. Härifrån är resultatobjektet exakt detsamma som tidigare exempel.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
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
