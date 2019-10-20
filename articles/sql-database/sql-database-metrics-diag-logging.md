---
title: Azure SQL Database mått och diagnostisk loggning | Microsoft Docs
description: Lär dig hur du aktiverar diagnostik i Azure SQL Database för att lagra information om resursutnyttjande och statistik för körning av frågor.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/21/2019
ms.openlocfilehash: 235cdff1297b840bfd1a522e265633b47094c855
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597972"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database mått och diagnostikloggning

I det här avsnittet får du lära dig hur du konfigurerar loggning av Diagnostics-telemetri för Azure SQL Database via mallen Azure Portal, PowerShell, Azure CLI, Azure Monitor REST API och Azure Resource Manager. Diagnostiken kan användas för att mäta resursutnyttjande och köra statistik för frågekörning.

Enskilda databaser, databaser i pooler i elastiska pooler och instans databaser i en hanterad instans kan strömma mått och diagnostikloggar för enklare prestanda övervakning. Du kan konfigurera en databas för att överföra resursanvändning, arbetare och sessioner samt anslutning till någon av följande Azure-resurser:

- **Azure SQL-analys**: för att få intelligent övervakning av dina Azure SQL-databaser som innehåller prestanda rapporter, varningar och rekommendationer.
- **Azure Event Hubs**: för att integrera SQL Database telemetri med dina anpassade övervaknings lösningar eller frekventa pipeliner.
- **Azure Storage**: för att arkivera stora mängder telemetri för en bråkdel av priset.

    ![Arkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

Mer information om mått och logg kategorier som stöds av de olika Azure-tjänsterna finns i:

- [Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Översikt över Azure Diagnostics-loggar](../azure-monitor/platform/resource-logs-overview.md)

Den här artikeln innehåller rikt linjer för hur du aktiverar telemetri för Azure SQL-databaser, elastiska pooler och hanterade instanser. Det kan också hjälpa dig att förstå hur du konfigurerar Azure SQL-analys som ett övervaknings verktyg för att Visa telemetri för Database Diagnostics.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Aktivera loggning av telemetri för diagnostik

Du kan aktivera och hantera loggning av mått och telemetri genom att använda någon av följande metoder:

- Azure portal
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager-mall

När du aktiverar mått-och diagnostikloggning måste du ange Azure-resursens mål för att samla in diagnostik-telemetri. De tillgängliga alternativen är:

- Azure SQL-analys
- Azure Event Hubs
- Azure Storage

Du kan etablera en ny Azure-resurs eller välja en befintlig resurs. När du har valt en resurs med hjälp av alternativet **diagnostiska inställningar** anger du vilka data som ska samlas in.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Diagnostisk loggning som stöds för Azure SQL-databaser och instans databaser

Aktivera mått och diagnostik-loggning på SQL-databaser – de är inte aktiverade som standard.

Du kan konfigurera Azure SQL-databaser och instans databaser för att samla in följande diagnostik:

| Övervaka telemetri för databaser | Stöd för enkel databas och poolad databas | Stöd för instans databas |
| :------------------- | ----- | ----- |
| [Grundläggande mått](#basic-metrics): innehåller DTU/CPU-procent, DTU/CPU-gräns, fysisk data läsning i procent, logg skrivnings procent, lyckad/misslyckad/blockerad av brand Väggs anslutningar, procent andel av arbets tagare, lagring, lagrings procent och XTP lagrings procent. | Ja | Nej |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): innehåller information om frågans körnings statistik, till exempel processor användning och statistik över fråge varaktighet. | Ja | Ja |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): innehåller information om frågan vänta i statistik (vad dina frågor väntar på), t. ex. CPU, logg och låsning. | Ja | Ja |
| [Fel](#errors-dataset): innehåller information om SQL-fel på en databas. | Ja | Ja |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): innehåller information om hur lång tid en databas har använt för att vänta på olika vänte typer. | Ja | Nej |
| [Timeout](#time-outs-dataset): innehåller information om tids gränser för en databas. | Ja | Nej |
| [Block](#blockings-dataset): innehåller information om hur du blockerar händelser i en databas. | Ja | Nej |
| [Död lägen](#deadlocks-dataset): innehåller information om deadlock-händelser på en databas. | Ja | Nej |
| [AutomaticTuning](#automatic-tuning-dataset): innehåller information om automatiska justerings rekommendationer för en databas. | Ja | Nej |
| [SQLInsights](#intelligent-insights-dataset): innehåller intelligent Insights till prestanda för en databas. Läs mer i [intelligent Insights](sql-database-intelligent-insights.md). | Ja | Ja |

> [!IMPORTANT]
> Elastiska pooler och hanterade instanser har egna separata telemetri från databaser som de innehåller. Detta är viktigt för att Observera att Diagnostics-telemetri konfigureras separat för var och en av dessa resurser, enligt beskrivningen nedan.

> [!NOTE]
> Det går inte att aktivera säkerhets gransknings-och SQLSecurityAuditEvents-loggar från databasens diagnostikinställningar (även om de visas på skärmen). Om du vill aktivera Gransknings logg strömning, se [Konfigurera granskning för din databas](sql-database-auditing.md#subheading-2)och [gransknings loggar i Azure Monitor loggar och Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>Azure portal

Du kan använda menyn **diagnostikinställningar** för varje enskild, poolad eller instans databas i Azure Portal för att konfigurera strömning av telemetri. Dessutom kan diagnostisk telemetri också konfigureras separat för databas behållare: elastiska pooler och hanterade instanser. Du kan ställa in följande destinationer för att strömma telemetri för diagnostik: Azure Storage, Azure Event Hubs och Azure Monitor loggar.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Konfigurera strömning av telemetri för elastiska pooler

   ![Ikon för elastisk pool](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Du kan konfigurera en resurs för elastisk pool för att samla in följande diagnostik:

| Resurs | Övervaka telemetri |
| :------------------- | ------------------- |
| **Elastisk pool** | [Basic-mått](sql-database-metrics-diag-logging.md#basic-metrics) innehåller EDTU/CPU-procent, EDTU/CPU-gräns, fysisk data Läs procent, logg skrivnings procent, sessioner, procent andel, lagring, lagrings utrymme, lagrings gräns och XTP lagrings procent. |

Om du vill konfigurera strömning av telemetri för elastiska pooler och databaser i elastiska pooler måste du konfigurera **båda följande två** separata:

- Aktivera strömning av Diagnostics-telemetri för en elastisk pool **och**
- Aktivera strömning av Diagnostics-telemetri för varje databas i elastisk pool

Detta beror på att elastisk pool är en databas behållare med en egen telemetri som skiljs från en enskild databas telemetri.

Följ dessa steg om du vill aktivera strömning av telemetri för en elastisk pool-resurs:

1. Gå till resursen för **elastisk pool** i Azure Portal.
1. Välj **diagnostikinställningar**.
1. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller Välj **Redigera inställning** för att redigera en föregående inställning.

   ![Aktivera diagnostik för elastiska pooler](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Ange ett inställnings namn för din egen referens.
1. Välj en mål resurs för data för strömmande diagnostik: **arkivera till lagrings konto**, **strömma till en Event Hub**eller **Skicka till Log Analytics**.
1. För Log Analytics väljer du **Konfigurera** och skapa en ny arbets yta genom att välja **+ Skapa ny arbets yta**eller Välj en befintlig arbets yta.
1. Markera kryss rutan för telemetri för Elastic pool-diagnostik: **grundläggande** mått.
   ![Configure diagnostik för elastiska pooler ](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Välj **Spara**.
1. Dessutom kan du konfigurera strömning av telemetri för varje databas i den elastiska pool som du vill övervaka genom att följa stegen som beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förutom att konfigurera telemetri för en elastisk pool måste du också konfigurera telemetri för varje databas i elastisk pool enligt beskrivningen nedan.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Konfigurera strömning av telemetri för en enskild databas eller databas i elastisk pool

   ![SQL Database ikon](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Följ dessa steg om du vill aktivera strömning av diagnostik för enskilda databaser eller databaser i pooler:

1. Gå till Azure **SQL Database** -resurs.
1. Välj **diagnostikinställningar**.
1. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller Välj **Redigera inställning** för att redigera en föregående inställning.
   - Du kan skapa upp till tre parallella anslutningar till Stream Diagnostics-telemetri.
   - Välj **+ Lägg till diagnostisk inställning** för att konfigurera parallell strömning av diagnostikdata till flera resurser.

   ![Aktivera diagnostik för enkel-, pool-eller instans databaser](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Ange ett inställnings namn för din egen referens.
1. Välj en mål resurs för data för strömmande diagnostik: **arkivera till lagrings konto**, **strömma till en Event Hub**eller **Skicka till Log Analytics**.
1. För den händelsebaserade övervaknings upplevelsen markerar du följande kryss rutor för Database Diagnostics-loggen telemetri: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** , **Fel**, **DatabaseWaitStatistics**, **tids gränser**, **block**och **död lägen**.
1. För en avancerad övervaknings upplevelse med en minut markerar du kryss rutan för **grundläggande** mått.
   ![Configure diagnostik för enskilda databaser eller instans databaser ](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Välj **Spara**.
1. Upprepa de här stegen för varje databas som du vill övervaka.

> [!NOTE]
> Det går inte att aktivera säkerhets gransknings-och SQLSecurityAuditEvents-loggar från databasens diagnostikinställningar (även om de visas på skärmen). Om du vill aktivera Gransknings logg strömning, se [Konfigurera granskning för din databas](sql-database-auditing.md#subheading-2)och [gransknings loggar i Azure Monitor loggar och Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).
> [!TIP]
> Upprepa de här stegen för varje Azure SQL Database som du vill övervaka.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Konfigurera strömning av telemetri för hanterade instanser

   ![Ikon för hanterad instans](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Du kan konfigurera en hanterad instans resurs för att samla in följande diagnostik:

| Resurs | Övervaka telemetri |
| :------------------- | ------------------- |
| **Hanterad instans** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) innehåller antal virtuella kärnor, genomsnittlig CPU-procent, IO-begäranden, byte, lästa/skrivna, reserverat lagrings utrymme och använt lagrings utrymme. |

Om du vill konfigurera strömning av telemetri för hanterade instanser och instans databaser måste du konfigurera båda följande **två** separata:

- Aktivera strömning av telemetri för hanterade instanser **och**
- Aktivera strömning av telemetri för varje instans databas

Detta beror på att den hanterade instansen är en databas behållare med sin egen telemetri, separat från en enskild instans databas telemetri.

Följ dessa steg om du vill aktivera strömning av telemetri för en hanterad instans resurs:

1. Gå till resursen för **hanterade instanser** i Azure Portal.
1. Välj **diagnostikinställningar**.
1. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller Välj **Redigera inställning** för att redigera en föregående inställning.

   ![Aktivera diagnostik för hanterad instans](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Ange ett inställnings namn för din egen referens.
1. Välj en mål resurs för data för strömmande diagnostik: **arkivera till lagrings konto**, **strömma till en Event Hub**eller **Skicka till Log Analytics**.
1. För Log Analytics väljer du **Konfigurera** och skapa en ny arbets yta genom att välja **+ Skapa ny arbets yta**eller använda en befintlig arbets yta.
1. Markera kryss rutan för telemetri för instansen diagnostik: **ResourceUsageStats**.
   ![Configure diagnostik för hanterad instans ](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Välj **Spara**.
1. Konfigurera dessutom strömning av telemetri för varje instans databas i den hanterade instans som du vill övervaka genom att följa stegen som beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förutom att konfigurera telemetri för en hanterad instans måste du också konfigurera telemetri för varje instans databas enligt beskrivningen nedan.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Konfigurera strömning av telemetri för instans databaser

   ![Ikonen instans databas i hanterad instans](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Följ dessa steg om du vill aktivera strömning av telemetri för instans databaser:

1. Gå till **instans databas** resursen inom en hanterad instans.
1. Välj **diagnostikinställningar**.
1. Välj **Aktivera diagnostik** om det inte finns några tidigare inställningar eller Välj **Redigera inställning** för att redigera en föregående inställning.
   - Du kan skapa upp till tre (3) parallella anslutningar till Stream Diagnostics-telemetri.
   - Välj **+ Lägg till diagnostisk inställning** för att konfigurera parallell strömning av diagnostikdata till flera resurser.

   ![Aktivera diagnostik för instans databaser](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Ange ett inställnings namn för din egen referens.
1. Välj en mål resurs för data för strömmande diagnostik: **arkivera till lagrings konto**, **strömma till en Event Hub**eller **Skicka till Log Analytics**.
1. Markera kryss rutorna för telemetri för databas diagnostik: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** och **errors**.
   ![Configure diagnostik för instans databaser ](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Välj **Spara**.
1. Upprepa de här stegen för varje instans databas som du vill övervaka.

> [!TIP]
> Upprepa de här stegen för varje instans databas som du vill övervaka.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Du kan aktivera mått och diagnostikloggning genom att använda PowerShell.

- Om du vill aktivera lagring av diagnostikloggar i ett lagrings konto använder du följande kommando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Lagrings kontots ID är resurs-ID för mål lagrings kontot.

- Använd följande kommando för att aktivera strömning av diagnostikloggar till en Event Hub:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus regelns ID är en sträng med det här formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Om du vill aktivera sändning av diagnostikloggar till en Log Analytics arbets yta använder du följande kommando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Du kan hämta resurs-ID: t för din Log Analytics-arbetsyta med hjälp av följande kommando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

### <a name="to-configure-multiple-azure-resources"></a>Konfigurera flera Azure-resurser

Använd PowerShell-skriptet från [Aktivera Azure Resource Metric-loggning med PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/)för att stödja flera prenumerationer.

Ange arbets ytans resurs-ID \< $WSID \> som en parameter när du kör skriptet `Enable-AzureRMDiagnostics.ps1` för att skicka diagnostikdata från flera resurser till arbets ytan.

- Använd följande skript för att hämta arbetsyte-ID \< $WSID \> av målet för dina diagnostikdata:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Ersätt \<subID \> med prenumerations-ID: t \<RG_NAME \> med resurs gruppens namn och \<WS_NAME \> med namnet på arbets ytan.

### <a name="azure-cli"></a>Azure CLI

Du kan aktivera mått och diagnostikloggning genom att använda Azure CLI.

> [!NOTE]
> Skript för att aktivera diagnostikloggning stöds för Azure CLI v 1.0. Observera att CLI v 2.0 inte stöds för tillfället.

- Om du vill aktivera lagring av diagnostikloggar i ett lagrings konto använder du följande kommando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Lagrings kontots ID är resurs-ID för mål lagrings kontot.

- Använd följande kommando om du vill aktivera strömning av diagnostikloggar till en Event Hub:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Service Bus regelns ID är en sträng med det här formatet:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Om du vill aktivera sändning av diagnostikloggar till en Log Analytics arbets yta använder du följande kommando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

### <a name="rest-api"></a>REST-API

Läs om hur du [ändrar diagnostikinställningar genom att använda Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Resource Manager-mall

Läs om hur du [aktiverar diagnostikinställningar när du skapar en resurs med hjälp av en Resource Manager-mall](../azure-monitor/platform/diagnostic-settings-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Strömma till Azure SQL-analys

Azure SQL-analys är en moln lösning som övervakar prestanda för Azure SQL-databaser, elastiska pooler och hanterade instanser i stor skala och över flera prenumerationer. Det kan hjälpa dig att samla in och visualisera Azure SQL Database prestanda mått och har inbyggd intelligens för fel sökning av prestanda.

![Översikt över Azure SQL-analys](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database mått och diagnostikloggar kan strömmas till Azure SQL-analys med hjälp av det inbyggda alternativet **Skicka till Log Analytics** på fliken diagnostikinställningar i portalen. Du kan också aktivera Log Analytics genom att använda en diagnostisk inställning via PowerShell-cmdletar, Azure CLI eller Azure Monitor REST API.

### <a name="installation-overview"></a>Installationsöversikt

Du kan övervaka en SQL Database-flotta med Azure SQL-analys. Utför följande steg:

1. Skapa en Azure SQL-analys-lösning från Azure Marketplace.
2. Skapa en arbets yta för övervakning i lösningen.
3. Konfigurera databaser för att strömma telemetri till arbets ytan.

Om du använder elastiska pooler eller hanterade instanser måste du också konfigurera strömning från dessa resurser.

### <a name="create-azure-sql-analytics-resource"></a>Skapa Azure SQL-analys resurs

1. Sök efter Azure SQL-analys på Azure Marketplace och markera det.

   ![Sök efter Azure SQL-analys i portalen](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Välj **skapa** på lösningens översikts skärm.

3. Fyll i Azure SQL-analys formuläret med ytterligare information som krävs: arbets ytans namn, prenumeration, resurs grupp, plats och pris nivå.

   ![Konfigurera Azure SQL-analys i portalen](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Bekräfta genom att klicka på **OK** och välj sedan **skapa**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurera databaser för att registrera mått och diagnostikloggar

Det enklaste sättet att konfigurera var databaser registrerar mått är genom att använda Azure Portal. Som tidigare beskrivits går du till din SQL Database-resurs i Azure Portal och väljer **diagnostikinställningar**.

Om du använder elastiska pooler eller hanterade instanser, måste du också konfigurera diagnostikinställningar i dessa resurser för att aktivera telemetri för att strömma till arbets ytan.

### <a name="use-the-sql-analytics-solution-for-monitoring-and-alerting"></a>Använd SQL Analytics-lösningen för övervakning och avisering

Du kan använda SQL Analytics som en hierarkisk instrument panel för att visa dina SQL Database-resurser.

- Information om hur du använder SQL Analytics-lösningen finns i [övervaka SQL Database med hjälp av SQL Analytics-lösningen](../log-analytics/log-analytics-azure-sql.md).
- Information om hur du ställer in aviseringar för SQL Database och hanterad instans baserat på SQL Analytics finns i [skapa aviseringar för SQL Database och hanterad instans](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Strömma till Event Hubs

Du kan strömma SQL Database mått och diagnostikloggar till Event Hubs genom att använda den inbyggda **strömmen till ett** alternativ för händelsehubben i Azure Portal. Du kan också aktivera ID för Service Bus regel med hjälp av en diagnostisk inställning via PowerShell-cmdletar, Azure CLI eller Azure Monitor REST API.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Vad du kan göra med mått och diagnostikloggar i Event Hubs

När de valda data strömmas i Event Hubs är du ett steg närmare att aktivera avancerade övervaknings scenarier. Event Hubs fungerar som en front dörr för en händelse pipeline. När data har samlats in i en händelsehubben kan de omvandlas och lagras med hjälp av en analys av real tids analys eller ett lagrings kort. Event Hubs frikopplas produktionen av en data ström med händelser från användningen av dessa händelser. På så sätt kan händelse konsumenter komma åt händelserna i sitt eget schema. Mer information om Event Hubs finns i:

- [Vad är Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Du kan använda strömmade mått i Event Hubs för att:

- **Visa tjänstens hälsa genom att strömma frekventa data till Power BI**. Genom att använda Event Hubs, Stream Analytics och Power BI kan du enkelt omvandla dina mått och diagnostikdata till nära real tids insikter om dina Azure-tjänster. En översikt över hur du konfigurerar en Event Hub, bearbetar data med Stream Analytics och använder Power BI som utdata finns i [Stream Analytics och Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Strömma loggar till data strömmar och telemetri från tredje part**. Genom att använda Event Hubs strömning kan du hämta mått och diagnostikloggar till olika övervaknings-och Log Analytics-lösningar från tredje part.

- **Bygg en anpassad telemetri-och loggnings plattform**. Har du redan en egen inbyggd telemetri plattform eller funderar du på att skapa en? Med den mycket skalbara publicerings prenumerations typen för Event Hubs kan du samla in diagnostikloggar på ett flexibelt sätt. Se [Dan Rosanovas Guide to using Event Hubs in the Global-Scale-plattform för telemetri](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Strömma till lagring

Du kan lagra SQL Database mått och diagnostikloggar i Azure Storage genom att använda det inbyggda **arkivet till ett lagrings konto** i Azure Portal. Du kan också aktivera lagring med hjälp av en diagnostisk inställning via PowerShell-cmdletar, Azure CLI eller Azure Monitor REST API.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schema för mått och diagnostikloggar i lagrings kontot

När du har konfigurerat mått och diagnostik loggar samling skapas en lagrings behållare i det lagrings konto som du valde när de första raderna med data är tillgängliga. BLOB-strukturen är:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Eller mer enkelt:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Till exempel kan ett BLOB-namn för grundläggande mått vara:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ett BLOB-namn för att lagra data från en elastisk pool ser ut så här:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Princip och prissättning för data lagring

Om du väljer Event Hubs eller ett lagrings konto kan du ange en bevarande princip. Den här principen tar bort data som är äldre än en vald tids period. Om du anger Log Analytics, är bevarande principen beroende av den valda pris nivån. I det här fallet kan de tillhandahållna kostnads fria enheterna av data inhämtningen möjliggöra kostnads fri övervakning av flera databaser varje månad. All användning av diagnostisk telemetri som överstiger de kostnads fria enheterna kan medföra kostnader. Tänk på att aktiva databaser med tyngre arbets belastningar inhämtar mer data än inaktiva databaser. Mer information finns i [priser för Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Om du använder Azure SQL-analys kan du övervaka förbrukningen av data i lösningen genom att välja **OMS-arbetsyta** i navigerings menyn i Azure SQL-analys och sedan välja **användning** och **beräknade kostnader**.

## <a name="metrics-and-logs-available"></a>Mått och loggar är tillgängliga

Övervakning av telemetri som är tillgängliga för Azure SQL Database, elastiska pooler och hanterade instanser dokumenteras nedan. Insamlade övervakande telemetri i SQL Analytics kan användas för din egen anpassad analys och program utveckling med hjälp av [Azure Monitor logg frågor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) språk.

## <a name="basic-metrics"></a>Grundläggande mått

Se följande tabeller för information om grundläggande mått per resurs.

> [!NOTE]
> Alternativet grundläggande mått var tidigare känt som alla mått. Ändringen har gjorts enbart till namngivning och det gick inte att ändra de mått som övervakades. Den här ändringen initierades för att möjliggöra introduktion av ytterligare mått kategorier i framtiden.

### <a name="basic-metrics-for-elastic-pools"></a>Grundläggande mått för elastiska pooler

|**Resurs**|**Mått**|
|---|---|
|Elastisk pool|eDTU-procent, eDTU-använt, eDTU-gräns, processor procent, Läs procent för fysisk data, logg skrivnings procent, sessioner, procent, lagring, lagrings procent, lagrings gräns, XTP Storage-procent |

### <a name="basic-metrics-for-azure-sql-databases"></a>Grundläggande mått för Azure SQL-databaser

|**Resurs**|**Mått**|
|---|---|
|Azure SQL-databas|DTU-procent, använt DTU, DTU-gräns, CPU-procent, fysisk data läsnings procent, logg skrivnings procent, slutförd/misslyckad/blockerad av brand Väggs anslutningar, sessioner procent, procent för arbetare, lagring, lagrings procent, XTP lagrings procent och låsningar |

## <a name="basic-logs"></a>Basic-loggar

Information om telemetri som är tillgängliga för alla loggar finns dokumenterade i tabellerna nedan. Se [diagnostisk loggning som stöds](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) för att förstå vilka loggar som stöds för en viss databas smak – Azure SQL Single, pooled eller instance Database.

### <a name="resource-usage-stats-for-managed-instance"></a>Resursanvändnings statistik för hanterad instans

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure|
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: ResourceUsageStats |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: MANAGEDINSTANCES |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på den hanterade instansen |
|ResourceId|Resurs-URI |
|SKU_s|Produkt-SKU för hanterad instans |
|virtual_core_count_s|Antal tillgängliga virtuella kärnor |
|avg_cpu_percent_s|Genomsnittlig CPU-procent |
|reserved_storage_mb_s|Reserverad lagrings kapacitet på den hanterade instansen |
|storage_space_used_mb_s|Använt lagrings utrymme på den hanterade instansen |
|io_requests_s|IOPS-antal |
|io_bytes_read_s|Lästa IOPS-byte |
|io_bytes_written_s|Skrivna IOPS-byte |

### <a name="query-store-runtime-statistics"></a>Körnings statistik för Query Store

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: QueryStoreRuntimeStatistics |
|OperationName|Åtgärdens namn. Always: QueryStoreRuntimeStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|query_hash_s|Fråga hash |
|query_plan_hash_s|Fråga planera hash |
|statement_sql_handle_s|Instruktion för SQL-referens |
|interval_start_time_d|Starta datetimeoffset för intervallet i antal Tick från 1900-1-1 |
|interval_end_time_d|Slutet av datetimeoffset för intervallet i antal Tick från 1900-1-1 |
|logical_io_writes_d|Totalt antal logiska IO-skrivningar |
|max_logical_io_writes_d|Högsta antal logiska IO-skrivningar per körning |
|physical_io_reads_d|Totalt antal fysiska IO-läsningar |
|max_physical_io_reads_d|Högsta antal logiska IO-läsningar per körning |
|logical_io_reads_d|Totalt antal logiska IO-läsningar |
|max_logical_io_reads_d|Högsta antal logiska IO-läsningar per körning |
|execution_type_d|Körnings typ |
|count_executions_d|Antal körningar av frågan |
|cpu_time_d|Total CPU-tid som använts av frågan i mikrosekunder |
|max_cpu_time_d|Maximal CPU-tid för konsument med en enda körning i mikrosekunder |
|dop_d|Summan av grader av parallellitet |
|max_dop_d|Högsta grad av parallellitet som används för enskild körning |
|rowcount_d|Totalt antal returnerade rader |
|max_rowcount_d|Högsta antal rader som returneras i en enskild körning |
|query_max_used_memory_d|Total mängd minne som används i KB |
|max_query_max_used_memory_d|Maximal mängd minne som används av en enskild körning i KB |
|duration_d|Total körnings tid i mikrosekunder |
|max_duration_d|Maximal körnings tid för en enskild körning |
|num_physical_io_reads_d|Totalt antal fysiska läsningar |
|max_num_physical_io_reads_d|Högsta antal fysiska läsningar per körning |
|log_bytes_used_d|Total mängd logg byte som använts |
|max_log_bytes_used_d|Maximal mängd logg byte som använts per körning |
|query_id_d|ID för frågan i Frågearkivet |
|plan_id_d|ID för planen i Frågearkivet |

Läs mer om [körnings statistik data för Query Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Väntande statistik för Query Store

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: QueryStoreWaitStatistics |
|OperationName|Åtgärdens namn. Always: QueryStoreWaitStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|wait_category_s|Kategorin väntar |
|is_parameterizable_s|Är frågan parameteriseringsbar |
|statement_type_s|Typ av instruktion |
|statement_key_hash_s|Utdrags nyckelns hash |
|exec_type_d|Typ av körning |
|total_query_wait_time_ms_d|Total vänte tid för frågan i en viss wait-kategori |
|max_query_wait_time_ms_d|Maximal vänte tid för frågan i en enskild körning i den specifika wait-kategorin |
|query_param_type_d|0 |
|query_hash_s|Fråga hash i Frågearkivet |
|query_plan_hash_s|Fråga planera hash i Frågearkivet |
|statement_sql_handle_s|Instruktions referens i Frågearkivet |
|interval_start_time_d|Starta datetimeoffset för intervallet i antal Tick från 1900-1-1 |
|interval_end_time_d|Slutet av datetimeoffset för intervallet i antal Tick från 1900-1-1 |
|count_executions_d|Antal körningar av frågan |
|query_id_d|ID för frågan i Frågearkivet |
|plan_id_d|ID för planen i Frågearkivet |

Läs mer om [väntande statistik data för Query Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Data uppsättning fel

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: Errors |
|OperationName|Åtgärdens namn. Always: ErrorEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|Meddelande|Fel meddelande i oformaterad text |
|user_defined_b|Är den användardefinierade biten för fel |
|error_number_d|Felkod |
|Allvarsgrad|Felets allvarlighets grad |
|state_d|Tillstånd för felet |
|query_hash_s|Fråga hash för den misslyckade frågan, om den är tillgänglig |
|query_plan_hash_s|Fråga plan-hash för den misslyckade frågan, om den är tillgänglig |

Läs mer om [SQL Server fel meddelanden](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

### <a name="database-wait-statistics-dataset"></a>Data uppsättning för väntande statistik för databasen

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: DatabaseWaitStatistics |
|OperationName|Åtgärdens namn. Always: DatabaseWaitStatisticsEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|wait_type_s|Namn på vänte typen |
|start_utc_date_t [UTC]|Uppmätt period start tid |
|end_utc_date_t [UTC]|Uppmätt period slut tid |
|delta_max_wait_time_ms_d|Högsta vänte tid per körning |
|delta_signal_wait_time_ms_d|Total signal vänte tid |
|delta_wait_time_ms_d|Total vänte tid i perioden |
|delta_waiting_tasks_count_d|Antal väntande uppgifter |

Läs mer om [väntande statistik för databaser](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Data uppsättning för tids gränser

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: timeout |
|OperationName|Åtgärdens namn. Always: TimeoutEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|error_state_d|Fel tillstånds kod |
|query_hash_s|Fråga hash, om tillgängligt |
|query_plan_hash_s|Fråga om prenumerations-hash, om tillgängligt |

### <a name="blockings-dataset"></a>Blocker-datauppsättning

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: Blocks |
|OperationName|Åtgärdens namn. Always: BlockEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|lock_mode_s|Lås läge som används av frågan |
|resource_owner_type_s|Ägare till låset |
|blocked_process_filtered_s|Rapport-XML för blockerad process |
|duration_d|Längden på låset i mikrosekunder |

### <a name="deadlocks-dataset"></a>Data uppsättning för deadlock

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC] |Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: död lägen |
|OperationName|Åtgärdens namn. Always: DeadlockEvent |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|deadlock_xml_s|Deadlock-rapportens XML |

### <a name="automatic-tuning-dataset"></a>Data uppsättning för automatisk justering

|Egenskap|Beskrivning|
|---|---|
|TenantId|Ditt klient-ID |
|SourceSystem|Always: Azure |
|TimeGenerated [UTC]|Tidstämpel när loggen registrerades |
|Typ|Always: AzureDiagnostics |
|ResourceProvider|Namnet på resurs leverantören. Always: MICROSOFT. SQL |
|Kategori|Kategorins namn. Always: AutomaticTuning |
|Resurs|Namn på resursen |
|ResourceType|Namnet på resurs typen. Always: SERVERs/DATABASes |
|SubscriptionId|Prenumerations-GUID för databasen |
|ResourceGroup|Namnet på resurs gruppen för databasen |
|LogicalServerName_s|Namnet på servern för databasen |
|LogicalDatabaseName_s|Namn på databasen |
|ElasticPoolName_s|Namnet på den elastiska poolen för databasen, om det finns någon |
|DatabaseName_s|Namn på databasen |
|ResourceId|Resurs-URI |
|RecommendationHash_s|Unik hash för automatisk justerings rekommendation |
|OptionName_s|Automatisk justerings åtgärd |
|Schema_s|Databasschemat |
|Table_s|Tabell som påverkas |
|IndexName_s|Index namn |
|IndexColumns_s|Kolumn namn |
|IncludedColumns_s|Inkluderade kolumner |
|EstimatedImpact_s|Beräknad effekt av JSON för automatisk justerings rekommendation |
|Event_s|Typ av händelse för automatisk justering |
|Timestamp_t|Senast uppdaterad tidsstämpel |

### <a name="intelligent-insights-dataset"></a>Intelligent Insights data uppsättning

Läs mer om det [intelligent Insights logg formatet](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar loggning och förstår de mått och logg kategorier som stöds av de olika Azure-tjänsterna finns i:

- [Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Översikt över Azure Diagnostics-loggar](../azure-monitor/platform/resource-logs-overview.md)

Läs mer om Event Hubs:

- [Vad är Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Information om hur du ställer in aviseringar baserat på telemetri från Log Analytics finns i:

- [Skapa aviseringar för SQL Database och hanterad instans](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
