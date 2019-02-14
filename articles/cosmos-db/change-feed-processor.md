---
title: Arbeta med ändringsflödet processor-biblioteket i Azure Cosmos DB
description: Med hjälp av Azure Cosmos DB-ändringen feed processor-biblioteket.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: cf03233c6a92b7fd1b782f8128787bfda5582f7d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243203"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processor-ändringsflödet i Azure Cosmos DB 

Den [processor-biblioteket för Azure Cosmos DB-ändringsfeed](sql-api-sdk-dotnet-changefeed.md) hjälper dig att distribuera händelsebearbetning bland olika konsumenter. Det här biblioteket förenklar läsning ändringar i partitioner och flera trådar som arbetar parallellt.

Den största fördelen med biblioteket change feed processor är att du inte behöver hantera varje partition och fortsättningstoken och du behöver inte avsöka varje behållare manuellt.

Biblioteket för change feed processor förenklar läsning ändringar i partitioner och flera trådar som arbetar parallellt. Den hanterar automatiskt läsning ändringar mellan partitioner som använder en mekanism för lånet. Som du ser i följande bild, om du startar två klienter som använder ändringsflödet processor-biblioteket kan de dela upp arbete sinsemellan. När du fortsätter att öka antalet klienter kan behålla de dividera arbete sinsemellan.

![Med hjälp av Azure Cosmos DB ändringen feed processor-biblioteket](./media/change-feed-processor/change-feed-output.png)

Vänstra klienten startades första och det igång med att övervaka alla partitionerna och andra klienten har startats och sedan först släpper några av lån till andra klienter. Det här är ett effektivt sätt att fördela arbetet mellan olika datorer och klienter.

Om du har två server utan Azure functions övervakning samma behållare och använder samma lånet kan kan de två funktionerna få olika dokument, beroende på hur processor-biblioteket bestämmer sig för att bearbeta partitioner.

## <a name="implementing-the-change-feed-processor-library"></a>Genomförs feed processor-biblioteket

Det finns fyra huvudsakliga komponenter för att implementera ändringsflödet processor-biblioteket: 

1. **Övervakade behållaren:** Övervakade behållaren har data som genereras ändringsflöde. Alla infogningar och ändringar till behållaren för övervakade återspeglas i ändringsflödet på behållaren.

1. **Lån-behållaren:** Behållaren lånet samordnar bearbetning ändringsflöden över flera arbetare. En separat behållare används som omfattar lånen med ett lån per partition. Nu är det bra att lagra den här behållaren för lånet på ett annat konto med skrivningsregionen närmare till där ändringsflödet processor körs. Ett lån-objekt innehåller följande attribut:

   * Ägare: Anger den värd som äger lånet.

   * Fortsättning: Anger placeringen (fortsättningstoken) i ändringsflödet för en viss partition.

   * Tidsstämpel: Lånet senast uppdaterades; tidsstämpeln som kan användas för att kontrollera om lånet tas i beaktande har upphört att gälla.

1. **Värd för händelsebearbetning:** Varje värd avgör hur många partitioner för att bearbeta baserat på hur många andra instanser av värdar har aktiva lån.

   * När en värd startas, skaffar lån för att balansera arbetsbelastningen över alla värdar. En värd förnyas regelbundet lån, så lån är aktiva.

   * En värd kontrollpunkter senaste fortsättningstoken att lånet för varje läsa. För att säkerställa säkerheten för samtidighet, kontrollerar en värd ETag för varje uppdatering för lånet. Andra strategier för kontrollpunkt stöds också.

   * Vid avstängning, en värd släpper alla lån men behålls fortsättning-information så att den kan återuppta läsning från den lagrade kontrollpunkten senare.

   För närvarande, får inte antalet värdar vara större än antalet partitioner (lån).

1. **Användare:** Konsumenter eller arbeten är trådar som utför bearbetningen ändringsflödet initieras av varje värd. Varje värd för händelsebearbetning kan ha flera konsumenter. Varje konsument läser ändringen flöde från den partition som den är tilldelad till och meddelar dess värden för ändringar och upphört att gälla lån.

För att bättre förstå hur dessa fyra element i ändringsfeed processor fungerar tillsammans, nu ska vi titta på ett exempel i följande diagram. Övervakade samlingen lagrar dokument och använder ”City” som partitionsnyckel. Vi kan se att den blå partitionen innehåller dokument med fältet ”City” från ”A-E” och så vidare. Det finns två värdar med två konsumenter att läsa från de fyra partitionerna parallellt. Pilarna visar konsumenterna läsning från en specifik plats i den ändringsflödet. I den första partitionen representerar mörkare blå olästa ändringar medan ljusblått representerar redan läsning ändringar på ändringsflöde. Värdarna använda lease-samlingen för att lagra ett värde för ”fortsättning” för att hålla reda på den aktuella läsning positionen för varje konsument.

![Exempel för processor för ändringsfeed](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Ändringsfeed och dataflöde

Du debiteras för RU: er som förbrukas, eftersom data flyttas till och från Cosmos-behållare använder alltid ru: er. Du debiteras för RU: er som används av behållaren lånet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure biblioteket i Cosmos DB change feed processor](sql-api-sdk-dotnet-changefeed.md)
* [NugGet paket](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Ytterligare exempel på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig mer om ändringsfeed i följande artiklar:

* [Översikt över ändringsfeed](change-feed.md)
* [Sätt att läsa ändringsflödet](read-change-feed.md)
* [Med hjälp av ändringen feed med Azure Functions](change-feed-functions.md)
