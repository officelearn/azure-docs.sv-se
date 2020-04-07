---
title: Konfigurera direktuppspelningsexport av mått och resursloggar
description: Lär dig hur du konfigurerar direktuppströmningsexport av mått och resursloggar, inklusive intelligent diagnostikanalys från Azure SQL Database till det mål du väljer för att lagra information om resursanvändning och frågekörningsstatistik.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: 9c9f069ad38c65aa0bbfdcde9eef3fed32585d9e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756411"
---
# <a name="configure-streaming-export-of-azure-sql-database-diagnostic-telemetry"></a>Konfigurera strömmande export av diagnostiktelemetri för Azure SQL Database

I den här artikeln får du lära dig mer om prestandamått och resursloggar för Azure SQL Database som du kan exportera till en av flera mål för analys. Du får lära dig hur du konfigurerar direktuppspelningsexport av den här diagnostiktelemetrin via Azure-portalen, PowerShell, Azure CLI, REST API- och Azure Resource Manager-mallarna.

Du kommer också att lära dig om de destinationer som du kan strömma den här diagnostiska telemetrin och hur du väljer bland dessa val. Dina målalternativ inkluderar:

- [Logganalys och SQL Analytics](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure-lagring](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export-for-azure-sql-database"></a>Diagnostiktelemetri för export för Azure SQL Database

Viktigast bland den diagnostiska telemetri som du kan exportera är Loggen Intelligent Insights (SQLInsights). [Intelligent Insights](sql-database-intelligent-insights.md) använder inbyggd intelligens för att kontinuerligt övervaka databasanvändningen genom artificiell intelligens och upptäcka störande händelser som orsakar dåliga prestanda. När det har identifierats utförs en detaljerad analys som genererar en Intelligent Insights-logg med en intelligent bedömning av problemet. Den här utvärderingen består av en grundorsaksanalys av databasprestandaproblemet och, om möjligt, rekommendationer för prestandaförbättringar. Du måste konfigurera direktuppspelningsexporten av den här loggen för att kunna visa dess innehåll.

Förutom att strömma exporten av intelligenta insikter kan du också exportera en mängd olika prestandamått och ytterligare SQL-databasloggar. I följande tabell beskrivs prestandamått och resursloggar som du kan konfigurera för direktuppspelningsexport till ett av flera mål. Den här diagnostiska telemetrin kan konfigureras för enskilda databaser, elastiska pooler och poolade databaser samt hanterade instanser och instansdatabaser.

| Diagnostisk telemetri för databaser | Stöd för en enda databas och poolad databas | Stöd för hanterad instansdatabas |
| :------------------- | ----- | ----- |
| [Grundläggande mått:](#basic-metrics)Innehåller DTU/CPU-procent, DTU/CPU-gräns, fysisk dataavläsningsprocent, loggskrivningsprocent, Lyckad/Misslyckad/Blockerad av brandväggsanslutningar, sessionsprocent, arbetare procent, lagring, lagringsprocent och XTP-lagringsprocent. | Ja | Inga |
| [Instans och App Advanced:](#advanced-metrics)Innehåller tempdb systemdatabas data och loggfil storlek och tempdb procent loggfil används. | Ja | Inga |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Innehåller information om frågekörningsstatistiken, till exempel PROCESSOR-användning och statistik över frågevaraktighet. | Ja | Ja |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Innehåller information om frågan vänta statistik (vad dina frågor väntade på) sådana är CPU, LOG och LÅSNING. | Ja | Ja |
| [Fel](#errors-dataset): Innehåller information om SQL-fel i en databas. | Ja | Ja |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Innehåller information om hur mycket tid en databas tillbringade väntar på olika vänta typer. | Ja | Inga |
| [Timeout :](#time-outs-dataset)Innehåller information om timeout i en databas. | Ja | Inga |
| [Block](#blockings-dataset): Innehåller information om hur du blockerar händelser i en databas. | Ja | Inga |
| [Dödlägen](#deadlocks-dataset): Innehåller information om dödlägehändelser i en databas. | Ja | Inga |
| [AutomaticTuning](#automatic-tuning-dataset): Innehåller information om automatiska justeringsrekommendationer för en databas. | Ja | Inga |
| [SQLInsights](#intelligent-insights-dataset): Innehåller intelligenta insikter om prestanda för en databas. Mer information finns i [Intelligenta insikter](sql-database-intelligent-insights.md). | Ja | Ja |

> [!NOTE]
> Diagnostikinställningar kan inte konfigureras för **systemdatabaserna**, sådana är huvud-, msdb-, modell-, resurs- och tempdb-databaser.

## <a name="streaming-export-destinations"></a>Strömmande exportdestinationer

Den här diagnostiska telemetrin kan strömmas till en av följande Azure-resurser för analys.

- **[Log Analytics arbetsyta:](#stream-into-sql-analytics)**

  Data som strömmas till en [Log Analytics-arbetsyta](../azure-monitor/platform/resource-logs-collect-workspace.md) kan förbrukas av [SQL Analytics](../azure-monitor/insights/azure-sql.md). SQL Analytics är en molnövervakningslösning som tillhandahåller intelligent övervakning av dina databaser som innehåller prestandarapporter, aviseringar och begränsningsrekommendationer. Data som strömmas till en Log Analytics-arbetsyta kan analyseras med andra övervakningsdata som samlas in och gör det också möjligt för dig att utnyttja andra Azure Monitor-funktioner som aviseringar och visualiseringar
- **[Azure-händelsehubbar:](#stream-into-event-hubs)**

  Data som strömmas till en [Azure Event Hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md)innehåller följande funktioner:

  - **Strömma loggar till loggnings- och telemetrisystem**från tredje part: Strömma alla dina mått och resursloggar till en enda händelsehubb för att leda loggdata till ett SIEM- eller logganalysverktyg från tredje part.
  - **Skapa en anpassad telemetri- och loggningsplattform:** Den mycket skalbara publiceringsprenumerationens natur för händelsehubbar gör att du flexibelt kan använda mått och resursloggar i en anpassad telemetriplattform. Mer information finns i [Designa och dimensionera en telemetriplattform för global skala på Azure Event Hubs.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)
  - **Visa tjänstens hälsotillstånd genom att strömma data till Power BI:** Använd eventhubbar, Stream Analytics och Power BI för att omvandla diagnostikdata till nära realtidsinsikter om dina Azure-tjänster. Se [Stream Analytics och Power BI: En instrumentpanel för analys i realtid för direktuppspelning av data](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) för mer information om den här lösningen.
- **[Azure-lagring:](#stream-into-azure-storage)**

  Data som strömmas till [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) gör att du kan arkivera stora mängder diagnostiktelemetri för en bråkdel av kostnaden för de två tidigare direktuppspelningsalternativen.

Den här diagnostiska telemetri som strömmas till en av dessa mål kan användas för att mäta resursutnyttjande och frågekörningsstatistik för enklare prestandaövervakning.

![Arkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Aktivera och konfigurera direktuppspelningsexport av diagnostiktelemetri

Du kan aktivera och hantera mått och diagnostiktelemetriloggning med någon av följande metoder:

- Azure Portal
- PowerShell
- Azure CLI
- REST-API:et för Azure Monitor
- Azure Resource Manager-mall

> [!NOTE]
> Information om hur du aktiverar direktuppspelning av säkerhetstelemetri finns i [Konfigurera granskning för databasen](sql-database-auditing.md#setup-auditing) och [granskningsloggar i Azure Monitor-loggar och Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Konfigurera direktuppspelningsexport av diagnostiktelemetri

Du kan använda menyn **Diagnostikinställningar** i Azure-portalen för att aktivera och konfigurera direktuppspelning av diagnostiktelemetri. Dessutom kan du använda PowerShell, Azure CLI, [REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)och [Resource Manager-mallar](../azure-monitor/platform/diagnostic-settings-template.md) för att konfigurera direktuppspelning av diagnostiktelemetri. Du kan ange följande mål för att strömma diagnostiktelemetrin: Azure Storage, Azure Event Hubs och Azure Monitor-loggar.

> [!IMPORTANT]
> Direktuppspelningsexport av diagnostiktelemetri är inte aktiverat som standard.

Välj en av följande flikar för steg-för-steg-vägledning för att konfigurera direktuppspelningsexport av diagnostiktelemetri i Azure-portalen och för skript för att utföra samma sak med PowerShell och Azure CLI.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="elastic-pools"></a>Elastiska pooler

Du kan ställa in en elastisk poolresurs för att samla in följande diagnostiska telemetri:

| Resurs | Övervakning av telemetri |
| :------------------- | ------------------- |
| **Elastisk pool** | [Grundläggande mått](sql-database-metrics-diag-logging.md#basic-metrics) innehåller eDTU/CPU-procent, eDTU/CPU-gräns, fysisk dataavläsningsprocent, loggskrivningsprocent, sessionsprocent, arbetares procent, lagring, lagringsprocent, lagringsgräns och XTP-lagringsprocent. |

Om du vill konfigurera direktuppspelning av diagnostisk telemetri för elastiska pooler och poolade databaser måste du konfigurera var och en separat:

- Aktivera direktuppspelning av diagnostisk telemetri för en elastisk pool
- Aktivera direktuppspelning av diagnostisk telemetri för varje databas i elastisk pool

Den elastiska poolbehållaren har en egen telemetri separat från varje enskild poolad databass telemetri.

Så här aktiverar du direktuppspelning av diagnostentel för en elastisk poolresurs:

1. Gå till den **elastiska poolresursen** i Azure-portalen.
2. Välj **diagnostikinställningar**.
3. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller välj Redigera **inställning** för att redigera en tidigare inställning.

   ![Aktivera diagnostik för elastiska pooler](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Ange ett inställningsnamn för din egen referens.
5. Välj en målresurs för data för direktuppspelning av diagnostik: **Arkiv till lagringskonto,** **Strömma till en händelsenav**eller **Skicka till Logganalys**.
6. För logganalys väljer du **Konfigurera** och skapar en ny arbetsyta genom att välja **+Skapa ny arbetsyta**eller välja en befintlig arbetsyta.
7. Markera kryssrutan för elastisk pooldiagnostiktelemetri: **Grundläggande** mått.
   ![Konfigurera diagnostik för elastiska pooler](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

8. Välj **Spara**.
9. Dessutom konfigurerar du direktuppspelning av diagnostisk telemetri för varje databas i den elastiska pool som du vill övervaka genom att följa stegen som beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förutom att konfigurera diagnostisk telemetri för en elastisk pool måste du också konfigurera diagnostisk telemetri för varje databas i den elastiska poolen.

### <a name="single-or-pooled-database"></a>Enkel eller poolad databas

Du kan ställa in en enskild eller poolad databasresurs för att samla in följande diagnostiktelemetri:

| Resurs | Övervakning av telemetri |
| :------------------- | ------------------- |
| **Enkel eller poolad databas** | [Grundläggande mått](sql-database-metrics-diag-logging.md#basic-metrics) innehåller DTU-procent, DTU-använd, DTU-gräns, CPU-procent, fysisk dataavläsningsprocent, loggskrivningsprocent, Lyckad/misslyckad/Blockerad av brandväggsanslutningar, sessionsprocent, arbetare procent, lagring, lagringsprocent, XTP-lagringsprocent och dödlägen. |

Så här aktiverar du direktuppspelning av diagnostiktelemetri för en enda eller en poolad databas:

1. Gå till Azure **SQL-databasresursen.**
2. Välj **diagnostikinställningar**.
3. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller välj Redigera **inställning** för att redigera en tidigare inställning. Du kan skapa upp till tre parallella anslutningar för att strömma diagnostiktelemetri.
4. Välj **Lägg till diagnostikinställning** för att konfigurera parallell direktuppspelning av diagnostikdata till flera resurser.

   ![Aktivera diagnostik för enstaka och poolade databaser](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

5. Ange ett inställningsnamn för din egen referens.
6. Välj en målresurs för data för direktuppspelning av diagnostik: **Arkiv till lagringskonto,** **Strömma till en händelsenav**eller **Skicka till Logganalys**.
7. För standard, händelsebaserad övervakningsupplevelse markerar du följande kryssrutor för databasdiagnostikloggtelemetri: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks**och **Dödlägen**.
8. Om du vill ha en avancerad, en minuts baserad övervakningsupplevelse markerar du kryssrutan för **grundläggande** mått.

   ![Konfigurera diagnostik för databaser med enstaka, poolade eller instanser](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
9. Välj **Spara**.
10. Upprepa dessa steg för varje databas som du vill övervaka.

> [!TIP]
> Upprepa dessa steg för varje enskild och poolad databas som du vill övervaka.

### <a name="managed-instance"></a>Hanterad instans

Du kan ställa in en hanterad instansresurs för att samla in följande diagnostiktelemetri:

| Resurs | Övervakning av telemetri |
| :------------------- | ------------------- |
| **Hanterad instans** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) innehåller vCores antal, genomsnittlig CPU-procent, IO-begäranden, byte läsa/skriftliga byte, reserverat lagringsutrymme och använt lagringsutrymme. |

Om du vill konfigurera direktuppspelning av diagnostiktelemetri för hanterade instans- och instansdatabaser måste du konfigurera var och en:

- Aktivera direktuppspelning av diagnostiktelemetri för hanterad instans
- Aktivera direktuppspelning av diagnostiktelemetri för varje instansdatabas

Behållaren för hanterade instanser har en egen telemetri separat från varje instansdatabas telemetri.

Så här aktiverar du direktuppspelning av diagnostiktelemetri för en hanterad instansresurs:

1. Gå till den **hanterade instansresursen** i Azure-portalen.
2. Välj **diagnostikinställningar**.
3. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller välj Redigera **inställning** för att redigera en tidigare inställning.

   ![Aktivera diagnostik för hanterad instans](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Ange ett inställningsnamn för din egen referens.
5. Välj en målresurs för data för direktuppspelning av diagnostik: **Arkiv till lagringskonto,** **Strömma till en händelsenav**eller **Skicka till Logganalys**.
6. För logganalys väljer du **Konfigurera** och skapar en ny arbetsyta genom att välja **+Skapa ny arbetsyta**eller använda en befintlig arbetsyta.
7. Markera kryssrutan för till exempel diagnostiktelemetri: **ResourceUsageStats**.

   ![Konfigurera diagnostik för hanterad instans](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

8. Välj **Spara**.
9. Dessutom konfigurerar du direktuppspelning av diagnostiktelemetri för varje instansdatabas i den hanterade instans som du vill övervaka genom att följa stegen som beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förutom att konfigurera diagnostiktelemetri för en hanterad instans måste du också konfigurera diagnostiktelemetri för varje instansdatabas.

### <a name="instance-database"></a>Instansdatabas

Du kan ställa in en instansdatabasresurs för att samla in följande diagnostiktelemetri:

| Resurs | Övervakning av telemetri |
| :------------------- | ------------------- |
| **Instansdatabas** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) innehåller vCores antal, genomsnittlig CPU-procent, IO-begäranden, byte läsa/skriftliga byte, reserverat lagringsutrymme och använt lagringsutrymme. |

Så här aktiverar du direktuppspelning av diagnostiktelemetri för en instansdatabas:

1. Gå till **instansdatabasresursen** i hanterad instans.
2. Välj **diagnostikinställningar**.
3. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller välj Redigera **inställning** för att redigera en tidigare inställning.
   - Du kan skapa upp till tre (3) parallella anslutningar för att strömma diagnostiktelemetri.
   - Välj **+Lägg till diagnostikinställning** för att konfigurera parallell direktuppspelning av diagnostikdata till flera resurser.

   ![Aktivera diagnostik för till exempel databaser](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Ange ett inställningsnamn för din egen referens.
5. Välj en målresurs för data för direktuppspelning av diagnostik: **Arkiv till lagringskonto,** **Strömma till en händelsenav**eller **Skicka till Logganalys**.
6. Markera kryssrutorna för databasdiagnostisk telemetri: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**och **Errors**.
   ![Konfigurera diagnostik för databaser till exempel](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
7. Välj **Spara**.
8. Upprepa dessa steg för varje instansdatabas som du vill övervaka.

> [!TIP]
> Upprepa dessa steg för varje instansdatabas som du vill övervaka.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Du kan aktivera statistik och diagnostikloggning med PowerShell.

- Om du vill aktivera lagring av mått och resursloggar i ett lagringskonto använder du det här kommandot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  Lagringskonto-ID:et är resurs-ID för mållagringskontot.

- Om du vill aktivera direktuppspelning av mått och resursloggar till en händelsehubb använder du det här kommandot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Regel-ID för Azure Service Bus är en sträng med det här formatet:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Om du vill aktivera skicka mått och resursloggar till en Log Analytics-arbetsyta använder du det här kommandot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- Du kan hämta resurs-ID för Log Analytics-arbetsytan med hjälp av följande kommando:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Du kan kombinera dessa parametrar om du vill aktivera flera utdataalternativ.

**Så här konfigurerar du flera Azure-resurser**

Om du vill stödja flera prenumerationer använder du PowerShell-skriptet från [Aktivera Azure-resursmått som loggar med PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Ange arbetsyteresurs-ID \<\> $WSID som en parameter `Enable-AzureRMDiagnostics.ps1` när skriptet körs för att skicka diagnostikdata från flera resurser till arbetsytan.

- Om du vill hämta \<arbetsyte-ID:$WSID\> för målet för dina diagnostikdata använder du följande skript:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Ersätt \<subID\> \<med prenumerations-ID:\> RG_NAME med resursgruppsnamnet \<och\> WS_NAME med arbetsytans namn.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan aktivera mått och diagnostikloggning med hjälp av Azure CLI.

> [!IMPORTANT]
> Skript för att aktivera diagnostikloggning stöds för Azure CLI v1.0. Azure CLI v2.0 stöds inte just nu.

- Om du vill aktivera lagring av mått och resursloggar i ett lagringskonto använder du det här kommandot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  Lagringskonto-ID:et är resurs-ID för mållagringskontot.

- Om du vill aktivera direktuppspelning av mått och resursloggar till en händelsehubb använder du det här kommandot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  Service Bus-regel-ID är en sträng med det här formatet:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Om du vill aktivera sändning av mått och resursloggar till en Log Analytics-arbetsyta använder du det här kommandot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Du kan kombinera dessa parametrar om du vill aktivera flera utdataalternativ.

---

## <a name="stream-into-sql-analytics"></a>Strömma till SQL Analytics

SQL Database-mått och resursloggar som strömmas till en Log Analytics-arbetsyta kan förbrukas av Azure SQL Analytics. Azure SQL Analytics är en molnlösning som övervakar prestanda för enskilda databaser, elastiska pooler och poolade databaser och hanterade instanser och instansdatabaser i stor skala och över flera prenumerationer. Det kan hjälpa dig att samla in och visualisera Azure SQL Database prestandamått, och det har inbyggd intelligens för felsökning av prestanda.

![Översikt över Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Installationsöversikt

Du kan övervaka en samling Azure SQL-databaser med Azure SQL Analytics genom att utföra följande steg:

1. Skapa en Azure SQL Analytics-lösning från Azure Marketplace.
2. Skapa en Log Analytics-arbetsyta i lösningen.
3. Konfigurera databaser för att strömma diagnostiktelemetri till arbetsytan.

Du kan konfigurera direktuppspelningsexport av den här diagnostiktelemetrin med hjälp av det inbyggda alternativet **Skicka till logganalys** på fliken Diagnostikinställningar i Azure-portalen. Du kan också aktivera direktuppspelning till en Log Analytics-arbetsyta med hjälp av diagnostikinställningar via [PowerShell-cmdlets,](sql-database-metrics-diag-logging.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry) [Azure CLI,](sql-database-metrics-diag-logging.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry) [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)eller Resource [Manager-mallar](../azure-monitor/platform/diagnostic-settings-template.md).

### <a name="create-an-azure-sql-analytics-resource"></a>Skapa en Azure SQL Analytics-resurs

1. Sök efter Azure SQL Analytics på Azure Marketplace och välj den.

   ![Sök efter Azure SQL Analytics i portalen](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Välj **Skapa** på lösningens översiktsskärm.

3. Fyll i Azure SQL Analytics-formuläret med den ytterligare information som krävs: arbetsytenamn, prenumeration, resursgrupp, plats och prisnivå.

   ![Konfigurera Azure SQL Analytics i portalen](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Välj **OK** för att bekräfta och välj sedan **Skapa**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Konfigurera resursen för att registrera mått och resursloggar

Du måste konfigurera uppspelning av diagnostiktelemetri för enstaka och poolade databaser, elastiska pooler, hanterade instanser och instansdatabaser. Det enklaste sättet att konfigurera var ett resurspostermått är med hjälp av Azure-portalen. Detaljerade steg finns i [Konfigurera direktuppspelningsexport av diagnostiktelemetri](sql-database-metrics-diag-logging.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Använda Azure SQL Analytics för övervakning och avisering

Du kan använda SQL Analytics som en hierarkisk instrumentpanel för att visa dina SQL-databasresurser.

- Mer information om hur du använder Azure SQL Analytics finns i [Övervaka SQL Database med hjälp av SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
- Mer information om hur du konfigurerar aviseringar för i SQL Analytics finns i [Skapa aviseringar för databas, elastiska pooler och hanterade instanser](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Strömma till Event Hubs

Du kan strömma SQL Database-mått och resursloggar till eventhubbar med hjälp av det inbyggda **flödet till ett händelsenavalternativ** i Azure-portalen. Du kan också aktivera servicebussregel-ID genom att använda diagnostikinställningar via PowerShell-cmdlets, Azure CLI eller AZURE Monitor REST API.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Vad du ska göra med mått och resursloggar i eventhubbar

När de valda data har strömmats till eventhubbar är du ett steg närmare att aktivera avancerade övervakningsscenarier. Event Hubs fungerar som ytterdörren för en händelsepipeline. När data har samlats in till en händelsenav kan de omvandlas och lagras med hjälp av en analysleverantör i realtid eller ett lagringskort. Event Hubs frikopplar produktionen av en ström av händelser från förbrukningen av dessa händelser. På så sätt kan evenemangskonsumenter komma åt händelserna enligt sitt eget schema. Mer information om eventhubbar finns i:

- [Vad är Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Du kan använda strömmade mått i händelsehubbar för att:

- **Visa tjänstens hälsotillstånd genom att strömma hot-path-data till Power BI**

  Genom att använda Event Hubs, Stream Analytics och Power BI kan du enkelt omvandla dina mått och diagnostikdata till insikter i nära realtid om dina Azure-tjänster. En översikt över hur du konfigurerar en händelsehubb, bearbetar data med Stream Analytics och använder Power BI som utdata finns i [Stream Analytics och Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Strömma loggar till loggnings- och telemetriströmmar från tredje part**

  Genom att använda streaming av eventhubbar kan du hämta dina mått och resursloggar i olika övervaknings- och logganalyslösningar från tredje part.

- **Skapa en anpassad telemetri- och loggningsplattform**

  Har du redan en specialbyggd telemetriplattform eller funderar på att bygga en? Med eventhubbars mycket skalbara publiceringsprenumeration kan du med böjligt inta mått och resursloggar. Se [Dan Rosanovas guide till att använda Event Hubs i en global telemetriplattform.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="stream-into-azure-storage"></a>Strömma till Azure Storage

Du kan lagra mått och resursloggar i Azure Storage med hjälp av det inbyggda **arkivet till ett lagringskontoalternativ** i Azure-portalen. Du kan också aktivera lagring med hjälp av diagnostikinställningar via PowerShell-cmdlets, Azure CLI eller AZURE Monitor REST API.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schema för mått och resursloggar i lagringskontot

När du har ställt in mått och resursloggar skapas en lagringsbehållare i det lagringskonto som du valde när de första raderna med data är tillgängliga. Blobbars struktur är:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Eller, mer enkelt:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ett blob-namn för grundläggande mått kan till exempel vara:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ett blob-namn för lagring av data från en elastisk pool ser ut som:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Policy för datalagring och prissättning

Om du väljer Event Hubs eller ett lagringskonto kan du ange en bevarandeprincip. Den här principen tar bort data som är äldre än en vald tidsperiod. Om du anger Logganalys beror bevarandeprincipen på den valda prisnivån. I det här fallet kan de tillhandahållna kostnadsfria enheterna för datainmatning möjliggöra fri övervakning av flera databaser varje månad. All förbrukning av diagnostisk telemetri som överstiger de kostnadsfria enheterna kan medföra kostnader.

> [!IMPORTANT]
> Aktiva databaser med tyngre arbetsbelastningar får mer data än inaktiva databaser. Mer information finns i [Prissättning för logganalys](https://azure.microsoft.com/pricing/details/monitor/).

Om du använder Azure SQL Analytics kan du övervaka förbrukningen av datainmatning genom att välja **OMS Workspace** på navigeringsmenyn i Azure SQL Analytics och sedan välja **Användning** och **uppskattade kostnader**.

## <a name="metrics-and-logs-available"></a>Tillgängliga mått och loggar

Övervakning telemetri tillgänglig för enskilda databaser, poolade databaser, elastiska pooler, hanterad instans och instansdatabaser dokumenteras i det här avsnittet i artikeln. Insamlad övervakningstelemetri inuti SQL Analytics kan användas för din egen anpassade analys och programutveckling med hjälp av [Azure Monitor-loggfrågor språk.](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)

### <a name="basic-metrics"></a>Grundläggande mått

Mer information om grundläggande mått per resurs finns i följande tabeller.

> [!NOTE]
> Alternativet Grundläggande mått var tidigare känt som Alla mått. Ändringen som gjordes var endast till namngivninget och det fanns ingen ändring av de övervakade måtten. Den här ändringen initierades för att möjliggöra införande av ytterligare måttkategorier i framtiden.

#### <a name="basic-metrics-for-elastic-pools"></a>Grundläggande mått för elastiska pooler

|**Resurs**|**Mått**|
|---|---|
|Elastisk pool|eDTU-procent, eDTU-använd, eDTU-gräns, CPU-procent, fysisk dataavläsningsprocent, loggskrivningsprocent, sessionsprocent, arbetare procent, lagring, lagringsprocent, lagringsgräns, XTP-lagringsprocent |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Grundläggande mått för enstaka och poolade databaser

|**Resurs**|**Mått**|
|---|---|
|Enkel och poolad databas|DTU-procent, DTU-använd, DTU-gräns, CPU-procent, fysisk dataavläsningsprocent, loggskrivningsprocent, Lyckad/misslyckad/Blockerad av brandväggsanslutningar, sessionsprocent, arbetares procent, lagring, lagringsprocent, XTP-lagringsprocent och dödlägen |

### <a name="advanced-metrics"></a>Avancerade mått

I följande tabell finns information om avancerade mått.

|**Mått**|**Namn på mätdisplay**|**Beskrivning**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL Server-processkärna procent|CPU-användningsprocent för SQL Server-processen, mätt med operativsystemet.|
|sqlserver_process_memory_percent<sup>1</sup> |Procent av SQL Server-processminne|Procentandel av minnesanvändningen för SQL Server-processen, mätt med operativsystemet.|
|tempdb_data_size<sup>2</sup>| Tempdb-datafilstorlek Kilobyte |Tempdb Data filstorlek Kilobytes.|
|tempdb_log_size<sup>2</sup>| Tempdb-loggfilstorlek Kilobyte |Tempdb Loggfil storlek Kilobytes.|
|tempdb_log_used_percent<sup>2</sup>| Tempdb procent logg används |Tempdb procent logg används.|

<sup>1</sup> Det här måttet är tillgängligt för databaser som använder vCore-inköpsmodellen med 2 virtuella kärnor och högre, eller 200 DTU och högre för DTU-baserade inköpsmodeller. 

<sup>2</sup> Det här måttet är tillgängligt för databaser som använder vCore-inköpsmodellen med 2 virtuella kärnor och högre, eller 200 DTU och högre för DTU-baserade inköpsmodeller. Det här måttet är för närvarande inte tillgängligt för storskaliga databaser eller informationslager.

### <a name="basic-logs"></a>Grundläggande loggar

Information om telemetri som är tillgänglig för alla loggar dokumenteras i följande tabeller. Se [diagnostiktelemetri](#diagnostic-telemetry-for-export-for-azure-sql-database) som stöds för att förstå vilka loggar som stöds för en viss databassmak - Azure SQL-engrupp, poolad eller instansdatabas.

#### <a name="resource-usage-stats-for-managed-instances"></a>Resursanvändningsstatistik för hanterade instanser

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: ResourceUsageStats |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: MANAGEDINSTANCES |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på den hanterade instansen |
|ResourceId|Resurs URI |
|SKU_s|SKU för hanterad instans |
|virtual_core_count_s|Antal tillgängliga virtuella kärnor |
|avg_cpu_percent_s|Genomsnittlig CPU-procent |
|reserved_storage_mb_s|Reserverad lagringskapacitet för den hanterade instansen |
|storage_space_used_mb_s|Använt lagringsutrymme på den hanterade instansen |
|io_requests_s|IOPS-antal |
|io_bytes_read_s|IOPS-byte läsa |
|io_bytes_written_s|IOPS byte skrivna |

#### <a name="query-store-runtime-statistics"></a>Statistik över frågelagringskörning

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure |
|TimeGenerated [UTC]|Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: QueryStoreRuntimeStatistics |
|OperationName|Operationens namn. Alltid: QueryStoreRuntimeStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: SERVRAR/DATABASER |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på databasens server |
|ElasticPoolName_s|Namn på den elastiska poolen för databasen, om någon |
|DatabaseName_s|Databasens namn |
|ResourceId|Resurs URI |
|query_hash_s|Fråga hash |
|query_plan_hash_s|Hash för frågeplan |
|statement_sql_handle_s|Uttryck sql handtag |
|interval_start_time_d|Startdatumtidtidsdatum för intervallet i antal fästingar från 1900-1-1 |
|interval_end_time_d|Slutdatumtidsdatum för intervallet i antal fästingar från 1900-1-1 |
|logical_io_writes_d|Totalt antal logiska IO-skrivningar |
|max_logical_io_writes_d|Max antal logiska IO-skrivningar per körning |
|physical_io_reads_d|Totalt antal fysiska IO-läsningar |
|max_physical_io_reads_d|Max antal logiska IO-läsningar per körning |
|logical_io_reads_d|Totalt antal logiska IO-läsningar |
|max_logical_io_reads_d|Max antal logiska IO-läsningar per körning |
|execution_type_d|Typ av körning |
|count_executions_d|Antal körningar av frågan |
|cpu_time_d|Total CPU-tid som förbrukas av frågan i mikroseconds |
|max_cpu_time_d|Max CPU-tid konsument genom en enda körning i mikroseconds |
|dop_d|Summan av grader av parallellism |
|max_dop_d|Max grad av parallellism som används för enstaka utförande |
|rowcount_d|Totalt antal returnerade rader |
|max_rowcount_d|Max antal rader som returneras i en enda körning |
|query_max_used_memory_d|Total mängd minne som används i KB |
|max_query_max_used_memory_d|Max mängd minne som används av en enda körning i KB |
|duration_d|Total körningstid i mikrosekoner |
|max_duration_d|Max körningstid för en enda körning |
|num_physical_io_reads_d|Totalt antal fysiska läsningar |
|max_num_physical_io_reads_d|Max antal fysiska läsningar per körning |
|log_bytes_used_d|Totalt antal loggbyte som används |
|max_log_bytes_used_d|Max antal loggbyte som används per körning |
|query_id_d|ID för frågan i Query Store |
|plan_id_d|ID för planen i Query Store |

Läs mer om [statistikdata för körningsstatistik för Query Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Vänta statistik för fråga store

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure |
|TimeGenerated [UTC]|Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: QueryStoreWaitStatistics |
|OperationName|Operationens namn. Alltid: QueryStoreWaitStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: SERVRAR/DATABASER |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på databasens server |
|ElasticPoolName_s|Namn på den elastiska poolen för databasen, om någon |
|DatabaseName_s|Databasens namn |
|ResourceId|Resurs URI |
|wait_category_s|Kategori av väntan |
|is_parameterizable_s|Är frågan parameteriserbar |
|statement_type_s|Typ av utdrag |
|statement_key_hash_s|Uttalande nyckelhh |
|exec_type_d|Typ av körning |
|total_query_wait_time_ms_d|Total väntetid för frågan i den specifika väntekategorin |
|max_query_wait_time_ms_d|Max väntetid för frågan i enskilda körning på den specifika väntekategorin |
|query_param_type_d|0 |
|query_hash_s|Frågehh-hash i Frågearkivet |
|query_plan_hash_s|Frågehchh i Frågearbeta |
|statement_sql_handle_s|Utdragsreferens i Query Store |
|interval_start_time_d|Startdatumtidtidsdatum för intervallet i antal fästingar från 1900-1-1 |
|interval_end_time_d|Slutdatumtidsdatum för intervallet i antal fästingar från 1900-1-1 |
|count_executions_d|Antal körningar av frågan |
|query_id_d|ID för frågan i Query Store |
|plan_id_d|ID för planen i Query Store |

Läs mer om [statistik över väntarstatistik för Query Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Datauppsättning för fel

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure |
|TimeGenerated [UTC]|Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: Fel |
|OperationName|Operationens namn. Alltid: errorevent |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: SERVRAR/DATABASER |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på databasens server |
|ElasticPoolName_s|Namn på den elastiska poolen för databasen, om någon |
|DatabaseName_s|Databasens namn |
|ResourceId|Resurs URI |
|Meddelande|Felmeddelande i oformaterad text |
|user_defined_b|Är felanvändardefinierad bit |
|error_number_d|Felkod |
|Severity|Felets allvarlighetsgrad |
|state_d|Felets tillstånd |
|query_hash_s|Frågehh för den misslyckade frågan, om den är tillgänglig |
|query_plan_hash_s|Frågeplanhh för den misslyckade frågan, om den är tillgänglig |

Läs mer om [SQL Server-felmeddelanden](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

#### <a name="database-wait-statistics-dataset"></a>Databas vänta statistik datauppsättning

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure |
|TimeGenerated [UTC]|Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: DatabaseWaitStatistics |
|OperationName|Operationens namn. Alltid: DatabaseWaitStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: SERVRAR/DATABASER |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på databasens server |
|ElasticPoolName_s|Namn på den elastiska poolen för databasen, om någon |
|DatabaseName_s|Databasens namn |
|ResourceId|Resurs URI |
|wait_type_s|Namn på väntetypen |
|start_utc_date_t [UTC]|Uppmätt periodstarttid |
|end_utc_date_t [UTC]|Uppmätt periodsluttid |
|delta_max_wait_time_ms_d|Max väntetid per körning |
|delta_signal_wait_time_ms_d|Total signal väntetid |
|delta_wait_time_ms_d|Total väntetid i perioden |
|delta_waiting_tasks_count_d|Antal väntande uppgifter |

Läs mer om [statistik över databasväntarstatistik](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Time-outs datauppsättning

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure |
|TimeGenerated [UTC]|Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: Timeouts |
|OperationName|Operationens namn. Alltid: TimeoutEvent |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: SERVRAR/DATABASER |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på databasens server |
|ElasticPoolName_s|Namn på den elastiska poolen för databasen, om någon |
|DatabaseName_s|Databasens namn |
|ResourceId|Resurs URI |
|error_state_d|Tillståndskod för fel |
|query_hash_s|Frågehhing, om tillgängligt |
|query_plan_hash_s|Frågeplanhh, om tillgängligt |

#### <a name="blockings-dataset"></a>Blockerar datauppsättning

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure |
|TimeGenerated [UTC]|Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: Block |
|OperationName|Operationens namn. Alltid: BlockEvent |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: SERVRAR/DATABASER |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på databasens server |
|ElasticPoolName_s|Namn på den elastiska poolen för databasen, om någon |
|DatabaseName_s|Databasens namn |
|ResourceId|Resurs URI |
|lock_mode_s|Låsläge som används av frågan |
|resource_owner_type_s|Ägare av låset |
|blocked_process_filtered_s|XML-rapport för blockerad process |
|duration_d|Låsets längd i mikroseconds |

#### <a name="deadlocks-dataset"></a>Datauppsättning för dödlägen

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure |
|TimeGenerated [UTC] |Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: Dödlägen |
|OperationName|Operationens namn. Alltid: Dödläge |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: SERVRAR/DATABASER |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på databasens server |
|ElasticPoolName_s|Namn på den elastiska poolen för databasen, om någon |
|DatabaseName_s|Databasens namn |
|ResourceId|Resurs URI |
|deadlock_xml_s|XML för dödlägesrapport |

#### <a name="automatic-tuning-dataset"></a>Automatisk justeringsdatauppsättning

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Alltid: Azure |
|TimeGenerated [UTC]|Tidsstämpel när loggen spelades in |
|Typ|Alltid: AzureDiagnostics |
|ResourceProvider|Resursleverantörens namn. Alltid: MICROSOFT. Sql |
|Kategori|Namnet på kategorin. Alltid: AutomaticTuning |
|Resurs|Namn på resursen |
|ResourceType|Namn på resurstypen. ALLTID: SERVRAR/DATABASER |
|SubscriptionId|Guid för prenumeration för databasen |
|ResourceGroup|Namn på databasens resursgrupp |
|LogicalServerName_s|Namnet på databasens server |
|LogicalDatabaseName_s|Databasens namn |
|ElasticPoolName_s|Namn på den elastiska poolen för databasen, om någon |
|DatabaseName_s|Databasens namn |
|ResourceId|Resurs URI |
|RecommendationHash_s|Unik hash av automatisk justering rekommendation |
|OptionName_s|Automatisk justering |
|Schema_s|Databasschema |
|Table_s|Tabellen påverkas |
|IndexName_s|Indexnamn |
|IndexColumns_s|Kolumnnamn |
|IncludedColumns_s|Kolumner ingår |
|EstimatedImpact_s|Beräknad effekt av automatisk justeringsrekommendation JSON |
|Event_s|Typ av automatisk justeringshändelse |
|Timestamp_t|Senast uppdaterad tidsstämpel |

#### <a name="intelligent-insights-dataset"></a>Intelligent insights-datauppsättning

Läs mer om [loggformatet Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du aktiverar loggning och förstå de mått och loggkategorier som stöds av de olika Azure-tjänsterna finns i:

- [Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Översikt över Azure-plattformsloggar](../azure-monitor/platform/platform-logs-overview.md)

Om du vill veta mer om eventhubbar läser du:

- [Vad är Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Mer information om hur du ställer in aviseringar baserat på telemetri från logganalys finns i:

- [Skapa aviseringar för SQL Database och hanterad instans](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
