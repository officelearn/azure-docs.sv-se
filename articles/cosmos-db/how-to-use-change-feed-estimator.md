---
title: Använd ändringsfeedkalkylorn - Azure Cosmos DB
description: Lär dig hur du använder ändringsflödeskalkylatorn för att analysera förloppet för ändringsflödesprocessorn
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 0023f68400b36b9abd3b9d4a789895e79f67aa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585296"
---
# <a name="use-the-change-feed-estimator"></a>Använd ändringsmatningsuppskattningsgivaren

I den här artikeln beskrivs hur du kan övervaka förloppet för processorinstanserna för [ändringsflödet](./change-feed-processor.md) när de läser ändringsflödet.

## <a name="why-is-monitoring-progress-important"></a>Varför är det viktigt att övervaka framstegen?

Ändringsfeedprocessorn fungerar som en pekare som går framåt i [ändringsflödet](./change-feed.md) och levererar ändringarna i en ombudsimplementering. 

Distributionen av ändringsflödesprocessorn kan bearbeta ändringar med en viss hastighet baserat på dess tillgängliga resurser som CPU, minne, nätverk och så vidare.

Om den här hastigheten är långsammare än den hastighet med vilken dina ändringar sker i din Azure Cosmos-behållare, börjar processorn att släpa efter.

Identifiera det här scenariot hjälper till att förstå om vi behöver skala vår distribution av ändringsfeedprocessor.

## <a name="implement-the-change-feed-estimator"></a>Implementera ändringsmatningsuppskattningsgivaren

Precis som [ändringsmatningsprocessorn](./change-feed-processor.md)fungerar ändringsmatningsuppskattningen som en push-modell. Kalkylatorn mäter skillnaden mellan den senast bearbetade artikeln (definierad av lånebehållarens tillstånd) och den senaste ändringen i behållaren och skickar det här värdet till ett ombud. Det intervall med vilket mätningen görs kan också anpassas med ett standardvärde på 5 sekunder.

Om ändringsflödesbehandlaren till exempel definieras så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Det korrekta sättet att initiera en skattningsgivare `GetChangeFeedEstimatorBuilder` för att mäta att processorn skulle använda så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Om både processorn och kalkylatorn har samma `leaseContainer` och samma namn.

De andra två parametrarna är ombudet, som får ett tal som representerar hur många ändringar som **väntar på att läsas** av processorn och det tidsintervall med vilket du vill att mätningen ska göras.

Ett exempel på ett ombud som tar emot uppskattningen är:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Du kan skicka den här uppskattningen till din övervakningslösning och använda den för att förstå hur dina framsteg beter sig över tid.

> [!NOTE]
> Ändringsflödesuppskattningsordningen behöver inte distribueras som en del av ändringsflödesbehandlaren och inte heller vara en del av samma projekt. Det kan vara oberoende och köras i en helt annan instans. Det behöver bara använda samma namn och lånekonfiguration.

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användningsexempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta med att läsa mer om ändringsflödesbehandlare i följande artiklar:

* [Översikt över ändringsmatningsprocessor](change-feed-processor.md)
* [Starttid för ändringsflödesprocessor](how-to-configure-change-feed-start-time.md)
