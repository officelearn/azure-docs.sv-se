---
title: Azure SQL database-mått och diagnostikloggning | Microsoft Docs
description: Läs mer om hur du konfigurerar Azure SQL Database för att lagra Resursanvändning, anslutningar och statistik för körning av frågan.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 19117803783047d8ddd9740c799b4cb81fd74c2c
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890916"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database-mått och diagnostikloggning 

Azure SQL-databas, elastiska pooler, Managed Instance och databaser i Managed Instance kan genererar loggar för mått och diagnostik för lättare prestandaövervakning. Du kan konfigurera en databas till stream-Resursanvändning, personal och sessioner och anslutning till en av dessa Azure-resurser:

* **Azure SQL-analys**: används som integrerad Azure intelligent databasprestanda övervakningslösning med rapporter, aviseringar och problemlösningsfunktioner.
* **Azure Event Hubs**: används för att integrera telemetri för SQL-databas med din anpassade övervakningslösning eller med heta pipelines.
* **Azure Storage**: används för arkivering av stora mängder telemetri för en bråkdel av priset.

    ![Arkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

Om du vill förstå mått och loggar kategorier som stöds av olika Azure-tjänster, kanske du vill överväga läsning:

* [Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Översikt över Azure-diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

## <a name="enable-logging-of-diagnostics-telemetry"></a>Aktivera loggning av diagnostiktelemetri

Du kan använda den första delen av det här dokumentet för att aktivera diagnostiktelemetri för databaser och den andra delen av dokumentet för att aktivera diagnostiktelemetri för elastiska pooler eller hanterade instanser. Använd avsnitten senare i det här dokumentet för att konfigurera Azure SQL Analytics som ett övervakningsverktyg för visning av diagnostiktelemetri strömmas databas.

> [!NOTE]
> Om du använder elastiska pooler eller hanterade instanser, förutom att aktivera diagnostiktelemetri för databaser, bör du också aktivera diagnostiktelemetri för dessa resurser också. Detta beror på elastiska pooler och hanterade instanser i rollen för databasen behållare har sin egen diagnostiktelemetri som är separat från en enskild databas-diagnostiktelemetri. 
>

Du kan aktivera och hantera mått och diagnostik telemetri loggning genom att använda någon av följande metoder:

- Azure Portal
- PowerShell
- Azure CLI
- Azure Monitor REST-API 
- Azure Resource Manager-mall

När du aktiverar mått och diagnostikloggning kan behöva du ange Azure-resurs mål där markerade data kommer att samlas in. Alternativen är:

- Azure SQL-analys
- Azure Event Hubs
- Azure Storage

Du kan etablera en ny resurs i Azure eller välja en befintlig resurs. Du måste ange vilka data du samlar in när du har valt en resurs med hjälp av alternativet för diagnostikinställningar.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Aktivera loggning för Azure SQL Database eller databaser i Managed Instance

Mått och diagnostik loggning på SQL-databas och databaserna i hanterade instanser är inte aktiverade som standard.

Följande diagnostiktelemetri är tillgängligt för samlingen för Azure SQL-databaser och databaser i hanterad instans:

| Övervakning av telemetri för databaser | Support för Azure SQL Database | Database Managed Instance support |
| :------------------- | ------------------- | ------------------- |
| [Alla mått](sql-database-metrics-diag-logging.md#all-metrics): innehåller DTU/CPU-procent DTU/CPU limit, fysiska data läses procent log skrivning procent brandväggsanslutningar, sessioner procent, arbetare procent, lagring, lagringsprocent, lyckades/misslyckades/blockerades och XTP-lagringsprocent. | Ja | Nej |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): innehåller information om körningsstatistik fråga, till exempel är CPU-användning och fråga efter varaktighet statistik. | Ja | Ja |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): innehåller information om frågan vänta statistik, som talar om dina frågor väntade på, till exempel processor, LOG och låser. | Ja | Ja |
| [Fel](sql-database-metrics-diag-logging.md#errors-dataset): innehåller information om SQL-fel som inträffat på den här databasen. | Ja | Nej |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): innehåller information om hur lång tid en databas har använt för att vänta på olika vänta typer. | Ja | Nej |
| [Tidsgränser](sql-database-metrics-diag-logging.md#time-outs-dataset): innehåller information om fel som inträffat på en databas. | Ja | Nej |
| [Block](sql-database-metrics-diag-logging.md#blockings-dataset): innehåller information om blockering av händelser som inträffat på en databas. | Ja | Nej |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): innehåller intelligenta insikter om prestanda. [Läs mer om intelligenta insikter](sql-database-intelligent-insights.md). | Ja | Ja |

### <a name="azure-portal"></a>Azure Portal

Strömning av diagnostiktelemetri för Azure SQL Database och databaser i Managed Instance till mål för Azure storage, konfigureras händelsehubbar och Log Analytics via inställningsmenyn för diagnostik för var och en av databaser i Azure-portalen.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Konfigurera strömning av diagnostiktelemetri för Azure SQL Database

   ![Ikon för SQL-databas](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Strömning av diagnostiktelemetri för **Azure SQL Database**, Följ dessa steg:

1. Gå till din Azure SQL Database-resurs
2. Välj **diagnostikinställningar**
3. Välj **slå på diagnostik** om inga tidigare inställningar finns, eller välj **Redigera inställning** så här redigerar du en föregående inställning
- Du kan skapa upp till tre (3) parallella anslutningar till stream-diagnostiktelemetri. Om du vill konfigurera flera parallella strömning av diagnostikdata till flera resurser, Välj **+ Lägg till diagnostikinställning** att skapa en ytterligare inställning.

   ![Aktivera diagnostik för SQL-databas](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

4. Ange ett namn för inställningen – för din egen referens
5. Välj till vilken resurs som ska stream diagnostics-data från databasen: **arkivet till lagringskontot**, **Stream till en händelsehubb**, eller **skicka till Log Analytics**
6. För standard övervakningsupplevelse, markerar du kryssrutorna för databasen loggtelemetri för diagnostik: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics** , **QueryStoreWaitStatistics**, **fel**, **DatabaseWaitStatistics**, **tidsgränser**, **block** , **Låsningar**. Den här telemetrin är händelse baserat och innehåller standard övervakningsupplevelse.
7. För avancerad övervakningsupplevelse, markerar du kryssrutan för **AllMetrics**. Det här är en 1 minut baserat telemetri för databas-diagnostiktelemetri enligt beskrivningen ovan. 
8. Klicka på **Spara**

   ![Konfigurera diagnostik för SQL-databas](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Kan inte aktiveras säkerhetsgranskning loggar från databasen diagnostikinställningar. För att aktivera granskning av loggströmningen, se [konfigurera granskning för din databas](sql-database-auditing.md#subheading-2), och får även [SQL granskningsloggar i Azure Log Analytics och Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).
>

> [!TIP]
> Upprepa ovanstående steg för varje Azure SQL-databas som du vill övervaka. 
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Konfigurera strömning av diagnostiktelemetri för databaser i Managed Instance

   ![Database Managed Instance ikon](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Strömning av diagnostiktelemetri för **databaser i Managed Instance**, Följ dessa steg:

1. Gå till din databas i Managed Instance
2. Välj **diagnostikinställningar**
3. Välj **slå på diagnostik** om inga tidigare inställningar finns, eller välj **Redigera inställning** så här redigerar du en föregående inställning
- Du kan skapa upp till tre (3) parallella anslutningar till stream-diagnostiktelemetri. Om du vill konfigurera flera parallella strömning av diagnostikdata till flera resurser, Välj **+ Lägg till diagnostikinställning** att skapa en ytterligare inställning.

   ![Aktivera diagnostik för hanterad instans-databas](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Ange ett namn för inställningen – för din egen referens
5. Välj till vilken resurs som ska stream diagnostics-data från databasen: **arkivet till lagringskontot**, **Stream till en händelsehubb**, eller **skicka till Log Analytics**
6. Markera kryssrutorna för databastelemetri för diagnostik: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** och **fel**
7. Klicka på **Spara**

   ![Konfigurera diagnostik för hanterad instans-databas](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Upprepa ovanstående steg för varje databas i hanterade instanser som du vill övervaka.
>

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Aktivera loggning för elastiska pooler eller hanterad instans

Elastiska pooler och instanser som hanteras som databasen behållare har sin egen diagnostiktelemetri som är separat från databaser. Den här diagnostiktelemetri är inte aktiverat som standard. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Konfigurera strömning av diagnostiktelemetri för elastiska pooler

   ![Ikon för elastisk pool](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Följande diagnostiktelemetri är tillgängligt för samlingen för elastiska pooler resurs:

| Resurs | Övervakning av telemetri |
| :------------------- | ------------------- |
| **Elastisk pool** | [Alla mått](sql-database-metrics-diag-logging.md#all-metrics) innehåller eDTU/CPU-procent, eDTU/CPU-begränsning, fysiska data läses procent, skriva log procent, sessioner procent, arbetare procent, lagring, lagringsprocent, lagringsgräns och XTP lagringsprocent. |

Strömning av diagnostiktelemetri för **elastiska pooler**, Följ dessa steg:

1. Gå till resursen som elastisk pool i Azure-portalen
2. Välj **diagnostikinställningar**
3. Välj **slå på diagnostik** om inga tidigare inställningar finns, eller välj **Redigera inställning** så här redigerar du en föregående inställning

   ![Aktivera diagnostik för elastiska pooler](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Ange ett namn för inställningen – för din egen referens
5. Välj till vilken resurs som ska stream diagnostics-data från den elastiska poolen: **arkivet till lagringskontot**, **Stream till en händelsehubb**, eller **skicka till Log Analytics**
6. Om Log Analytics har valts, Välj **konfigurera** och skapa en ny arbetsyta genom att välja **+ Skapa ny arbetsyta**, eller välj en befintlig arbetsyta
7. Markera kryssrutan för telemetri för elastisk pool diagnostik **AllMetrics**
8. Klicka på **Spara**

   ![Konfigurera diagnostik för elastiska pooler](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Upprepa ovanstående steg för varje elastisk pool som du vill övervaka.
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Konfigurera strömning av diagnostiktelemetri för hanterad instans

   ![Hanterad instans-ikon](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Följande diagnostiktelemetri är tillgängligt för samlingen för hanterad instans-resursen:

| Resurs | Övervakning av telemetri |
| :------------------- | ------------------- |
| **Hanterad instans** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) innehåller antal virtuella kärnor, Genomsnittlig CPU-procent, i/o-begäranden, byte lästa/skrivna, reserverade lagringsutrymme, använt lagringsutrymme. |

Strömning av diagnostiktelemetri för **Managed Instance resource**, Följ dessa steg:

1. Gå till resursen som hanterad instans i Azure-portalen
2. Välj **diagnostikinställningar**
3. Välj **slå på diagnostik** om inga tidigare inställningar finns, eller välj **Redigera inställning** så här redigerar du en föregående inställning

   ![Aktivera diagnostik för hanterad instans](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Ange ett namn för inställningen – för din egen referens
5. Välj till vilken resurs som ska stream diagnostics-data från den elastiska poolen: **arkivet till lagringskontot**, **Stream till en händelsehubb**, eller **skicka till Log Analytics**
6. Om Log Analytics väljs, skapa eller använda en befintlig arbetsyta
7. Markera kryssrutan för instans-diagnostiktelemetri **ResourceUsageStats**
8. Klicka på **Spara**

   ![Konfigurera diagnostik för hanterad instans](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Upprepa ovanstående steg för varje hanterad instans som du vill övervaka.
>

### <a name="powershell"></a>PowerShell

Om du vill aktivera mått och diagnostik loggning med hjälp av PowerShell, använder du följande kommandon:

- Använd följande kommando om du vill aktivera lagring av diagnostikloggar i ett lagringskonto:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Storage-konto-ID är resurs-ID för lagringskontot där du vill skicka loggarna.

- Om du vill aktivera strömning av diagnostikloggar till en händelsehubb, Använd följande kommando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Regel-ID för Azure Service Bus är en sträng med det här formatet:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Använd följande kommando om du vill aktivera skicka diagnostikloggar till en Log Analytics-arbetsyta:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Du kan hämta resurs-ID för Log Analytics-arbetsytan med hjälp av följande kommando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Du kan kombinera dessa parametrar om du vill aktivera flera Utdataalternativ för.

### <a name="to-configure-multiple-azure-resources"></a>Konfigurera flera Azure-resurser

För att stödja flera prenumerationer, använder du PowerShell-skriptet från [aktivera Azure resource mått loggning med hjälp av PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Ange resurs-ID för arbetsyta &lt;$WSID&gt; som en parameter när du kör skriptet (aktivera AzureRMDiagnostics.ps1) att skicka diagnostikdata från flera resurser till arbetsytan. Att hämta arbetsyte-ID &lt;$WSID&gt; till vilket du vill skicka diagnostiska data, Ersätt &lt;subID&gt; med prenumerations-ID Ersätt &lt;RG_NAME&gt; med resursgruppens namn och Ersätt &lt;WS_NAME&gt; med arbetsytans namn i skriptet nedan.

- Om du vill konfigurera flera Azure-resurser, använder du följande kommandon:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Om du vill aktivera mått och diagnostik loggning med hjälp av Azure CLI, använder du följande kommandon:

- Använd följande kommando om du vill aktivera lagring av diagnostikloggar i ett lagringskonto:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Storage-konto-ID är resurs-ID för lagringskontot där du vill skicka loggarna.

- Om du vill aktivera strömning av diagnostikloggar till en händelsehubb, Använd följande kommando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Regel-ID för Service Bus är en sträng med det här formatet:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Använd följande kommando om du vill aktivera skicka diagnostikloggar till en Log Analytics-arbetsyta:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Du kan kombinera dessa parametrar om du vill aktivera flera Utdataalternativ för.

### <a name="rest-api"></a>REST-API

Läs om hur du [ändra inställningarna för startdiagnostik med hjälp av REST-API i Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Resource Manager-mall

Läs om hur du [aktivera diagnostikinställningar när resursen skapas med hjälp av Resource Manager-mall](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-azure-sql-analytics"></a>Stream till Azure SQL-analys 

Azure SQL Analytics är ett moln som övervakningslösning för övervakning av prestanda för Azure SQL-databaser, elastiska pooler och instanser som hanteras i stor skala och över flera prenumerationer via ett fönster för alla. Den samlar in och visar viktiga mått för Azure SQL Database-prestanda med inbyggd intelligens för prestandafelsökning av.

![Översikt över Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database mått och diagnostik för loggar kan strömmas till Azure SQL Analytics med hjälp av inbyggt **skicka till Log Analytics** alternativ i inställningsbladet diagnostik i portalen. Du kan också aktivera Log Analytics med hjälp av en diagnostikinställning via PowerShell-cmdletar, Azure CLI eller REST-API i Azure Monitor.

### <a name="installation-overview"></a>Installationsöversikt

Övervakning av en SQL Database-flotta är enkelt med Azure SQL Analytics. Tre steg krävs:

1. Skapa Azure SQL Analytics-lösningen från Azure Marketplace
2. Skapa arbetsytan för övervakning i lösningen
3. Konfigurera databaserna så att stream-diagnostiktelemetri till arbetsytan som du har skapat.

Om du använder elastiska pooler eller hanterade instanser, utöver att konfigurera databasen diagnostiktelemetri, konfigurera strömning av diagnostiktelemetri från dessa resurser också.

### <a name="create-azure-sql-analytics-resource"></a>Skapa Azure SQL Analytics-resurs

1. Sök efter Azure SQL Analytics i Azure Marketplace och välj den

   ![Sök efter Azure SQL Analytics i portalen](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)
   
2. Välj **skapa** på lösningens översiktsskärmen

3. Fyll i formuläret Azure SQL Analytics med ytterligare information som krävs: namn på arbetsyta, prenumeration, resursgrupp, plats och prisnivå.
 
   ![Konfigurera Azure SQL Analytics i portalen](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Bekräfta genom att välja **OK**, och slutför genom att välja **skapa**

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurera databaser för att registrera mått och diagnostik för loggar

Det enklaste sättet att konfigurera där databaser registrera sina mått är via Azure portal – enligt beskrivningen ovan. Gå till din SQL Database-resurs i portalen och välj **diagnostikinställningar**.

Om du använder elastiska pooler eller hanterade instanser, du måste också konfigurera inställningarna för Startdiagnostik i de här resurserna för att strömma sina egna diagnostiktelemetri till arbetsytan som du har skapat.

### <a name="use-the-sql-analytics-solution"></a>Använd SQL Analytics-lösningen

SQL Analytics är en hierarkisk instrumentpanel där du kan navigera i hierarkin för SQL Database-resurser. Läs hur du använder SQL Analytics-lösningen i [övervaka SQL-databasen med hjälp av SQL Analytics-lösningen](../azure-monitor/insights/azure-sql.md).

## <a name="stream-into-event-hubs"></a>Strömma till Event Hubs

SQL Database mått och diagnostik för loggar kan strömmas till Event Hubs med hjälp av inbyggt **Stream till en händelsehubb** alternativet i portalen. Du kan också aktivera regel-ID för Service Bus med hjälp av en diagnostikinställning via PowerShell-cmdletar, Azure CLI eller REST-API i Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Vad du gör med mått och diagnostik för loggar i Event Hubs
När valda data strömmas i Event Hubs, är ett steg närmare att aktivera avancerade scenarier för övervakning. Händelsehubbar fungerar som åtkomsten för en händelsepipeline. När data har samlats in i en händelsehubb, kan de omvandlas och lagras med valfri leverantör av realtidsanalys eller batchbearbetnings-/ lagringsadapter. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser. På så sätt kan händelsekonsumenterna kan komma åt händelser på sitt eget schema. Mer information om Händelsehubbar finns:

- [Vad är Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Här är några sätt att du kan använda funktionen för direktuppspelning:

* **Visa tjänstehälsa med strömmande data i frekvent sökväg till Power BI**. Med hjälp av Event Hubs, Stream Analytics och Power BI, kan du enkelt omvandla dina data för mått och diagnostik i nära realtid på dina Azure-tjänster. En översikt över hur du ställer in en event hub, bearbeta data med Stream Analytics och utnyttja Power BI som utdata, finns i [Stream Analytics och Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Stream loggar till tredje parts loggning och telemetri dataströmmar**. Genom att använda Event Hubs strömning kan få du mått och diagnostik för loggarna i olika lösningar från tredje part övervaknings- och log analytics. 

* **Skapa en anpassad telemetri och loggning**. Om du redan har en specialbyggda telemetri plattform eller funderar på att bygga en mycket skalbar publicerings-/ natur Händelsehubbar kan du flexibelt kan mata in diagnostikloggar. Se [Dan Rosanova för att använda Event Hubs i en global skala telemetri platform](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream till Storage

SQL Database mått och diagnostik för loggar kan lagras i Storage med hjälp av inbyggt **arkivet till ett lagringskonto** alternativet i portalen. Du kan också aktivera lagring med hjälp av en diagnostikinställning via PowerShell-cmdletar, Azure CLI eller REST-API i Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schemat för mått och diagnostik för loggar i storage-konto

När du har konfigurerat mått och diagnostik för insamling av loggar, skapas en lagringsbehållare i lagringskontot som du har valt när de första raderna i data är tillgängliga. Strukturen för dessa blobar är:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Eller rättare:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Exempelvis kanske en blob-namnet för alla mått:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Om du vill spela in data från den elastiska poolen är blobnamnet lite annorlunda:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Hämta mått och loggar från Storage

Lär dig hur du [hämta mått och diagnostik för loggar från Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Policy för datalagring och prissättning

Om du väljer Event Hubs eller ett lagringskonto kan ange du en bevarandeprincip. Den här principen tar bort data som är äldre än en vald tidsperiod. Om du anger Log Analytics beror bevarandeprincipen på den valda prisnivån. Förbrukning av diagnostiktelemetri ovan de kostnadsfria enheterna för datainmatning som allokeras per månad gäller. De kostnadsfria enheterna för datainmatning tillhandahålls aktivera kostnadsfri övervakning av flera databaser varje månad. Observera att mer aktiva databaser med större arbetsbelastningar kommer mata in mer data jämfört med inaktiva databaser. Mer information finns i [priserna för Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). 

Om du använder Azure SQL Analytics kan övervaka du enkelt din förbrukning för inmatning av data i lösningen genom att välja OMS-arbetsyta på navigeringsmenyn i Azure SQL Analytics och sedan välja användning och uppskattade kostnader.

## <a name="metrics-and-logs-available"></a>Mått och loggar som är tillgängliga

Insamlade övervakning telemetri som kan användas för dina egna **anpassade analysis** och **programutveckling** med [SQL Analytics-språket](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries). Strukturen för de insamlade data, loggar och mått som listas nedan.

## <a name="all-metrics"></a>Alla mått

### <a name="all-metrics-for-elastic-pools"></a>Alla mått för elastiska pooler

|**Resurs**|**Mått**|
|---|---|
|Elastisk pool|eDTU procent eDTU används, eDTU-gränsen, CPU-procent, fysiska data Läs procent, skriva log procent, sessioner procent, arbetare procent, lagring, lagringsprocent, gränsen för lagring, XTP-lagringsprocent |

### <a name="all-metrics-for-azure-sql-database"></a>Alla mått för Azure SQL Database

|**Resurs**|**Mått**|
|---|---|
|Azure SQL Database|DTU-procent DTU används, DTU-gränsen, CPU-procent, fysiska data Läs procent, skriva log procent, brandväggsanslutningar, sessioner procent, arbetare procent, lagring, lagringsprocent, XTP lagringsprocent, lyckades/misslyckades/blockerades och låsningar |

## <a name="logs"></a>Logs

### <a name="logs-for-managed-instance"></a>Loggar för den hanterade instansen

### <a name="resource-usage-stats"></a>Statistik för användning av resurs

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: ResourceUsageStats|
|Resurs|Namnet på resursen.|
|ResourceType|Namnet på resurstypen. Alltid: MANAGEDINSTANCES|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den hanterade instansen.|
|Resurs-ID|Resurs-URI.|
|SKU_s|Hanterad instans produkt-SKU|
|virtual_core_count_s|Numver av virtuella kärnor tillgängliga|
|avg_cpu_percent_s|Genomsnittlig CPU-procent|
|reserved_storage_mb_s|Reserverad kapacitet på hanterad instans|
|storage_space_used_mb_s|Använda lagringsutrymmet på hanterad instans|
|io_requests_s|Antal IOPS|
|io_bytes_read_s|Lästa IOPS-byte|
|io_bytes_written_s|Skrivna IOPS-byte|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Loggar för databaser med Azure SQL Database Managed Instance

### <a name="query-store-runtime-statistics"></a>Query Store-körningsstatistik

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: QueryStoreRuntimeStatistics|
|OperationName|Åtgärdens namn. Alltid: QueryStoreRuntimeStatisticsEvent|
|Resurs|Namnet på resursen.|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|query_hash_s|Fråga hash.|
|query_plan_hash_s|Fråge-hash för planen.|
|statement_sql_handle_s|Sql instruktionsreferensen.|
|interval_start_time_d|Starta datetimeoffset för intervallet i antalet tick från 1900-1-1.|
|interval_end_time_d|Avsluta datetimeoffset tidsintervall i antalet tick från 1900-1-1.|
|logical_io_writes_d|Totalt antal logiska i/o-skrivningar.|
|max_logical_io_writes_d|Maxantal logiska IO skriver per körning.|
|physical_io_reads_d|Totalt antal fysiska i/o-läsåtgärder.|
|max_physical_io_reads_d|Maxantal logiska IO läser per körning.|
|logical_io_reads_d|Totalt antal logiska i/o-läsåtgärder.|
|max_logical_io_reads_d|Maxantal logiska IO läser per körning.|
|execution_type_d|Körningstyp av.|
|count_executions_d|Antal körningar av frågan.|
|cpu_time_d|Total CPU-tid som används av frågan i mikrosekunder.|
|max_cpu_time_d|Max CPU tid konsument av en enda körning i mikrosekunder.|
|dop_d|Summan av grad av parallellitet.|
|max_dop_d|Maxgrad av parallellitet som används för utförande.|
|rowcount_d|Totalt antal rader som returneras.|
|max_rowcount_d|Maxantal rader som returneras i utförande.|
|query_max_used_memory_d|Totala mängden minne som används i KB.|
|max_query_max_used_memory_d|Högsta mängd minne som används av en enda körning i KB.|
|duration_d|Total körningstid i mikrosekunder.|
|max_duration_d|Maximal körningstid för en enda åtgärd.|
|num_physical_io_reads_d|Totalt antal fysiska läsningar.|
|max_num_physical_io_reads_d|Maxantal fysiska läsningar per körning.|
|log_bytes_used_d|Totala mängden logg-byte som används.|
|max_log_bytes_used_d|Maximal mängd logg-byte som används per körning.|
|query_id_d|ID för frågan i Query Store.|
|plan_id_d|ID för planen i Query Store.|

Läs mer om [Query Store runtime statistikdata](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Query Store vänta statistik

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: QueryStoreWaitStatistics|
|OperationName|Åtgärdens namn. Alltid: QueryStoreWaitStatisticsEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|wait_category_s|Kategori för väntan.|
|is_parameterizable_s|Är frågan parameteriseringsbar.|
|statement_type_s|Typ av instruktionen.|
|statement_key_hash_s|Hash för instruktionen nyckel.|
|exec_type_d|Typ av körningen.|
|total_query_wait_time_ms_d|Total väntetid av frågan för specifika wait-kategorin.|
|max_query_wait_time_ms_d|Maximal väntetid av frågan i enskilda körningen på kategorin specifika vänta.|
|query_param_type_d|0|
|query_hash_s|Fråga hash i Query Store.|
|query_plan_hash_s|Fråga plan hash i Query Store.|
|statement_sql_handle_s|Instruktionsreferensen i Query Store.|
|interval_start_time_d|Starta datetimeoffset för intervallet i antalet tick från 1900-1-1.|
|interval_end_time_d|Avsluta datetimeoffset tidsintervall i antalet tick från 1900-1-1.|
|count_executions_d|Antal körningar av frågan.|
|query_id_d|ID för frågan i Query Store.|
|plan_id_d|ID för planen i Query Store.|

Läs mer om [Query Store vänta statistikdata](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Fel-datauppsättning

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: fel|
|OperationName|Åtgärdens namn. Alltid: ErrorEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|Meddelande|Felmeddelande i oformaterad text.|
|user_defined_b|Är fel användardefinierade biten.|
|error_number_d|Felkod.|
|Severity|Allvarlighetsgraden för felet.|
|state_d|Tillståndet för felet.|
|query_hash_s|Fråge-hash för misslyckade frågan, om det är tillgängligt.|
|query_plan_hash_s|Fråga plan hash för misslyckade frågan, om det är tillgängligt.|

Läs mer om [felmeddelanden för SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Databasen vänta statistik datauppsättning

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: DatabaseWaitStatistics|
|OperationName|Åtgärdens namn. Alltid: DatabaseWaitStatisticsEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|wait_type_s|Namnet på typen vänta.|
|start_utc_date_t [UTC]|Mätt periodens starttid.|
|end_utc_date_t [UTC]|Mätt period sluttid.|
|delta_max_wait_time_ms_d|Max väntade tid per körning|
|delta_signal_wait_time_ms_d|Totalt antal signalen väntetid.|
|delta_wait_time_ms_d|Total Väntetid i perioden.|
|delta_waiting_tasks_count_d|Antalet väntande aktiviteter.|

Läs mer om [databasen vänta statistik](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Timeout-datauppsättning

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: tidsgränser|
|OperationName|Åtgärdens namn. Alltid: TimeoutEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|error_state_d|Felkod för tillstånd.|
|query_hash_s|Fråga hash, om det är tillgängligt.|
|query_plan_hash_s|Fråga plan hash, om det är tillgängligt.|

### <a name="blockings-dataset"></a>Blockings datauppsättning

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: block|
|OperationName|Åtgärdens namn. Alltid: BlockEvent|
|Resurs|Namn på resursen|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|lock_mode_s|LOCK-läge som används av frågan.|
|resource_owner_type_s|Låsägare.|
|blocked_process_filtered_s|Blockerad processen rapporten XML.|
|duration_d|Varaktighet för låset i mikrosekunder.|

### <a name="deadlocks-dataset"></a>Låsningar datauppsättning

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC] |Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: låsningar|
|OperationName|Åtgärdens namn. Alltid: DeadlockEvent|
|Resurs|Namnet på resursen.|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana.|
|DatabaseName_s|Namnet på databasen. |
|Resurs-ID|Resurs-URI.|
|deadlock_xml_s|Deadlock rapport XML.|

### <a name="automatic-tuning-dataset"></a>Automatisk justering datauppsättning

|Egenskap |Beskrivning|
|---|---|
|TenantId|Din klient-ID.|
|SourceSystem|Alltid: Azure|
|TimeGenerated [UTC]|Tidsstämpel när loggen registrerades.|
|Typ|Alltid: AzureDiagnostics|
|ResourceProvider|Namnet på resursprovidern. Alltid: MICROSOFT. SQL|
|Kategori|Namnet på kategorin. Alltid: AutomaticTuning|
|Resurs|Namnet på resursen.|
|ResourceType|Namnet på resurstypen. Alltid: Servrar/databaser|
|SubscriptionId|Prenumerations-GUID som databasen tillhör.|
|ResourceGroup|Namnet på resursgruppen som databasen tillhör.|
|LogicalServerName_s|Namnet på den server som databasen tillhör.|
|LogicalDatabaseName_s|Namnet på databasen.|
|ElasticPoolName_s|Namnet på den elastiska poolen som databasen tillhör, om sådana.|
|DatabaseName_s|Namnet på databasen.|
|Resurs-ID|Resurs-URI.|
|RecommendationHash_s|Unikt hash för automatisk justering rekommendation.|
|OptionName_s|Åtgärd för automatisk justering.|
|Schema_s|Databasschemat.|
|Table_s|Tabell som påverkas.|
|IndexName_s|Indexnamnet.|
|IndexColumns_s|Kolumnnamn.|
|IncludedColumns_s|Kolumner som ingår.|
|EstimatedImpact_s|Uppskattad påverkan av automatisk justering rekommendation JSON.|
|Event_s|Typ av händelse med automatisk justering.|
|Timestamp_t|Senast uppdaterade tidsstämpeln.|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights datauppsättning
Läs mer om den [smarta insikter loggformat](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Nästa steg

Läs följande information om hur du aktiverar loggning och förstå mått och loggfiler kategorier som stöds av olika Azure-tjänster:

 * [Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Översikt över Azure-diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Läs mer om Event Hubs finns:

* [Vad är Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Mer information om Storage finns så [hämta mått och diagnostik för loggar från Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
