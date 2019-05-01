---
title: Metodtips när du använder API:et för avvikelseidentifiering
description: 'Läs mer om bästa praxis när du söker efter avvikelser med API: T för Avvikelseidentifiering detektor.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 766d009be3cd664d928a3c12f5fea38c26bbbdde
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692203"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Metodtips för API: T för Avvikelseidentifiering detektor

Avvikelseidentifiering detektor API är en tillståndslös avvikelseidentifieringstjänst. Prestanda resultat kan påverkas av:

* Hur dina tidsseriedata förbereds.
* Avvikelseidentifiering detektor API-parametrar som användes.
* Antalet datapunkter i API-begäran. 

Använd den här artikeln om du vill veta mer om bästa praxis för att använda API: et får bästa resultat för dina data. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>När du ska använda batch (hela) eller senaste (senaste) pekar avvikelseidentifiering

Avvikelseidentifiering detektor API batch identifiering slutpunkt kan du identifiera avvikelser genom hela times series-data. I det här identifieringsläget, skapas en enda statistisk modell och tillämpas på varje punkt i datauppsättningen. Om din tidsserier har den nedan egenskaper, vi rekommenderar att du använder batch-identifiering förhandsgranska data i ett API-anrop.

* En säsongens tidsserie, med tillfällig avvikelser.
* En fast trend tidsserie, med tillfälliga toppar/dalar. 

Vi rekommenderar inte med hjälp av batch-avvikelseidentifiering för realtidsdata övervakning eller används i time series-data som inte har ovan egenskaper. 

* Identifiering av batch skapar och gäller endast en modell, identifiering för varje punkt sker i samband med hela serien. Om time series datatrender uppåt och nedåt utan säsongsberoende, några saker för ändrar (dalar och toppar i data) kan missas i modellen. På samma sätt kan räknas några saker av ändring som är mindre viktig än de som senare i datauppsättningen inte betydelse i modellen.

* Batch-identifiering är långsammare än identifiering av avvikelser status för den senaste tidpunkten när du gör realtidsdata övervakning, på grund av antalet punkter som analyseras.

För övervakning av data i realtid rekommenderar vi att identifiera avvikelser statusen för din senaste datapunkt endast. Genom att kontinuerligt tillämpa den senaste återställningspunkt identifiering, kan strömmande data övervakning göras mer effektivt och korrekt.

Exemplet nedan beskriver påverkan dessa identifiering lägen kan ha på prestanda. Den första bilden visar resultatet av kontinuerligt identifiering av avvikelser status senaste punkt längs 28 tidigare visad datapunkter. Avvikelser är röda punkter.

![En bild som visar avvikelseidentifiering med hjälp av den senaste tidpunkten](../media/last.png)

Nedan är samma data med hjälp av batch-avvikelseidentifiering. Modellen som byggts för åtgärden har ignorerats flera avvikelser som markerats av rektanglar.

![En bild som visar avvikelseidentifiering med batch-metoden](../media/entire.png)

## <a name="data-preparation"></a>Förberedelse av data

API: T för Avvikelseidentifiering detektor accepterar tidsserier data formaterats som en JSON-begäran. En tidsserie kan vara numeriska data registreras med tiden i sekventiell ordning. Du kan skicka windows av time series-data till den Avvikelseidentifiering detektor API-slutpunkten för att förbättra prestanda för API: er. Det minsta antalet datapunkter som du kan skicka är 12, och högsta 8640 punkter. 

Datapunkter som skickas till API: T för Avvikelseidentifiering detektor måste ha en giltig Coordinated Universal Time (UTC) tidsstämpel och numeriska värden. 

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

### <a name="missing-data-points"></a>Saknade datapunkter

Saknade datapunkter är vanliga i jämnt fördelade time series datauppsättningar, särskilt de som med en bra kornighet (ett litet exempelintervall. Till exempel data samplas några minuters mellanrum). Mindre än 10% av det förväntade antalet punkter i dina data som saknas får inte ha en negativ inverkan på resultaten av programuppdateringsidentifieringen. Överväg att fylla luckor i dina data baserat på dess egenskaper som Ersätt datapunkter från en tidigare period, linjär interpolation eller ett glidande medelvärde.

### <a name="aggregate-distributed-data"></a>Sammanställd distribuerade data

API: T för Avvikelseidentifiering detektor fungerar bäst på ett jämnt fördelade tidsserier. Om dina data distribueras slumpmässigt, du bör samla ihop den av en tidsenhet som Per minut, per timme eller dag till exempel.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Identifiering av avvikelser i data med säsongens mönster

Om du vet att dina tidsseriedata har en säsongsknutet mönster (en som inträffar med jämna mellanrum), kan du förbättra noggrannheten och API-svarstiden. 

Ange en `period` när du skapar din JSON-begäran kan förkorta svarstiden för identifiering av avvikelser med upp till 50%. Den `period` är ett heltal som anger ungefär hur många datapunkter tidsserien tar du upprepa ett mönster. Till exempel en tidsserie med en datapunkt per dag skulle ha en `period` som `7`, och en tidsserie med en punkt per timme (med samma veckovisa mönster) skulle ha en `period` av `7*24`. Om du är osäker på dina data mönster kan du inte ange den här parametern.

För bästa resultat bör du ange 4 `period`är för datapunkt, plus ytterligare en. Till exempel varje timme data med ett mönster för veckovis enligt beskrivningen ovan bör ge 673 datapunkter i frågans brödtext (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Sampling av data för övervakning i realtid

Om din strömmande data samplas med en liten stund (till exempel sekunder eller minuter), kan skickar det rekommenderade antalet datapunkter överskrida Avvikelseidentifiering detektor API maximala antalet tillåtna (8640 datapunkter). Överväg att skicka ett urval av dina time series-data med ett större tidsintervall som timmar om dina data visas en stabil säsongsknutet mönster. Sampling dina data på det här sättet kan du också märkbart förbättra API-svarstiden. 

## <a name="next-steps"></a>Nästa steg

* [Vad är API: T för Avvikelseidentifiering detektor?](../overview.md)
* [Snabbstart: Identifiera avvikelser i dina time series-data med hjälp av Avvikelseidentifiering detektor REST API](../quickstarts/detect-data-anomalies-csharp.md)
