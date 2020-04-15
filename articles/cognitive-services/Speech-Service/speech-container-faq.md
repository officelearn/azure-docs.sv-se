---
title: Taltjänstbehållare med vanliga frågor och svar (FAQ)
titleSuffix: Azure Cognitive Services
description: Installera och kör talbehållare. tal-till-text transkriberar ljudströmmar till text i realtid som dina program, verktyg eller enheter kan använda eller visa. Text-till-tal konverterar indatatext till människoliknande syntetiserat tal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: aahi
ms.openlocfilehash: 17582244aef173da6ac700c980f7bd7fb0fec307
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383083"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Taltjänstbehållare med vanliga frågor och svar (FAQ)

När du använder taltjänsten med behållare bör du förlita dig på den här samlingen av vanliga frågor innan du eskalerar till stöd. Denna artikel fångar frågor varierande grad, från allmänna till tekniska. Om du vill utöka ett svar klickar du på frågan.

## <a name="general-questions"></a>Allmänna frågor

<details>
<summary>
<b>Hur fungerar talbehållare och hur konfigurerar jag dem?</b>
</summary>

**Svar:** När du ställer in produktionsklustret finns det flera saker att tänka på. För det första bör det inte vara ett stort problem att konfigurera ett enda språk, flera behållare på samma dator. Om du har problem kan det vara ett maskinvarurelaterat problem - så vi skulle först titta på resurs, det vill säga; CPU- och minnesspecifikationer.

Tänk för ett `ja-JP` ögonblick, behållaren och senaste modellen. Den akustiska modellen är den mest krävande bit CPU-vis, medan den språkmodell kräver mest minne. När vi benchmarked användningen, det tar ca 0,6 CPU-kärnor för att bearbeta en enda tal-till-text-begäran när ljud flödar in i realtid (som från mikrofonen). Om du matar ljud snabbare än realtid (som från en fil) kan den användningen fördubblas (1,2 x kärnor). Samtidigt är minnet nedan driftminne för avkodning tal. Det tar *inte* hänsyn till den faktiska fulla storleken på språkmodellen, som kommer att finnas i filcachen. För `ja-JP` det är ytterligare 2 GB; för `en-US`kan det vara mer (6-7 GB).

Om du har en dator där minnet är knappa, och du försöker distribuera flera språk på den, är det möjligt att filcachen är full och operativsystemet tvingas att söka modeller in och ut. För en löpande transkription, som kan vara katastrofala, och kan leda till avmattning och andra konsekvenser prestanda.

Dessutom förpaketerar vi körbara filer för maskiner med den [avancerade instruktionsuppsättningen för vektorförlängning (AVX2).](speech-container-howto.md#advanced-vector-extension-support) En maskin med AVX512 instruktionsuppsättningen kräver kodgenerering för det målet, och starta 10 behållare för 10 språk kan tillfälligt avgas CPU. Ett meddelande som detta visas i dockerloggarna:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Slutligen kan du ställa in hur många dekodrar du `DECODER MAX_COUNT` vill ha inuti en *enda* behållare med variabeln. Så, i princip bör vi börja med din SKU (CPU / minne), och vi kan föreslå hur man får ut det bästa av det. En bra utgångspunkt är att referera till de rekommenderade resursspecifikationerna för värdmaskinen.

<br>
</details>

<details>
<summary>
<b>Kan du hjälpa till med kapacitetsplanering och kostnadsuppskattning av on-prem Talbehållare?</b>
</summary>

**Svar:** För behållarkapacitet i batchbearbetningsläge kunde varje dekoder hantera 2-3x i realtid, med två CPU-kärnor, för en enda igenkänning. Vi rekommenderar inte att du behåller mer än två samtidiga igenkänningar per behållarinstans, men rekommenderar att du kör fler instanser av behållare av tillförlitlighets-/tillgänglighetsskäl bakom en belastningsutjämnare.

Även om vi kan ha varje containerinstans igång med fler dekodrar. Vi kanske till exempel kan ställa in 7 avkodare per behållarinstans på en åtta kärnmaskin (vid mer än 2x vardera), vilket ger 15x dataflöde. Det finns en `DECODER_MAX_COUNT` param att vara medveten om. För det extrema fallet uppstår tillförlitlighet och latensproblem, med genomströmning ökat avsevärt. För en mikrofon, kommer det att vara på 1x realtid. Den totala användningen bör vara ungefär en kärna för en enda igenkänning.

För scenario med bearbetning 1 K timmar/dag i batchbearbetningsläge, i ett extremt fall, 3 virtuella datorer kunde hantera det inom 24 timmar men inte garanteras. För att hantera toppdagar, redundans, uppdatering och för att tillhandahålla minsta säkerhetskopiering/BCP rekommenderar vi 4-5 datorer i stället för 3 per kluster och med 2 + kluster.

För maskinvara använder vi `DS13_v2` standarddytads-vm som referens (varje kärna måste vara 2,6 GHz eller bättre, med AVX2-instruktionsuppsättning aktiverad).

| Instans  | vCPU(er) | RAM    | Temp lagring | Betala per du kör med AHB | 1-års reserv med AHB (% Besparingar) | 3 års reserverad med AHB (% Besparingar) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB (gib) | 112 GiB (på andra)      | $0.598/timme            | $0.3528/timme (~41%)                 | $0.2333/timme (~61%)                  |

Baserat på designreferensen (två kluster med 5 virtuella datorer för att hantera 1 K timmar/dag ljudpartibearbetning) blir 1 års maskinvarukostnad:

> 2 (kluster) * 5 (virtuella datorer per kluster) * $0.3528/hour * 365 (dagar) * 24 (timmar) = $31K / år

Vid mappning till fysisk maskin är en allmän uppskattning 1 vCPU = 1 Fysisk CPU-kärna. I verkligheten är 1vCPU mer kraftfull än en enda kärna.

För on-prem, alla dessa ytterligare faktorer spelar in:

- På vilken typ den fysiska processorn är och hur många kärnor på den
- Hur många processorer som körs tillsammans på samma låda/maskin
- Så här konfigureras virtuella datorer
- Hur hyper-threading / flertrådning används
- Så här delas minnet
- Operativsystemet, etc.

Normalt är det inte lika väljusterat som Azure miljön. Med tanke på andra overhead, skulle jag säga en säker uppskattning är 10 fysiska CPU-kärnor = 8 Azure vCPU. Även populära processorer har bara åtta kärnor. Med distribution på prem blir kostnaden högre än att använda virtuella Azure-datorer. Tänk också på avskrivningstakten.

Servicekostnaden är densamma som onlinetjänsten: $1/timme för tal-till-text. Kostnaden för taltjänsten är:

> $1 * 1000 * 365 = $365K

Underhållskostnaden som betalas till Microsoft beror på tjänstens servicenivå och innehåll. Det olika från $ 29.99/month för grundläggande nivå till hundratusentals om på plats tjänst inblandade. Ett grovt nummer är $ 300/hour för service / underhålla. Människor kostnaden ingår inte. Andra infrastrukturkostnader (till exempel lagring, nätverk och belastningsutjämnare) ingår inte.

<br>
</details>

<details>
<summary>
<b>Varför saknas interpunktion från transkriptionen?</b>
</summary>

**Svar:** Bör `speech_recognition_language=<YOUR_LANGUAGE>` uttryckligen konfigureras i begäran om de använder Carbon-klienten.

Ett exempel:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Här är utdata:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Kan jag använda en anpassad akustisk modell och språkmodell med Talbehållare?</b>
</summary>

Vi kan för närvarande bara passera ett modell-ID, antingen anpassad språkmodell eller anpassad akustisk modell.

**Svar:** Beslutet att *inte* stödja både akustiska och språkmodeller samtidigt fattades. Detta kommer att fortsätta att gälla tills en enhetlig identifierare skapas för att minska API-avbrott. Så tyvärr stöds detta inte just nu.

<br>
</details>

<details>
<summary>
<b>Kan du förklara dessa fel från den anpassade behållaren för tal-till-text?</b>
</summary>

**Fel 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Svar 1:** Om du tränar med den senaste anpassade modellen stöder vi för närvarande inte det. Om du tränar med en äldre version, bör det vara möjligt att använda. Vi arbetar fortfarande på att stödja de senaste versionerna.

I huvudsak stöder de anpassade behållarna inte Halide eller ONNX-baserade akustiska modeller (vilket är standard i den anpassade utbildningsportalen). Detta beror på att anpassade modeller inte krypteras och vi vill inte exponera ONNX-modeller, dock; språkmodeller är bra. Kunden måste uttryckligen välja en äldre icke-ONNX-modell för anpassad utbildning. Noggrannheten påverkas inte. Modellstorleken kan vara större (med 100 MB).

> Stödmodell > 20190220 (v4.5 Unified)

**Fel 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Svar 2:** Du måste ange rätt röstnamn i begäran, som är skiftlägeskänslig. Se mappningen för fullständigt tjänstnamn. Du måste `en-US-JessaRUS`använda `en-US-JessaNeural` , som inte är tillgänglig just nu i behållarversionen av text-till-tal.

**Fel 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Svar 3:** Du skickar för att skapa en talresurs, inte en Cognitive Services-resurs.


<br>
</details>

<details>
<summary>
<b>Vilka API-protokoll stöds, REST eller WS?</b>
</summary>

**Svar:** För tal-till-text- och anpassade tal-till-text-behållare stöder vi för närvarande endast det websocketbaserade protokollet. SDK stöder bara anrop i WS men inte REST. Det finns en plan för att lägga rest stöd, men inte ETA för tillfället. Se alltid den officiella dokumentationen, se [slutpunkter för frågeprognos .](speech-container-howto.md#query-the-containers-prediction-endpoint)

<br>
</details>

<details>
<summary>
<b>Stöds CentOS för talbehållare?</b>
</summary>

**Svar:** CentOS 7 stöds inte av Python SDK ännu, även Ubuntu 19.04 stöds inte.

Python Speech SDK-paketet är tillgängligt för dessa operativsystem:
- **Windows** - x64 och x86
- **Mac** - macOS X version 10.12 eller senare
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 på x64

Mer information om miljöinställningar finns i [Python-plattformsinställningar](quickstarts/setup-platform.md?pivots=programming-language-python). För nu är Ubuntu 18.04 den rekommenderade versionen.

<br>
</details>

<details>
<summary>
<b>Varför får jag fel när jag försöker anropa LUIS-förutsägelseslutpunkter?</b>
</summary>

Jag använder LUIS-behållaren i en IoT Edge-distribution och försöker anropa LUIS-förutsägelseslutpunkten från en annan behållare. LUIS-behållaren lyssnar på port 5001 och webbadressen jag använder är följande:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Felet jag får är:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Jag ser begäran i LUIS-behållarloggarna och meddelandet säger:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Vad betyder detta? Vad är det jag saknar? Jag följde exemplet för tal SDK, [härifrån](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Scenariot är att vi upptäcker ljudet direkt från PC-mikrofonen och försöker fastställa avsikten, baserat på LUIS-appen vi tränade. Det exempel jag länkade till gör just det. Och det fungerar bra med LUIS molnbaserade tjänsten. Använda Tal SDK tycktes rädda oss från att behöva göra en separat explicit samtal till tal-till-text API och sedan ett andra samtal till LUIS.

Så allt jag försöker göra är att växla från scenariot med att använda LUIS i molnet till att använda LUIS-behållaren. Jag kan inte föreställa mig om tal SDK fungerar för en, kommer det inte att fungera för den andra.

**Svar:** Tal-SDK ska inte användas mot en LUIS-behållare. För att använda LUIS-behållaren bör LUIS SDK- eller LUIS REST API användas. Tal SDK ska användas mot en talbehållare.

Ett moln skiljer sig från en behållare. Ett moln kan bestå av flera aggregerade behållare (kallas ibland mikrotjänster). Så det finns en LUIS-behållare och sedan finns det en talbehållare - Två separata behållare. Behållaren Tal gör bara tal. LUIS-behållaren gör bara LUIS. I molnet, eftersom båda behållarna är kända för att distribueras, och det är dålig prestanda för en fjärrklient att gå till molnet, göra tal, komma tillbaka, sedan gå till molnet igen och göra LUIS, tillhandahåller vi en funktion som gör att klienten kan gå till Tal, stanna i molnet, gå till LUIS och sedan komma tillbaka till klienten. Således även i det här scenariot tal SDK går till Tal moln behållare med ljud, och sedan Tal moln behållare samtal till LUIS moln behållare med text. LUIS-behållaren har inget koncept för att acceptera ljud (det skulle inte vara meningsfullt för LUIS-behållaren att acceptera strömmande ljud - LUIS är en textbaserad tjänst). Med on-prem, vi har ingen säkerhet vår kund har distribuerat båda behållarna, vi förutsätter inte att orkestrera mellan behållare i våra kunders lokaler, och om båda behållarna distribueras på prem, med tanke på att de är mer lokala för kunden, är det inte en börda att gå SR först, tillbaka till kunden, och har kunden sedan ta den texten och gå till LUIS.

<br>
</details>

<details>
<summary>
<b>Varför får vi fel med macOS, Talbehållare och Python SDK?</b>
</summary>

När vi skickar en *WAV-fil* som ska transkriberas, kommer resultatet tillbaka med:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Vi vet att websocket är korrekt inställd.

**Svar:** Om så är fallet, se [detta GitHub fråga](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Vi har en lösning, [föreslås här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Carbon fast detta på version 1.8.


<br>
</details>

<details>
<summary>
<b>Vilka är skillnaderna i slutpunkterna för talbehållare?</b>
</summary>

Kan du hjälpa till att fylla följande testmått, inklusive vilka funktioner som ska testas, och hur du testar SDK- och REST-API:erna? Speciellt skillnader i "interaktiv" och "konversation", som jag inte såg från befintliga doc / prov.

| Slutpunkt                                                | Funktionellt test                                                   | SDK | REST-API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Syntetisera text (text-till-tal)                                  |     | Ja      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services on-prem dictation v1 websocket slutpunkt        | Ja | Inga       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Cognitive Services on-prem interaktiva v1 websocket slutpunkt  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Den kognitiva tjänster on-prem konversation v1 websocket slutpunkt |     |          |

**Svar:** Detta är en fusion av:
- Personer som försöker dikteringslutpunkten för behållare, (jag är inte säker på hur de fick den webbadressen)
- Den<sup>1: a</sup> part slutpunkten är den i en behållare.
- Den<sup>1: a</sup> part slutpunkten återvänder speech.fragment meddelanden i stället för `speech.hypothesis` de meddelanden 3:<sup>e</sup> delen slutpunkter tillbaka för diktering slutpunkten.
- Kol snabbstarter all `RecognizeOnce` användning (interaktivt läge)
- Carbon har en `speech.fragment` hävda att för meddelanden som kräver att de inte returneras i interaktivt läge.
- Kol med hävdar brand i release bygger (döda processen).

Lösningen växlar antingen till att använda kontinuerlig igenkänning i koden eller (snabbare) ansluta till antingen interaktiva eller kontinuerliga slutpunkter i behållaren.
För koden ställer du in slutpunkten på <värd:port>/ta/erkännande/interaktiva/kognitiva tjänster/v1

För de olika lägena, se Tallägen - se nedan:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Rätt fix kommer med SDK 1,8, som har on-prem stöd (kommer att välja rätt slutpunkt, så vi kommer inte att vara värre än online-tjänst). Under tiden finns det ett prov för kontinuerlig igenkänning, varför pekar vi inte på det?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Vilket läge ska jag använda för olika ljudfiler?</b>
</summary>

**Svar:** Här är en [snabbstart med Python](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python). Du hittar de andra språk som är länkade på dokumentwebbplatsen.

Bara för att klargöra för den interaktiva, konversation och diktamen; Detta är ett avancerat sätt att specificera det särskilda sätt på vilket vår tjänst kommer att hantera talbegäran. Tyvärr, för on-prem behållare måste vi ange hela URI (eftersom det innehåller lokal maskin), så denna information läckt ut från abstraktion. Vi samarbetar med SDK-teamet för att göra detta mer användbart i framtiden.

<br>
</details>

<details>
<summary>
<b>Hur kan vi jämföra ett grovt mått på transaktioner / andra / kärna?</b>
</summary>

**Svar:** Här är några av de grova siffror att förvänta sig av befintliga modellen (kommer att förändras till det bättre i den vi kommer att leverera i GA):

- För filer kommer begränsningen att finnas i talet SDK, vid 2x. De första fem sekunderna av ljudet begränsas inte. Decoder kan göra ca 3x realtid. För detta kommer den totala CPU-användningen att vara nära 2 kärnor för en enda igenkänning.
- För mic, kommer det att vara på 1x realtid. Den totala användningen bör vara på ca 1 kärna för en enda igenkänning.

Allt detta kan verifieras från dockerloggarna. Vi dumpar faktiskt linjen med sessions- och fras-/yttrandestatistik, och det inkluderar RTF-nummer.


<br>
</details>

<details>
<summary>
<b>Är det vanligt att dela upp ljudfiler i chuckar för talbehållare användning?</b>
</summary>

Min nuvarande plan är att ta en befintlig ljudfil och dela upp den i 10 sekunders bitar och skicka dem genom behållaren. Är det ett godtagbart scenario?  Finns det ett bättre sätt att bearbeta större ljudfiler med behållaren?

**Svar:** Använd bara talet SDK och ge den filen, kommer det att göra det rätta. Varför behöver du segment filen?


<br>
</details>

<details>
<summary>
<b>Hur gör jag flera behållare som körs på samma värd?</b>
</summary>

Doc säger att exponera en annan port, vilket jag gör, men LUIS-behållaren lyssnar fortfarande på port 5000?

**Svar:** Försök `-p <outside_unique_port>:5000`. Till exempel `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Tekniska frågor

<details>
<summary>
<b>Hur kan jag få API:er &lt;som inte är batch-api:er för att hantera ljud 15 sekunder?</b>
</summary>

**Svar:** `RecognizeOnce()` i interaktivt läge bearbetar endast upp till 15 sekunder av ljud, eftersom läget är avsett för talkommando där yttranden förväntas vara korta. Om du `StartContinuousRecognition()` använder för diktering eller konversation finns det ingen gräns på 15 sekunder.


<br>
</details>

<details>
<summary>
<b>Vilka är de rekommenderade resurserna, CPU och RAM; för 50 samtidiga förfrågningar?</b>
</summary>

Hur många samtidiga begäranden kommer en 4 core, 4 GB RAM-handtag? Om vi måste tjäna till exempel 50 samtidiga förfrågningar, hur många Core och RAM rekommenderas?

**Svar:** I realtid, 8 med `en-US`vår senaste, så vi rekommenderar att du använder fler docker behållare utöver 6 samtidiga förfrågningar. Det blir galnare bortom 16 kärnor, och det blir icke-enhetlig minnesåtkomst (NUMA) nod känslig. I följande tabell beskrivs den minsta och rekommenderade fördelningen av resurser för varje talbehållare.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container      | Minimum             | Rekommenderas         |
|----------------|---------------------|---------------------|
| Tal till text | 2 kärnor, 2 GB minne | 4-kärniga, 4 GB minne |

# <a name="custom-speech-to-text"></a>[Anpassad tal-till-text](#tab/cstt)

| Container             | Minimum             | Rekommenderas         |
|-----------------------|---------------------|---------------------|
| Anpassad tal-till-text | 2 kärnor, 2 GB minne | 4-kärniga, 4 GB minne |

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

| Container      | Minimum             | Rekommenderas         |
|----------------|---------------------|---------------------|
| Text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

| Container             | Minimum             | Rekommenderas         |
|-----------------------|---------------------|---------------------|
| Anpassad text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

***

- Varje kärna måste vara minst 2,6 GHz eller snabbare.
- För filer kommer begränsningen att finnas i Tal-SDK, vid 2x (de första 5 sekunderna av ljudet begränsas inte).
- Dekodern kan göra ca 2-3x realtid. För detta kommer den totala CPU-användningen att vara nära två kärnor för en enda igenkänning. Därför rekommenderar vi inte att du behåller fler än två aktiva anslutningar per behållarinstans. Den extrema sidan skulle vara att sätta ca 10 dekodrar på 2x realtid i en åtta core maskin som `DS13_V2`. För behållarversionen 1.3 och senare finns det en `DECODER_MAX_COUNT=20`param som du kan prova att ställa in .
- För mikrofon, kommer det att vara på 1x realtid. Den totala användningen bör vara ungefär en kärna för en enda igenkänning.

Tänk på det totala antalet timmar ljud du har. Om antalet är stort, för att förbättra tillförlitligheten / tillgängligheten, föreslår vi att köra fler instanser av behållare, antingen på en enda ruta eller på flera rutor, bakom en belastningsutjämnare. Orkestrering kan göras med Kubernetes (K8S) och Helm, eller med Docker komponera.

Som ett exempel, för att hantera 1000 timmar /24 timmar, har vi försökt att ställa in 3-4 virtuella datorer, med 10 instanser / dekodrar per virtuell dator.

<br>
</details>

<details>
<summary>
<b>Stöder behållaren Tal interpunktion?</b>
</summary>

**Svar:** Vi har versaler (ITN) tillgängliga i behållaren on-prem. Interpunktion är språkberoende och stöds inte för vissa språk, inklusive kinesiska och japanska.

Vi *do* har implicit och grundläggande skiljetecken stöd för `off` befintliga behållare, men det är som standard. Vad det betyder är att `.` du kan få karaktären `。` i ditt exempel, men inte tecknet. För att aktivera denna implicita logik, här är ett exempel på hur man gör det i Python med hjälp av vår Tal SDK (det skulle vara liknande på andra språk):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Varför får jag 404 fel när jag försöker publicera data i behållaren för tal-till-text?</b>
</summary>

Här är ett exempel HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Svar:** Vi stöder inte REST API i någon av tal-till-text-behållare, vi stöder bara WebSockets via Speech SDK. Se alltid den officiella dokumentationen, se [slutpunkter för frågeprognos .](speech-container-howto.md#query-the-containers-prediction-endpoint)

<br>
</details>

<details>
<summary>
<b>Varför får jag det här felet när jag använder tal-till-text-tjänsten?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Svar:** Detta händer vanligtvis när du matar ljudet snabbare än vad behållaren för taligenkänning kan ta det. Klientbuffertar fylls och annulleringen utlöses. Du måste kontrollera samtidigheten och RTF där du skickar ljudet.

<br>
</details>

<details>
<summary>
<b>Kan du förklara dessa text-till-tal-behållarfel från C++-exemplen?</b>
</summary>

**Svar:** Om behållarversionen är äldre än 1.3 ska den här koden användas:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Äldre behållare har inte den slutpunkt som krävs `FromHost` för att Carbon ska fungera med API:et. Om behållarna som används för version 1.3 ska den här koden användas:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Nedan är ett exempel `FromEndpoint` på hur du använder API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 Funktionen `SetSpeechSynthesisVoiceName` anropas eftersom behållarna med en uppdaterad text-till-tal-motor kräver röstnamnet.

<br>
</details>

<details>
<summary>
<b>Hur kan jag använda v1.7 i Tal-SDK med en talbehållare?</b>
</summary>

**Svar:** Det finns tre slutpunkter på talbehållaren för olika användningar, de definieras som tallägen – se nedan:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

De är för olika ändamål och används på olika sätt.

[Python-exempel:](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
- För enkel igenkänning (interaktivt läge) med en anpassad slutpunkt (det vill ha; `SpeechConfig` med en slutpunktsparameter), se `speech_recognize_once_from_file_with_custom_endpoint_parameters()`.
- För kontinuerlig igenkänning (konversationsläge) och bara ändra `speech_recognize_continuous_from_file()`för att använda en anpassad slutpunkt som ovan, se .
- Om du vill aktivera diktering i exempel som ovan (endast `speech_config`om `speech_config.enable_dictation()`du verkligen behöver det) direkt efter att du har skapat , lägg till kod .

Anropa funktionen i `SpeechConfig.EnableDictation()` C# för att aktivera diktering.

### <a name="fromendpoint-apis"></a>`FromEndpoint`Api
| Språk | API-information |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Stöds för närvarande inte, inte heller är det planerat. |

<br>
</details>

<details>
<summary>
<b>Hur kan jag använda v1.8 i Speech SDK med en talbehållare?</b>
</summary>

**Svar:** Det finns ett `FromHost` nytt API. Detta ersätter eller ändrar inte befintliga API:er. Det lägger bara till ett alternativt sätt att skapa ett tal config med hjälp av en anpassad värd.

### <a name="fromhost-apis"></a>`FromHost`Api

| Språk | API-information |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Stöds för närvarande inte |

> Parametrar: värd (obligatoriskt), prenumerationsnyckel (valfritt, om du kan använda tjänsten utan den).

Format för `protocol://hostname:port` värd `:port` är där är valfritt (se nedan):
- Om behållaren körs lokalt är `localhost`värdnamnet .
- Om behållaren körs på en fjärrserver använder du serverns värdnamn eller IPv4-adress.

Exempel på värdparameter för tal-till-text:
- `ws://localhost:5000`- icke-säker anslutning till en lokal behållare med port 5000
- `ws://some.host.com:5000`- icke-säker anslutning till en behållare som körs på en fjärrserver

Python-exempel ovanifrån, `host` men `endpoint`använd parametern i stället för:

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Cognitive Services-behållare](speech-container-howto.md)
