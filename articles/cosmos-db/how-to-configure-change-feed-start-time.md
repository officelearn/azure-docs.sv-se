---
title: Konfigurera ändringsflödesprocessorns starttid - Azure Cosmos DB
description: Lär dig hur du konfigurerar ändringsflödesprocessorn för att börja läsa från ett visst datum och en viss tid
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586282"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Så här konfigurerar du starttiden för ändringsmatningsmatningsmataren

I den här artikeln beskrivs hur du kan konfigurera [ändringsflödesbehandlaren](./change-feed-processor.md) så att den börjar läsa från ett visst datum och en viss tid.

## <a name="default-behavior"></a>Standardbeteende

När en ändringsmatningsprocessor startar första gången initieras lånebehållaren och [bearbetningslivscykeln](./change-feed-processor.md#processing-life-cycle)påbörjas. Alla ändringar som inträffade i behållaren innan ändringsfeedprocessorn initierades för första gången identifieras inte.

## <a name="reading-from-a-previous-date-and-time"></a>Läsa från ett tidigare datum och en tidigare tid

Det är möjligt att initiera ändringsflödesbehandlaren för att läsa ändringar som `DateTime` börjar `WithStartTime` vid ett visst datum och en **viss tid**, genom att skicka en instans av en till byggtillägget:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Ändringsfeedprocessorn initieras för det specifika datumet och tiden och börjar läsa de ändringar som inträffade efter.

## <a name="reading-from-the-beginning"></a>Läsa från början

I andra scenarier som datamigreringar eller analys av hela historiken för en behållare måste vi läsa **ändringsflödet**från början av behållarens livstid . För att göra det `WithStartTime` kan vi använda `DateTime.MinValue.ToUniversalTime()`på byggtillägget, men passerar `DateTime` , vilket skulle generera UTC-representationen av minimivärdet, så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Ändringsmatningsbehandlaren initieras och börjar läsa ändringar från början av behållarens livstid.

> [!NOTE]
> De här anpassningsalternativen fungerar bara för att ställa in startpunkten i tid för ändringsmatningsprocessorn. När lånebehållaren har initierats för första gången har det ingen effekt att ändra dem.

> [!NOTE]
> När du anger en tidpunkt läs avser endast ändringar för objekt som för närvarande finns i behållaren. Om ett objekt har tagits bort tas även dess historik i ändringsflödet bort.

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användningsexempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta med att läsa mer om ändringsflödesbehandlare i följande artiklar:

* [Översikt över ändringsmatningsprocessor](change-feed-processor.md)
* [Använda ändringsflödesövervakaren](how-to-use-change-feed-estimator.md)
