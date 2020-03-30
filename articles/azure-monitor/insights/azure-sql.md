---
title: Azure SQL Analytics-lösning i Azure Monitor | Microsoft-dokument
description: Azure SQL Analytics-lösning hjälper dig att hantera dina Azure SQL-databaser
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275469"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Övervaka Azure SQL-databas med Azure SQL Analytics (förhandsversion)

![Azure SQL Analytics-symbol](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics är en avancerad molnövervakningslösning för övervakning av prestanda för alla dina Azure SQL-databaser i stor skala och över flera prenumerationer i en enda vy. Azure SQL Analytics samlar in och visualiserar nyckeltal med inbyggd intelligens för felsökning av prestanda.

Genom att använda dessa insamlade mått kan du skapa anpassade övervakningsregler och aviseringar. Azure SQL Analytics hjälper dig att identifiera problem på varje lager i programstacken. Den använder Azure Diagnostic-mått tillsammans med Azure Monitor-vyer för att presentera data om alla dina Azure SQL-databaser i en enda Log Analytics-arbetsyta. Azure Monitor hjälper dig att samla in, korrelera och visualisera strukturerade och ostrukturerade data.

En praktisk översikt över hur du använder Azure SQL Analytics-lösning och för typiska användningsscenarier finns i den inbäddade videon:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Anslutna källor

Azure SQL Analytics är en molnövervakningslösning som stöder direktuppspelning av diagnostiktelemetri för alla dina Azure SQL-databaser. Eftersom Azure SQL Analytics inte använder agenter för att ansluta till Azure Monitor, stöder det inte övervakning av SQL Server som finns lokalt eller i virtuella datorer.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Diagnostikinställningar](../platform/diagnostic-settings.md) | **Ja** | Azure-mått och loggdata skickas till Azure Monitor-loggar direkt via Azure. |
| [Azure-lagringskonto](../platform/collect-azure-metrics-logs.md) | Inga | Azure Monitor läser inte data från ett lagringskonto. |
| [Windows-agenter](../platform/agent-windows.md) | Inga | Direkt Windows-agenter används inte av Azure SQL Analytics. |
| [Linux-agenter](../learn/quick-collect-linux-computer.md) | Inga | Direkt Linux-agenter används inte av Azure SQL Analytics. |
| [System Center Operations Manager-hanteringsgrupp](../platform/om-agents.md) | Inga | En direkt anslutning från Operations Manager-agenten till Azure Monitor används inte av Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Azure SQL Analytics-alternativ

I tabellen nedan beskrivs alternativ som stöds för två versioner av Azure SQL Analytics-instrumentpanelen, en för enstaka och poolerade databaser och elastiska pooler och den andra för hanterade instanser och instansdatabaser.

| Azure SQL Analytics-alternativ | Beskrivning | Stöd för enstaka och poolerade databaser och elastiska pooler | Stöd för hanterade instanser och instanser databas |
| --- | ------- | ----- | ----- |
| Resurs efter typ | Perspektiv som räknar alla resurser som övervakas. | Ja | Ja |
| Insikter | Ger hierarkisk detaljgranskning i intelligenta insikter om prestanda. | Ja | Ja |
| Fel | Ger hierarkisk detaljgranskning i SQL-fel som inträffade på databaserna. | Ja | Ja |
| Timeouter | Ger hierarkisk detaljgranskning i SQL-tidsutskrifter som hände på databaserna. | Ja | Inga |
| Blockeringar | Ger hierarkisk detaljgranskning i SQL-blockeringar som hände på databaserna. | Ja | Inga |
| Databasen väntar | Ger hierarkisk detaljgranskning i SQL-väntestatistik på databasnivå. Innehåller sammanfattningar av den totala väntetiden och väntetiden per väntetyp. |Ja | Inga |
| Varaktighet för fråga | Ger hierarkisk detaljgranskning i frågekörningsstatistiken, till exempel frågevaraktighet, CPU-användning, data-I/O-användning, log IO-användning. | Ja | Ja |
| Fråga väntar | Ger hierarkisk detaljgranskning i frågeväntans statistik efter väntekategori. | Ja | Ja |

## <a name="configuration"></a>Konfiguration

Använd processen som beskrivs i [Lägg till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md) för att lägga till Azure SQL Analytics (Preview) på din Log Analytics-arbetsyta.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Konfigurera Azure SQL-databaser för att strömma diagnostiktelemetri

När du har skapat Azure SQL Analytics-lösning på arbetsytan måste du **konfigurera varje** resurs som du vill övervaka för att strömma sin diagnostiktelemetri till Azure SQL Analytics. Följ detaljerade instruktioner på denna sida:

- Aktivera Azure Diagnostics för din Azure SQL-databas för att [strömma diagnostiktelemetri till Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

På sidan ovan finns också instruktioner om hur du aktiverar stöd för övervakning av flera Azure-prenumerationer från en enda Azure SQL Analytics-arbetsyta som en enda glasruta.

## <a name="using-azure-sql-analytics"></a>Använda Azure SQL Analytics

När du lägger till Azure SQL Analytics på din arbetsyta läggs Azure SQL Analytics-panelen till på arbetsytan och visas i Översikt. Välj Visa sammanfattningslänk om du vill läsa in panelinnehållet.

![Sammanfattningspanel för Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

När panelen har lästs in visas antalet databaser med en och pool, elastiska pooler, hanterade instanser och hanterade instansdatabaser som Azure SQL Analytics tar emot diagnostiktelemetri från.

![Azure SQL Analytics-panel](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics innehåller två separata vyer – en för övervakning av enskilda databaser och poolade databaser och elastiska pooler och den andra vyn för övervakning av hanterade instanser och instansdatabaser.

Om du vill visa Azure SQL Analytics-övervakningsinstrumentpanel för enstaka och poolerade databaser och elastiska pooler klickar du på den övre delen av panelen. Om du vill visa Azure SQL Analytics-övervakningsinstrumentpanel för hanterade instanser och instansdatabaser klickar du på den nedre delen av panelen.

### <a name="viewing-azure-sql-analytics-data"></a>Visa Azure SQL Analytics-data

Instrumentpanelen innehåller en översikt över alla databaser som övervakas med olika perspektiv. För att olika perspektiv ska fungera måste du aktivera rätt mått eller loggar på dina SQL-resurser som ska strömmas till Log Analytics-arbetsytan.

Om vissa mått eller loggar inte strömmas till Azure Monitor fylls inte panelerna i Azure SQL Analytics med övervakningsinformation.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Enstaka och poolade databaser och vyn elastiska pooler

När Azure SQL Analytics-panelen för databasen har valts visas övervakningsinstrumentpanelen.

![Översikt över Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Om du väljer någon av panelerna öppnas en nedåtgående rapport i det specifika perspektivet. När perspektivet har valts öppnas detaljgranskningsrapporten.

![Tidsutgångar för Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Varje perspektiv i den här vyn innehåller sammanfattningar på prenumerations-, server-, elastiskpool- och databasnivå. Dessutom visar varje perspektiv ett perspektiv som är specifikt för rapporten till höger. Om du väljer prenumeration, server, pool eller databas i listan fortsätter detaljgranskningen.

### <a name="managed-instance-and-instances-databases-view"></a>Vy över hanterade instanser och instanser

När Azure SQL Analytics-panelen för databaserna har valts visas instrumentpanelen för övervakning.

![Översikt över Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Om du väljer någon av panelerna öppnas en nedåtgående rapport i det specifika perspektivet. När perspektivet har valts öppnas detaljgranskningsrapporten.

Om du väljer den hanterade instansvyn visas information om den hanterade instansanvändningen, databaser som den innehåller och telemetri på de frågor som körs över instansen.

![Tidsutgångar för Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Rapport om intelligenta insikter

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) låter dig veta vad som händer med prestanda för alla Azure SQL-databaser. Alla intelligenta insikter som samlas in kan visualiseras och nås via insights-perspektivet.

![Azure SQL Analytics-insikter](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastiska pooler och databasrapporter

Både elastiska pooler och databaser har sina egna specifika rapporter som visar alla data som samlas in för resursen under den angivna tiden.

![Azure SQL Analytics-databas](./media/azure-sql/azure-sql-sol-database.png)

![Elastisk azure SQL-pool](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Frågerapporter

Genom frågevaraktighet och frågeväntans perspektiv kan du korrelera prestanda för alla frågor via frågerapporten. Den här rapporten jämför frågeprestandan mellan olika databaser och gör det enkelt att identifiera databaser som utför den valda frågan väl jämfört med de som är långsamma.

![Azure SQL Analytics-frågor](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Behörigheter

Om du vill använda Azure SQL Analytics måste användare beviljas en minsta behörighet för reader-rollen i Azure. Den här rollen tillåter dock inte användare att se frågetexten eller utföra några automatiska justeringsåtgärder. Mer tillåtande roller i Azure som gör det möjligt att använda Azure SQL Analytics i största möjliga utsträckning är Ägare, Deltagare, SQL DB Contributor eller SQL Server Contributor. Du kanske också vill överväga att skapa en anpassad roll i portalen med specifika behörigheter som krävs endast för att använda Azure SQL Analytics och utan åtkomst till hantering av andra resurser.

### <a name="creating-a-custom-role-in-portal"></a>Skapa en anpassad roll i portalen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Identifiera att vissa organisationer tillämpar strikta behörighetskontroller i Azure, hitta följande PowerShell-skript som möjliggör skapandet av en anpassad roll "SQL Analytics Monitoring Operator" i Azure-portalen med de lägsta läs- och skrivbehörigheter som krävs för att använda Azure SQL Analytics i dess största utsträckning.

Ersätt {SubscriptionId}" i skriptet nedan med ditt Azure-prenumerations-ID och kör skriptet som loggas in som en ägar- eller deltagarroll i Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

När den nya rollen har skapats tilldelar du den här rollen till varje användare som du behöver för att bevilja anpassade behörigheter för att använda Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analysera data och skapa aviseringar

Dataanalys i Azure SQL Analytics baseras på [Log Analytics-språk](../log-query/get-started-queries.md) för din anpassade fråga och rapportering. Hitta en beskrivning av tillgängliga data som samlats in från databasresursen för anpassad frågor i [tillgängliga mått och loggar](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatisk avisering i Azure SQL Analytics baseras på att skriva en Log Analytics-fråga som utlöser en avisering vid ett villkor som uppfylls. Nedan hittar du flera exempel på Logganalysfrågor där aviseringar kan ställas in i Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Skapa aviseringar för Azure SQL Database

Du kan enkelt [skapa aviseringar](../platform/alerts-metric.md) med data som kommer från Azure SQL Database-resurser. Här är några användbara [loggfrågor](../log-query/log-query-overview.md) som du kan använda med en loggavisering:

#### <a name="high-cpu-on-azure-sql-database"></a>Hög CPU i Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Förkrav för att konfigurera den här aviseringen är att övervakade databaser strömmar grundläggande mått till Azure SQL Analytics.
> - Ersätt MetricName-värdet cpu_percent med dtu_consumption_percent för att få höga DTU-resultat i stället.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Elastiska hög cpu i elastiska Azure SQL Database-pooler

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Förkrav för att konfigurera den här aviseringen är att övervakade databaser strömmar grundläggande mått till Azure SQL Analytics.
> - Ersätt MetricName-värdet cpu_percent med dtu_consumption_percent för att få höga DTU-resultat i stället.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Azure SQL Database lagring i genomsnitt över 95% under de senaste 1 tim

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - Förkrav för att konfigurera den här aviseringen är att övervakade databaser strömmar grundläggande mått till Azure SQL Analytics.
> - Den här frågan kräver att en aviseringsregel ställs in för att avfyra en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på vissa databaser. Utdata är en lista över databasresurser som ligger över storage_threshold inom de time_range definierade.
> - Utdata är en lista över databasresurser som ligger över storage_threshold inom de time_range definierade.

#### <a name="alert-on-intelligent-insights"></a>Varning om intelligenta insikter

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - Förkrav för att konfigurera den här aviseringen är att övervakade databaser strömmar SQLInsights diagnostiklogg till Azure SQL Analytics.
> - Den här frågan kräver att en aviseringsregel ställs in för att köras med samma frekvens som alert_run_interval för att undvika dubblettresultat. Regeln bör ställas in för att avfyra aviseringen när det finns resultat (> 0 resultat) från frågan.
> - Anpassa alert_run_interval för att ange tidsintervallet för att kontrollera om villkoret har inträffat på databaser som konfigurerats för att strömma SQLInsights-loggen till Azure SQL Analytics.
> - Anpassa insights_string för att fånga in utdata från analystexten för insights-orsaken. Det här är samma text som visas i användargränssnittet i Azure SQL Analytics som du kan använda från befintliga insikter. Du kan också använda frågan nedan för att se texten för alla insikter som genereras på din prenumeration. Använd utdata för frågan för att skörda de distinkta strängarna för att ställa in aviseringar på insikter.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Skapa aviseringar för hanterade instanser

#### <a name="managed-instance-storage-is-above-90"></a>Lagring av hanterade instanser är över 90 %

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - Pre-krav för att ställa in den här aviseringen är att övervakad den hanterade instansen har streaming av ResourceUsageStats loggen aktiverad till Azure SQL Analytics.
> - Den här frågan kräver att en aviseringsregel ställs in för att avfyra en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på den hanterade instansen. Utdata är lagringsprocentförbrukning för den hanterade instansen.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Hanterad instans CPU genomsnittlig förbrukning är över 95% under de senaste 1 tim

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - Pre-krav för att ställa in den här aviseringen är att den övervakade hanterade instansen har streaming av ResourceUsageStats loggen aktiverad till Azure SQL Analytics.
> - Den här frågan kräver att en aviseringsregel ställs in för att avfyra en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på den hanterade instansen. Utdata är genomsnittlig cpu-utnyttjande procentförbrukning under definierad period på den hanterade instansen.

### <a name="pricing"></a>Prissättning

Azure SQL Analytics är gratis att använda, men förbrukning av diagnostiktelemetri ovanför de kostnadsfria enheter av datainmatning som allokerats varje månad gäller, se [Log Analytics-priser](https://azure.microsoft.com/pricing/details/monitor). De kostnadsfria enheterna för datainmatning möjliggör fri övervakning av flera databaser varje månad. Mer aktiva databaser med tyngre arbetsbelastningar får in mer data jämfört med inaktiva databaser. Du kan enkelt övervaka din datainmatningsförbrukning i Azure SQL Analytics genom att välja OMS Workspace på navigeringsmenyn i Azure SQL Analytics och sedan välja Användning och uppskattade kostnader.

## <a name="next-steps"></a>Nästa steg

- Använd [loggfrågor](../log-query/log-query-overview.md) i Azure Monitor för att visa detaljerade Azure SQL-data.
- [Skapa egna instrumentpaneler](../learn/tutorial-logs-dashboards.md) som visar Azure SQL-data.
- [Skapa aviseringar](../platform/alerts-overview.md) när specifika Azure SQL-händelser inträffar.
