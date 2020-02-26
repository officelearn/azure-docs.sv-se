---
title: Konfigurera Start tiden för Change feed processor-Azure Cosmos DB
description: Lär dig hur du konfigurerar en Change feed-processor för att börja läsa från ett visst datum och en angiven tid
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 8a5507d11c9545e4053dde832b7305f9bf35e39e
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586282"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Konfigurera Start tiden för Change feed processor

I den här artikeln beskrivs hur du kan konfigurera [change feed-processorn](./change-feed-processor.md) för att börja läsa från ett visst datum och en angiven tidpunkt.

## <a name="default-behavior"></a>Standardbeteende

När en Change feed-processor startar första gången initieras behållaren för lån och startar [livs cykeln för bearbetning](./change-feed-processor.md#processing-life-cycle). Eventuella ändringar som gjordes i behållaren innan processorn för ändrings flöden initierades för första gången identifieras inte.

## <a name="reading-from-a-previous-date-and-time"></a>Läser från ett tidigare datum och en tidigare tidpunkt

Det går att initiera processorn för ändrings flöden för att läsa ändringar som börjar vid ett **visst datum och en angiven tidpunkt**, genom att skicka en instans av en `DateTime` till tillägget för `WithStartTime` Builder:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="TimeInitialization":::

Processorn för ändrings flöden initieras för det aktuella datumet och den aktuella tiden och börjar läsa de ändringar som skett efter.

## <a name="reading-from-the-beginning"></a>Läser från början

I andra scenarier, t. ex. datamigrering eller analys av hela historiken för en behållare, måste vi läsa ändrings flödet från **början av behållarens livs längd**. För att göra det kan vi använda `WithStartTime` i tillägget Builder, men genom att skicka `DateTime.MinValue.ToUniversalTime()`, vilket skulle generera UTC-representationen av det minsta `DateTime` värdet, t. ex.:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="StartFromBeginningInitialization":::

Processorn för ändrings flöden initieras och börjar läsa ändringar från början av containerns livs längd.

> [!NOTE]
> Dessa anpassnings alternativ fungerar bara för att ställa in start punkten i tiden för bearbetningen av Change feed. När låne containern har initierats för första gången har ändringar gjorts.

> [!NOTE]
> När du anger en tidpunkt kommer endast ändringar för objekt som finns i behållaren att läsas. Om ett objekt har tagits bort tas även historiken för ändrings flödet bort.

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användnings exempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om att ändra flödes processor i följande artiklar:

* [Översikt över ändring av flödes processor](change-feed-processor.md)
* [Använda uppskattningen ändra feed](how-to-use-change-feed-estimator.md)
