---
title: Övervaka Server sidans svars tid för åtgärder i Azure Cosmos DB
description: Lär dig hur du övervakar Server svars tid för åtgärder i Azure Cosmos DB konto eller en behållare. Ägare av ett Azure Cosmos DB konto kan förstå problem med svars tiden på Server sidan med dina Azure Cosmos-konton.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 0f1e6d07afb3b7b4d26081bc9e34ac257b280d0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113914"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Övervaka Server sidans svars tid för åtgärder i en Azure Cosmos DB behållare eller ett konto

Azure Monitor för Azure Cosmos DB ger en mått vy för att övervaka ditt konto och skapa instrument paneler. De Azure Cosmos DB måtten samlas in som standard, men den här funktionen kräver inte att du aktiverar eller konfigurerar något explicit. Måttet på Server sidans svars tid används för att Visa svars tiden på Server sidan för en åtgärd. Azure Cosmos DB tillhandahåller service avtal på mindre än 10 ms för punkt-Läs/skriv-åtgärder med direkt anslutning. För punkt läsnings-och skriv åtgärder beräknas service avtal så som beskrivs i [SLA-dokumentet](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Om du ser ovanligt stor latens för punkt åtgärder som:

* En get-eller Set-åtgärd med partitionsnyckel och ID i direkt läge
* En Läs-eller Skriv åtgärd eller
* En fråga

Du kan leta upp Diagnostic-loggen för att se storleken på de data som returneras. Om du ser en varaktig hög latens för frågor kan du leta upp diagnostikloggar efter storleken på de data som returneras, [genomflödet eller ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) som används, eller antalet sådana åtgärder under en viss period. På så sätt kan du felsöka svars tids problemen på Server sidan.

## <a name="view-the-server-side-latency-metric"></a>Visa fördröjnings mått på Server Sidan

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **övervaka** i det vänstra navigerings fältet och välj **mått**.

   ![Mått fönstret i Azure Monitor](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. I fönstret **mått** > väljer du **en resurs** > väljer den nödvändiga **prenumerationen**och **resurs gruppen**. För **resurs typen**väljer du **Azure Cosmos DB konton**, väljer något av dina befintliga Azure Cosmos-konton och väljer **Använd**.
   
   ![Välj Azure Cosmos DB konto om du vill visa mått](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Välj nästa **svars tid på Server sidan** från listan över tillgängliga mått. Mer information om alla tillgängliga mått i den här listan finns i artikeln [mått per kategori](monitor-cosmos-db-reference.md) . I det här exemplet väljer vi **svars tid på Server sidan** och **Genomsnittligt** som agg regerings värde. Förutom dessa uppgifter kan du också välja **tidsintervallet** och **tids kornig het** för måtten. Som Max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på ditt filter. Du kan se svars tiden på Server sidan per minut för den valda perioden.  

   ![Välj mått på Server sidans svars tid från Azure Portal](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filter för svars tid på Server Sidan

Du kan också filtrera mått och hämta diagram som visas av en speciell **samlings**-, **ConnectionMode**-, **databasename**-, **OperationType**-, **region**-och **PublicAPIType**. 

Om du vill filtrera måtten väljer du **Lägg till filter** och väljer den obligatoriska egenskapen som **PublicAPIType** och väljer värdet **SQL**. Lägg till ett annat filter för **OperationType**. Diagrammet visar sedan svars tiden på Server sidan för olika åtgärder under den valda perioden. De åtgärder som utförs via den lagrade proceduren loggas inte, så de är inte tillgängliga i OperationType-måttet.

**Server sidans svars** värden för varje åtgärd visas enligt följande bild:

![Filter för svars värden på Server Sidan](./media/monitor-server-side-latency/server-side-latency-filters.png)

Du kan också gruppera måtten med hjälp av alternativet **Använd delning** .  

## <a name="next-steps"></a>Nästa steg

* Övervaka Azure Cosmos DB data med hjälp av [diagnostiska inställningar](cosmosdb-monitor-resource-logs.md) i Azure.
* [Granska Azure Cosmos DB kontroll Plans åtgärder](audit-control-plane-logs.md)