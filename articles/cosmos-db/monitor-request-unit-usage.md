---
title: Övervaka dataflödesanvändningen av en åtgärd i Azure Cosmos DB
description: Lär dig hur du övervakar dataflödet eller begär enhetsanvändning av en åtgärd i Azure Cosmos DB. Ägare av ett Azure Cosmos DB-konto kan förstå vilka åtgärder som tar fler begärandeenheter.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115435"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Så här övervakar du dataflöde eller begär enhetsanvändning av en åtgärd i Azure Cosmos DB

Azure Monitor för Azure Cosmos DB ger en måttvy för att övervaka ditt konto och skapa instrumentpaneler. Azure Cosmos DB-måtten samlas in som standard, den här funktionen kräver inte att du aktiverar eller konfigurerar något explicit. Måttet **Totalt antal begärandeenheter** används för att hämta användning av begärandensenheter för olika typer av åtgärder. Senare kan du analysera vilka åtgärder som används mest av dataflödet. Som standard sammanställs dataflödet med en minuts intervall. Du kan dock ändra aggregeringsenheten genom att ändra tidsgranularitetsalternativet.

Det finns två sätt att analysera användningsdata för begäranhet:

* Inom det angivna tidsintervallet som operationer tar fler begärandeenheter.
* Vilka åtgärder i allmänhet dominerar din arbetsbelastning genom att förbruka fler begärandeenheter.
Med den här analysen kan du fokusera på åtgärder som infoga, upsert och titta på deras indexering. Du kan ta reda på om du är över/under indexering av specifika fält och ändrar [indexeringsprincipen](index-policy.md#include-exclude-paths) så att sökvägarna inkluderas eller utesluts.

Om du märker att vissa frågor tar fler begärandeenheter kan du vidta åtgärder som:

* Ompröva om du begär rätt mängd data.
* Ändra frågan om du vill använda index med filtersats.
* Utför billigare UDF-funktionsanrop.
* Definiera partitionsnycklar för att minimera fläkten ur frågan till olika partitioner.
* Du kan också använda frågemåtten som returneras i anropssvaret, diagnostiklogginformationen och referera till [frågeprestandajusteringsartikeln](sql-api-query-metrics.md) för att lära dig mer om frågekörningen.
* Du kan börja från summa och sedan titta på genomsnittlig användning med rätt dimension.

## <a name="view-the-total-request-unit-usage-metric"></a>Visa det totala användningsmåttet för begäranhet

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Övervaka** i det vänstra **navigeringsfältet**och välj Mått .

   ![Fönstret Mått i Azure Monitor](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. Välj **en resurs** > > välja den **prenumeration**och **resursgrupp**som krävs i fönstret **Mått.** För **resurstypen**väljer du **Azure Cosmos DB-konton,** väljer ett av dina befintliga Azure Cosmos-konton och väljer **Använd**.

   ![Välj Azure Cosmos DB-konto för att visa mått](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Välj sedan måttet **Totalt antal begärandenheter** i listan över tillgängliga mått. Mer information om alla tillgängliga mått i den här listan finns i artikeln [Mått efter kategori.](monitor-cosmos-db-reference.md) I det här exemplet ska vi välja **Totalt antal begärandeenheter** och **Genomsnittlig som** aggregeringsvärde. Utöver dessa uppgifter kan du också välja **tidsintervall och** **tidsgranularitet** för måtten. På max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på filtret. Du kan se det genomsnittliga antalet begärandeenheter som förbrukas per minut för den valda perioden.  

   ![Välj ett mått från Azure-portalen](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Filter för användning av begäransenhet

Du kan också filtrera mått och hämta diagrammen som visas av ett visst **CollectionName,** **DatabaseName**, **OperationType**, **Region**, **Status**och **StatusCode**. Med **alternativen Lägg till filter** och Använd **delning** kan du filtrera användning av begärandenhet och gruppera måtten.

Om du vill hämta användningen av begäranheten för varje operation antingen med total(summa) eller medelvärde väljer du **Använd delning** och väljer **Operationstyp** och filtervärdet som visas i följande bild:

   ![Cosmos DB-begäranheter för åtgärder i Azure-övervakaren](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Om du vill visa användning av begäranhet per samling väljer du **Använd delning** och väljer samlingsnamnet som ett filter. Du kommer att se en chatt som följande med ett urval av samlingar inom instrumentpanelen. Du kan sedan välja ett specifikt samlingsnamn för att visa mer information:

   ![Cosmos DB-begäranheter för alla åtgärder av samlingen i Azure-övervakaren](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>Nästa steg

* Övervaka Azure Cosmos DB-data med hjälp av [diagnostikinställningar](cosmosdb-monitor-resource-logs.md) i Azure.
* [Granska Azure Cosmos DB-kontrollplansåtgärder](audit-control-plane-logs.md)