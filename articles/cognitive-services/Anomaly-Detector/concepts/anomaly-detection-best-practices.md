---
title: Metodtips när du använder API:et för avvikelseidentifiering
titleSuffix: Azure Cognitive Services
description: Lär dig mer om metodtips när du identifierar avvikelser med API:et för avvikelsedetektor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721620"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Metodtips för användning av API:et för avvikelsedetektor

API:et för avvikelsedetektor är en tillståndslös avvikelseidentifieringstjänst. Resultatens riktighet och prestanda kan påverkas av:

* Hur dina tidsseriedata förbereds.
* API-parametrarna för avvikelsedetektorn som användes.
* Antalet datapunkter i din API-begäran. 

Använd den här artikeln om du vill lära dig mer om metodtips för hur du använder API:et för att få bästa resultat för dina data. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>När batch (hela) eller senaste (sista) punkt avvikelseidentifiering ska användas

Med API:et för avvikelsedetektorns batchidentifiering kan du identifiera avvikelser genom data i hela tidsserierna. I det här identifieringsläget skapas en enda statistisk modell på varje punkt i datauppsättningen. Om din tidsserie har nedanstående egenskaper rekommenderar vi att du använder batchidentifiering för att förhandsgranska dina data i ett API-anrop.

* En säsongsmässig tidsserie, med enstaka avvikelser.
* En platt trend tidsserie, med enstaka spikar / dips. 

Vi rekommenderar inte att du använder batchavvikelseidentifiering för dataövervakning i realtid eller använder den på tidsseriedata som inte har ovanstående egenskaper. 

* Batchidentifiering skapar och tillämpar endast en modell, identifieringen för varje punkt görs i samband med hela serier. Om tidsseriedatatrenderna uppåt och nedåt utan säsongsvariationer kan vissa förändringspunkter (dips och toppar i data) missas av modellen. På samma sätt kan vissa ändringspunkter som är mindre betydande än de senare i datauppsättningen inte räknas som tillräckligt betydande för att införlivas i modellen.

* Batchidentifiering är långsammare än att identifiera avvikelsestatus för den senaste punkten när du gör dataövervakning i realtid, på grund av antalet punkter som analyseras.

För dataövervakning i realtid rekommenderar vi att du upptäcker avvikelsestatus för den senaste datapunkten. Genom att kontinuerligt tillämpa den senaste punktdetekteringen kan övervakning av strömmande data göras mer effektivt och korrekt.

I exemplet nedan beskrivs vilken inverkan dessa identifieringslägen kan ha på prestanda. Den första bilden visar resultatet av att kontinuerligt upptäcka avvikelsestatus senaste punkten längs 28 tidigare sett datapunkter. De röda punkterna är avvikelser.

![En bild som visar avvikelseidentifiering med den senaste punkten](../media/last.png)

Nedan visas samma datauppsättning med batchavvikelseidentifiering. Modellen som skapats för åtgärden har ignorerat flera avvikelser, markerade med rektanglar.

![En bild som visar avvikelseidentifiering med batchmetoden](../media/entire.png)

## <a name="data-preparation"></a>Förberedelse av data

Api:et för avvikelsedetektorn accepterar tidsseriedata som är formaterade till ett JSON-begärandeobjekt. En tidsserie kan vara alla numeriska data som registreras över tid i sekventiell ordning. Du kan skicka fönster i tidsseriedata till API-slutpunkten för avvikelsedetektor för att förbättra API:ets prestanda. Det minsta antalet datapunkter som du kan skicka är 12 och det högsta är 8640 poäng. [Granularitet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) definieras som den hastighet som dina data samplas på. 

Datapunkter som skickas till API:et för avvikelsedetektor måste ha en giltig TIDSstämpel (Coordinated Universal Time) och det numeriska värdet. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Om dina data samplas med ett tidsintervall som inte `customInterval` är standard kan du ange det genom att lägga till attributet i din begäran. Om din serie till exempel samplas var femte minut kan du lägga till följande i din JSON-begäran:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Datapunkter saknas

Saknade datapunkter är vanliga i jämnt fördelade tidsseriedatauppsättningar, särskilt de med fin granularitet (Ett litet samplingsintervall. Till exempel samplas data med några minuters mellanrum). Mindre än 10 % av det förväntade antalet punkter i dina data bör inte ha en negativ inverkan på identifieringsresultaten. Överväg att fylla i luckor i dina data baserat på dess egenskaper som att ersätta datapunkter från en tidigare period, linjär interpolation eller ett glidande medelvärde.

### <a name="aggregate-distributed-data"></a>Aggregerade distribuerade data

Api:et för avvikelsedetektor fungerar bäst i en jämnt fördelad tidsserie. Om dina data distribueras slumpmässigt bör du aggregera dem med en tidsenhet, till exempel Per minut, timme eller dagligen.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Avvikelseidentifiering på data med säsongsmönster

Om du vet att dina tidsseriedata har ett säsongsmönster (ett som inträffar med jämna mellanrum) kan du förbättra noggrannheten och API-svarstiden. 

Om du `period` anger en när du konstruerar JSON-begäran kan avvikelseidentifieringsfördröjningen minskas med upp till 50 %. Är `period` ett heltal som anger ungefär hur många datapunkter tidsserierna tar att upprepa ett mönster. En tidsserie med en datapunkt per `period` dag `7`skulle till exempel ha en som , och en tidsserie med en poäng per timme (med samma veckomönster) skulle ha en `period` av `7*24`. Om du är osäker på dina datas mönster behöver du inte ange den här parametern.

För bästa resultat, `period`ge 4' s värde av datapunkt, plus en extra. Till exempel bör timdata med ett veckomönster enligt beskrivningen ovan ge 673 datapunkter i begärandeorganet (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Provtagningsdata för övervakning i realtid

Om dina strömmande data samplas med ett kort intervall (till exempel sekunder eller minuter) kan det rekommenderade antalet datapunkter överskrida API:et för avvikelsedetektorer (8640 datapunkter). Om dina data visar ett stabilt säsongsmönster kan du överväga att skicka ett urval av tidsseriedata med ett större tidsintervall, till exempel timmar. Att prova dina data på det här sättet kan också märkbart förbättra API-svarstiden. 

## <a name="next-steps"></a>Nästa steg

* [Vad är API:et för avvikelseidentifiering?](../overview.md)
* [Snabbstart: Identifiera avvikelser i tidsseriedata med REST API:et för avvikelsedetektor](../quickstarts/detect-data-anomalies-csharp.md)
