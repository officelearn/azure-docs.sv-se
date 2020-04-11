---
title: Övervaka svarstiden på serversidan för åtgärder i Azure Cosmos DB
description: Lär dig hur du övervakar serversvarstid för åtgärder i Azure Cosmos DB-konto eller en behållare. Ägare av ett Azure Cosmos DB-konto kan förstå svarstidsproblemen på serversidan med dina Azure Cosmos-konton.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 0f1e6d07afb3b7b4d26081bc9e34ac257b280d0f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113914"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Övervaka svarstiden på serversidan för åtgärder i en Azure Cosmos DB-behållare eller -konto

Azure Monitor för Azure Cosmos DB ger en måttvy för att övervaka ditt konto och skapa instrumentpaneler. Azure Cosmos DB-måtten samlas in som standard, den här funktionen kräver inte att du aktiverar eller konfigurerar något explicit. Måttet på svarstiden på serversidan används för att visa svarstiden på serversidan för en åtgärd. Azure Cosmos DB tillhandahåller SLA på mindre än 10 ms för punktläsning/skrivåtgärder med direkt anslutning. För punktläsnings- och skrivåtgärder beräknas SLA:erna som detaljerade i [SLA-dokumentet](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Om du ser ovanligt stor latens för punktåtgärder som:

* En get eller set-åtgärd med partitionsnyckel och ID i direktläge
* En läs- eller skrivåtgärd eller
* En fråga

Du kan slå upp diagnostikloggen för att se storleken på data som returneras. Om du ser en ihållande hög latens för frågeåtgärder kan du söka efter storleken på data som returneras, [dataflöde eller RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) som används, eller antalet sådana åtgärder under en viss period. På så sätt kan du felsöka svarstidsproblem på serversidan.

## <a name="view-the-server-side-latency-metric"></a>Visa svarstiden på serversidan

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Övervaka** i det vänstra **navigeringsfältet**och välj Mått .

   ![Fönstret Mått i Azure Monitor](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. Välj **en resurs** > > välja den **prenumeration**och **resursgrupp**som krävs i fönstret **Mått.** För **resurstypen**väljer du **Azure Cosmos DB-konton,** väljer ett av dina befintliga Azure Cosmos-konton och väljer **Använd**.
   
   ![Välj Azure Cosmos DB-konto för att visa mått](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Välj sedan måttet **Svarstid för serversidan** i listan över tillgängliga mått. Mer information om alla tillgängliga mått i den här listan finns i artikeln [Mått efter kategori.](monitor-cosmos-db-reference.md) I det här exemplet ska vi välja **Svarstid på serversidan** och **Genomsnittlig** som aggregeringsvärde. Utöver dessa uppgifter kan du också välja **tidsintervall och** **tidsgranularitet** för måtten. På max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på filtret. Du kan se svarstiden på serversidan per minut för den valda perioden.  

   ![Välj svarstidsmåttet på serversidan från Azure-portalen](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filter för svarstid på serversidan

Du kan också filtrera mått och hämta diagrammen som visas av ett visst **CollectionName,** **ConnectionMode**, **DatabaseName**, **OperationType**, **Region**och **PublicAPIType**. 

Om du vill filtrera måtten väljer du **Lägg till filter** och väljer den egenskap som krävs, till exempel **PublicAPIType,** och väljer värde **sql**. Lägg till ytterligare ett filter för **OperationType**. Diagrammet visar sedan svarstiden på serversidan för olika åtgärder under den valda perioden. De åtgärder som utförs via lagrad procedur loggas inte så att de inte är tillgängliga under OperationType-måttet.

Svarstidsmåtten **serversida** för varje åtgärd visas enligt följande bild:

![Filter för svarstider på serversidan](./media/monitor-server-side-latency/server-side-latency-filters.png)

Du kan också gruppera måtten med alternativet **Använd delning.**  

## <a name="next-steps"></a>Nästa steg

* Övervaka Azure Cosmos DB-data med hjälp av [diagnostikinställningar](cosmosdb-monitor-resource-logs.md) i Azure.
* [Granska Azure Cosmos DB-kontrollplansåtgärder](audit-control-plane-logs.md)