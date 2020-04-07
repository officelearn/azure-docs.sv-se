---
title: Prestandafrågor på serversidan
description: Så här utför du prestandafrågor på serversidan via API-anrop
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682031"
---
# <a name="server-side-performance-queries"></a>Prestandafrågor på serversidan

Bra renderingsprestanda på servern är avgörande för stabila bildhastigheter och en bra användarupplevelse. Det är viktigt att övervaka prestandaegenskaper på servern noggrant och optimera vid behov. Prestandadata kan efterfrågas via dedikerade API-funktioner.

Mest effektfulla för renderingsprestanda är modellens indata. Du kan justera indata enligt beskrivningen i [Konfigurera modellkonverteringen](../../how-tos/conversion/configure-model-conversion.md).

Programprestanda på klientsidan kan också vara en flaskhals. För en djupgående analys av klientsidans prestanda rekommenderas att du tar en [prestandaspårning](../../how-tos/performance-tracing.md).

## <a name="clientserver-timeline"></a>Tidslinje för klient/server

Innan du går in på detaljer om de olika latens värden, är det värt att ta en titt på synkroniseringspunkter mellan klient och server på tidslinjen:

![Tidslinje för pipeline](./media/server-client-timeline.png)

Bilden visar hur:

* en *Pose uppskattning* sparkas igång av kunden med konstant 60-Hz bildhastighet (varje 16,6 ms)
* servern börjar sedan återge, baserat på
* servern skickar tillbaka den kodade videobilden
* klienten avkodar avbildningen, utför en del CPU och GPU arbete ovanpå den och sedan visar bilden

## <a name="frame-statistics-queries"></a>Frågor om ramstatistik

Ramstatistik ger viss information på hög nivå för den senaste bildrutan, till exempel svarstid. Data som tillhandahålls `FrameStatistics` i strukturen mäts på klientsidan, så API:et är ett synkront anrop:

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

Det hämtade `FrameStatistics` objektet innehåller följande medlemmar:

| Medlem | Förklaring |
|:-|:-|
| latensPoseToReceive | Svarstid från kamera pose uppskattning på klientenheten tills en serverram för denna pose är fullt tillgänglig för klientprogrammet. Det här värdet inkluderar nätverksresa, serveråtergivningstid, videoavkodning och jitter-kompensation. Se **intervall 1 i bilden ovan.**|
| latensReceiveToPresent | Svarstid från tillgängligheten för en mottagen fjärrram tills klientappen anropar PresentFrame på processorn. |
| latenspresentföradisplay  | Svarstid från att presentera en ram på processorn tills displayen tänds. Det här värdet inkluderar klient-GPU-tid, eventuell bildrutebuffering som utförs av operativsystemet, omprojtering av maskinvara och enhetsberoende visningsgenomsökningstid. Se **intervall 2 i bilden ovan.**|
| tidSinceLastPresent | Tiden mellan efterföljande anrop till PresentFrame på processorn. Värden som är större än visningstiden (till exempel 16,6 ms på en 60-Hz-klientenhet) indikerar problem som orsakas av att klientprogrammet inte slutför sin CPU-arbetsbelastning i tid. Se **intervall 3 i bilden ovan.**|
| videoFramesKomment | Antalet ramar som togs emot från servern i sista sekunden. |
| videoFrameReusedCount | Antal mottagna bildrutor i den sista sekunden som användes på enheten mer än en gång. Värden som inte är noll anger att bildrutor måste återanvändas och återbejiceras antingen på grund av nätverksjitter eller överdriven serveråtergivningstid. |
| videoFramesSkipped | Antal mottagna bildrutor i den sista sekunden som avkodades, men som inte visas på displayen eftersom en nyare ram har anlänt. Icke-nollvärden anger att nätverksneross orsakade att flera bildrutor försenades och sedan anlände till klientenheten tillsammans i en burst. |
| videoFramesDiscarded videoFramesDiscarded videoFramesDiscarded videoFrame | Mycket lik **videoFramesSkipped**, men anledningen till att kasseras är att en ram kom in så sent att det inte ens kan korreleras med någon väntande pose längre. Om detta händer, det finns några allvarliga nätverk påstående.|
| videoFrameMinDelta | Minsta tid mellan två på varandra följande bildrutor som anländer under den sista sekunden. Tillsammans med videoFrameMaxDelta ger detta intervall en indikation på jitter som orsakas antingen av nätverket eller video codec. |
| videoFrameMaxDelta | Maximal tid mellan två på varandra följande bildrutor som anländer under den sista sekunden. Tillsammans med videoFrameMinDelta ger detta intervall en indikation på jitter som orsakas antingen av nätverket eller video codec. |

Summan av alla svarstidsvärden är vanligtvis mycket större än den tillgängliga tidsperioden vid 60 Hz. Detta är OK, eftersom flera bildrutor är i flykten parallellt, och nya rambegäranden sparkas igång med önskad bildhastighet, som visas i bilden. Men om latensen blir för stor, påverkar det kvaliteten på det [sena steget omprojection](../../overview/features/late-stage-reprojection.md), och kan äventyra den totala upplevelsen.

`videoFramesReceived`, `videoFrameReusedCount`och `videoFramesDiscarded` kan användas för att mäta nätverks- och serverprestanda. Om `videoFramesReceived` är `videoFrameReusedCount` låg och är hög, kan detta tyda på överbelastning av nätverket eller dålig serverprestanda. Ett `videoFramesDiscarded` högt värde indikerar också överbelastning i nätverket.

Slutligen ,`timeSinceLastPresent` `videoFrameMinDelta`, `videoFrameMaxDelta` och ge en uppfattning om variansen för inkommande videoramar och lokala närvarande samtal. Hög varians innebär instabil bildhastighet.

Inget av värdena ovan ger tydlig indikation på ren nätverksfördröjning (de röda pilarna i bilden), eftersom den exakta tiden `latencyPoseToReceive`som servern är upptagen med rendering måste subtraheras från värdet för tur och retur . Den del av svarstiden på serversidan av den totala svarstiden är information som inte är tillgänglig för klienten. I nästa stycke förklaras dock hur det här värdet approximerats genom ytterligare indata från servern och exponeras via `networkLatency` värdet.

## <a name="performance-assessment-queries"></a>Frågor om prestandabedömning

*Prestandabedömningsfrågor* ger mer ingående information om CPU- och GPU-arbetsbelastningen på servern. Eftersom data begärs från servern följer en begäran om en prestandaögonblicksbild det vanliga asynkronmönstret:

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

I motsats `FrameStatistics` till objektet `PerformanceAssessment` innehåller objektet information på serversidan:

| Medlem | Förklaring |
|:-|:-|
| timeCPU | Genomsnittlig server-CPU-tid per bildruta i millisekunder |
| timeGPU | Genomsnittlig server-GPU-tid per bildruta i millisekunder |
| användningCPU | Totalt server-CPU-användning i procent |
| användningGPU | Total server-GPU-användning i procent |
| memoryCPU | Total resursminnesanvändning i procent |
| minneGPU | Totalt dedikerat videominnesanvändning i procent av servern GPU |
| nätverkLatens | Den ungefärliga genomsnittliga svarstiden för rundtursnätverk i millisekunder. I bilden ovan motsvarar detta summan av de röda pilarna. Värdet beräknas genom att den faktiska serveråtergivningstiden `latencyPoseToReceive` subtraheras från värdet `FrameStatistics`för . Även om den här approximationen inte är korrekt, ger den en indikation på nätverksfördröjningen, isolerad från svarstidsvärdena som beräknas på klienten. |
| polygonsRendered | Antalet trianglar som återges i en ram. Detta nummer innehåller också de trianglar som gallras senare under rendering. Det innebär att detta antal inte varierar mycket mellan olika kamerapositioner, men prestanda kan variera drastiskt, beroende på triangeln culling takt.|

För att hjälpa dig att bedöma värdena, kommer varje del med en kvalitet klassificering som **Great**, **Bra**, **Medioker**, eller **Dålig**.
Det här bedömningsmåttet ger en grov indikation på serverns hälsa, men det bör inte ses som absolut. Anta till exempel att du ser en "medioker" poäng för GPU-tiden. Det anses medioker eftersom det kommer nära gränsen för den totala tidsramen. I ditt fall kan det dock vara ett bra värde ändå, eftersom du gör en komplex modell.

## <a name="statistics-debug-output"></a>Felsökningsutdata för statistik

Klassen `ARRServiceStats` sveper runt både ramstatistik och prestandabedömningsfrågor och ger praktiska funktioner för att returnera statistik som aggregerade värden eller som en förbyggd sträng. Följande kod är det enklaste sättet att visa statistik på serversidan i klientprogrammet.

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Koden ovan fyller textetiketten med följande text:

![Strängutdata för ArrServiceStats](./media/arr-service-stats.png)

`GetStatsString` API:et formaterar en sträng med alla värden, men varje enskilt `ARRServiceStats` värde kan också efterfrågas programmässigt från instansen.

Det finns också varianter av medlemmarna, som sammanställer värdena över tiden. Visa medlemmar med `*Avg`suffix , `*Max`, eller `*Total`. Medlemmen `FramesUsedForAverage` anger hur många bildrutor som har använts för den här aggregeringen.

## <a name="next-steps"></a>Nästa steg

* [Skapa prestandaspårningar](../../how-tos/performance-tracing.md)
* [Konfigurera modellkonverteringen](../../how-tos/conversion/configure-model-conversion.md)
