---
title: Så här granskar du Azure Cosmos DB-kontrollplansåtgärder
description: Lär dig hur du granskar kontrollplansåtgärder som att lägga till en region, uppdatera dataflöde, region redundans, lägga till ett VNet etc. i Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420257"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Så här granskar du Azure Cosmos DB-kontrollplansåtgärder

Kontrollplansåtgärder inkluderar ändringar i Azure Cosmos-kontot eller behållaren. Skapa till exempel ett Azure Cosmos-konto, lägga till en region, uppdatera dataflöde, region redundans, lägga till ett VNet etc. är några av kontrollplansåtgärderna. I den här artikeln beskrivs hur du granskar kontrollplansåtgärderna i Azure Cosmos DB.

## <a name="disable-key-based-metadata-write-access"></a>Inaktivera nyckelbaserad metadataskrivningsåtkomst
 
Innan du granskar kontrollplansåtgärderna i Azure Cosmos DB inaktiverar du den nyckelbaserade metadataskrivningsåtkomsten för ditt konto. När nyckelbaserad metadataskrivningsåtkomst är inaktiverad hindras klienter som ansluter till Azure Cosmos-kontot via kontonycklar från att komma åt kontot. Du kan inaktivera skrivåtkomst `disableKeyBasedMetadataWriteAccess` genom att ange egenskapen så att den är true. När du har angett den här egenskapen kan ändringar av alla resurser ske från en användare med rätt rollbaserad rollkontroll(RBAC) roll och autentiseringsuppgifter. Mer information om hur du anger den här egenskapen finns i artikeln [Förhindra ändringar från SDK:er.](role-based-access-control.md#preventing-changes-from-cosmos-sdk)

 Tänk på följande punkter när du inaktiverar metadataskrivningsåtkomst:

* Utvärdera och se till att dina program inte gör metadataanrop som ändrar ovanstående resurser (till exempel skapa samling, uppdatera dataflöde, ...) med hjälp av SDK- eller kontonycklarna.

* Azure-portalen använder för närvarande kontonycklar för metadataåtgärder och därför kommer dessa åtgärder att blockeras. Du kan också använda malldistributionerna Azure CLI, SDK eller Resource Manager för att utföra sådana åtgärder.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Aktivera diagnostikloggar för styrplansoperationer

Du kan aktivera diagnostikloggar för kontrollplanåtgärder med hjälp av Azure-portalen. Gör så här för att aktivera loggning på kontrollplansoperationer:

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till ditt Azure Cosmos-konto.

1. Öppna fönstret **Diagnostikinställningar** och ange ett **namn** för de loggar som ska skapas.

1. Välj **ControlPlaneRequests** för loggtyp och välj alternativet **Skicka till logganalys.**

Du kan också lagra loggarna i ett lagringskonto eller strömma till en händelsenav. Den här artikeln visar hur du skickar loggar till logganalys och sedan frågar dem. När du har aktiverat det tar det några minuter innan diagnostikloggarna börjar gälla. Alla kontrollplansoperationer som utförs efter den punkten kan spåras. Följande skärmbild visar hur du aktiverar kontrollplansloggar:

![Aktivera loggning av kontrollplansbegäranden](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Visa styrplansoperationer

När du har aktiverat loggning använder du följande steg för att spåra åtgärder för ett visst konto:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Öppna fliken **Bildskärm** från den vänstra navigeringen och välj sedan fönstret **Loggar.** Det öppnar ett användargränssnitt där du enkelt kan köra frågor med det specifika kontot i omfånget. Kör följande fråga för att visa kontrollplanloggar:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Följande skärmbilder fångar loggar när ett VNET läggs till i ett Azure Cosmos-konto:

![Styra planloggar när ett virtuella nätverk läggs till](./media/audit-control-plane-logs/add-ip-filter-logs.png)

Följande skärmbilder fångar loggar när dataflödet i en Cassandra-tabell uppdateras:

![Styra planloggar när dataflödet uppdateras](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identifiera identiteten som är kopplad till en viss åtgärd

Om du vill felsöka ytterligare kan du identifiera en specifik åtgärd i **aktivitetsloggen** med hjälp av aktivitets-ID eller av tidsstämpeln för åtgärden. Tidsstämpel används för vissa Resource Manager-klienter där aktivitets-ID:n inte uttryckligen skickas. Aktivitetsloggen innehåller information om vilken identitet åtgärden initierades med. Följande skärmbild visar hur du använder aktivitets-ID:et och hittar de åtgärder som är associerade med det i aktivitetsloggen:

![Använd aktivitets-ID:t och hitta operationerna](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>Nästa steg

* [Utforska Azure Monitor för Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md)