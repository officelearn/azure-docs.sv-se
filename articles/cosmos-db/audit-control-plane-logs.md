---
title: Granska Azure Cosmos DB kontroll Plans åtgärder
description: Lär dig hur du granskar kontroll Plans åtgärder som att lägga till en region, uppdatera data flöde, region växling vid fel, lägga till ett VNet osv. i Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 32dd598b8fc62c0ec68f86f95b02f9f3d98cedd2
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116306"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Granska Azure Cosmos DB kontroll Plans åtgärder

Kontroll planet i Azure Cosmos DB är en RESTful-tjänst som gör det möjligt att utföra en mängd olika åtgärder på Azure Cosmos-kontot. Den visar en offentlig resurs modell (till exempel: databas, konto) och olika åtgärder för slutanvändarna för att utföra åtgärder i resurs modellen. Kontroll Plans åtgärderna omfattar ändringar i Azure Cosmos-kontot eller containern. Till exempel åtgärder som att skapa ett Azure Cosmos-konto, lägga till en region, uppdatera data flöde, regions växling vid fel, lägga till ett VNet osv. är några av kontroll Plans åtgärderna. Den här artikeln beskriver hur du granskar kontroll Plans åtgärder i Azure Cosmos DB. Du kan köra kontroll Plans åtgärder på Azure Cosmos-konton med hjälp av Azure CLI, PowerShell eller Azure Portal, medan du använder Azure CLI eller PowerShell för behållare.

Här följer några exempel på scenarier där gransknings kontroll Plans åtgärder är användbara:

* Du vill få en avisering när brand Väggs reglerna för ditt Azure Cosmos-konto ändras. Aviseringen krävs för att hitta otillåtna ändringar i regler som reglerar nätverks säkerheten för ditt Azure Cosmos-konto och vidta snabb åtgärder.

* Du vill få en avisering om en ny region läggs till eller tas bort från ditt Azure Cosmos-konto. Att lägga till eller ta bort regioner har konsekvenser för fakturerings-och data suveränitets krav. Den här aviseringen hjälper dig att identifiera en oavsiktlig tillägg eller borttagning av en region på ditt konto.

* Du vill ha mer information från diagnostikloggar på vad som har ändrats. Till exempel har ett VNet ändrats.

## <a name="disable-key-based-metadata-write-access"></a>Inaktivera nyckelbaserade metadata skriv åtkomst

Innan du granskar kontroll Plans åtgärderna i Azure Cosmos DB inaktiverar du den nyckelbaserade metadata-Skriv åtkomsten på ditt konto. När Key-baserade metadata skriv åtkomst är inaktive rad förhindras klienter som ansluter till Azure Cosmos-kontot via konto nycklar från åtkomst till kontot. Du kan inaktivera skriv åtkomst genom att ställa `disableKeyBasedMetadataWriteAccess` in egenskapen på True. När du har angett den här egenskapen kan ändringar i alla resurser ske från en användare med en korrekt rollbaserad åtkomst kontroll (RBAC) roll och autentiseringsuppgifter. Mer information om hur du ställer in den här egenskapen finns i artikeln [förhindra ändringar från SDK](role-based-access-control.md#preventing-changes-from-cosmos-sdk) : er. När du har inaktiverat skriv åtkomst fungerar indexet SDK-baserade ändringar i data flödet.

Tänk på följande när du inaktiverar skriv åtkomst till metadata:

* Utvärdera och se till att dina program inte gör några metadata som ändrar ovanstående resurser (till exempel skapa samling, uppdaterings data flöde,...) med hjälp av SDK eller konto nycklar.

* För närvarande kommer Azure Portal att använda konto nycklar för metadata-åtgärder och därmed blockeras dessa åtgärder. Du kan också använda Azure CLI-, SDK-eller Resource Manager-mallens distributioner för att utföra sådana åtgärder.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Aktivera diagnostikloggar för kontroll Plans åtgärder

Du kan aktivera diagnostikloggar för kontroll Plans åtgärder med hjälp av Azure Portal. När den har Aktiver ATS registrerar diagnostikloggar åtgärden som ett par med start-och slut händelser med relevant information. *RegionFailoverStart* och *RegionFailoverComplete* kommer till exempel att slutföra redundansväxlingen av regionen.

Använd följande steg för att aktivera loggning av kontroll Plans åtgärder:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till ditt Azure Cosmos-konto.

1. Öppna fönstret **diagnostiska inställningar** och ange ett **namn** för loggarna som ska skapas.

1. Välj **ControlPlaneRequests** som logg typ och välj alternativet **Skicka till Log Analytics** .

Du kan också lagra loggarna i ett lagrings konto eller en data ström till en händelsehubben. Den här artikeln visar hur du skickar loggar till Log Analytics och frågar dem sedan. När du har aktiverat det tar det några minuter innan diagnostikloggar börjar gälla. Alla kontroll Plans åtgärder som utförs efter den punkten kan spåras. Följande skärm bild visar hur du aktiverar kontroll Plans loggar:

![Aktivera loggning av begär Anden för kontroll plan](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Visa kontroll Plans åtgärder

När du har aktiverat loggning följer du stegen nedan för att spåra åtgärder för ett bestämt konto:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Öppna fliken **övervakning** i den vänstra navigeringen och välj sedan fönstret **loggar** . Det öppnar ett användar gränssnitt där du enkelt kan köra frågor med det aktuella kontot i omfånget. Kör följande fråga för att Visa kontroll Plans loggar:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Följande skärm bilder fångar loggar när ett VNET läggs till i ett Azure Cosmos-konto:

![Kontroll Plans loggar när ett VNet läggs till](./media/audit-control-plane-logs/add-ip-filter-logs.png)

Följande skärm bilder fångar in loggar när data flödet i en Cassandra-tabell uppdateras:

![Kontrol lera plan loggar när data flödet uppdateras](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identifiera den identitet som är kopplad till en speciell åtgärd

Om du vill felsöka ytterligare kan du identifiera en åtgärd i **aktivitets loggen** med hjälp av aktivitets-ID eller tidsstämpel för åtgärden. Timestamp används för vissa Resource Manager-klienter där aktivitets-ID: t inte uttryckligen skickas. Aktivitets loggen innehåller information om den identitet som åtgärden initierades med. Följande skärm bild visar hur du använder aktivitets-ID: t och hittar de åtgärder som är kopplade till den i aktivitets loggen:

![Använd aktivitets-ID och hitta åtgärderna](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Kontroll Plans åtgärder för Azure Cosmos-konto

Följande är de kontroll Plans åtgärder som finns tillgängliga på konto nivå. De flesta åtgärder spåras på konto nivå. Dessa åtgärder är tillgängliga som mått i Azure Monitor:

* Region tillagt
* Region borttagen
* Kontot har tagits bort
* Regionen har redundansväxlats
* Kontot har skapats
* Virtuellt nätverk borttaget
* Kontots nätverks inställningar har uppdaterats
* Kontots replikeringsinställningar har uppdaterats
* Konto nycklar har uppdaterats
* Inställningarna för säkerhets kopiering av kontot har uppdaterats
* Inställningarna för konto diagnostik har uppdaterats

## <a name="control-plane-operations-for-database-or-containers"></a>Kontroll Plans åtgärder för databas eller behållare

Följande är de kontroll Plans åtgärder som finns tillgängliga på databas-och behållar nivån. Dessa åtgärder är tillgängliga som mått i Azure Monitor:

* SQL Database uppdaterad
* SQL-behållare har uppdaterats
* SQL Database data flöde uppdaterat
* SQL container data flöde uppdaterat
* SQL Database borttagen
* SQL-behållare borttagen
* Cassandra-disk utrymme uppdaterat
* Cassandra-tabellen har uppdaterats
* Cassandra-dataflöde har uppdaterats
* Cassandra tabell data flöde har uppdaterats
* Cassandra-tecken utrymme borttaget
* Cassandra-tabellen har tagits bort
* Gremlin-databasen har uppdaterats
* Gremlin-diagrammet har uppdaterats
* Gremlin Database-genomflöde har uppdaterats
* Gremlin Graph-genomflöde har uppdaterats
* Gremlin-databasen har tagits bort
* Gremlin Graph borttagen
* Mongo-databasen har uppdaterats
* Mongo-samlingen har uppdaterats
* Mongo Database-genomflöde har uppdaterats
* Mongo Collection-genomflöde har uppdaterats
* Mongo-databasen har tagits bort
* Mongo-samlingen har tagits bort
* AzureTable-tabellen har uppdaterats
* AzureTable tabell data flöde har uppdaterats
* AzureTable-tabellen har tagits bort

## <a name="diagnostic-log-operations"></a>Diagnostiska logg åtgärder

Följande är åtgärds namnen i diagnostikloggar för olika åtgärder:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

För API-speciella åtgärder heter åtgärden med följande format:

* ApiKind + ApiKindResourceType + OperationType + start/Complete
* ApiKind + ApiKindResourceType + "data flöde" + operationType + start/Complete

**Exempel** 

* CassandraKeyspacesUpdateStart, CassandraKeyspacesUpdateComplete
* CassandraKeyspacesThroughputUpdateStart, CassandraKeyspacesThroughputUpdateComplete

Egenskapen *ResourceDetails* innehåller hela resurs bröd texten som en begäran om nytto last och innehåller alla egenskaper som begärs för uppdatering

## <a name="next-steps"></a>Nästa steg

* [Utforska Azure Monitor för Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md)
