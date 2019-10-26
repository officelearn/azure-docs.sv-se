---
title: Azure SQL-analys lösning i Azure Monitor | Microsoft Docs
description: Azure SQL-analys lösning hjälper dig att hantera dina Azure SQL-databaser
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 12/17/2018
ms.reviewer: carlrab
ms.openlocfilehash: 75e8cffea08c7db4526c647a32ed92be2d1779f1
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899133"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Övervaka Azure SQL Database att använda Azure SQL-analys (förhands granskning)

![Azure SQL-analys symbol](./media/azure-sql/azure-sql-symbol.png)

Azure SQL-analys är en avancerad lösning för moln övervakning för att övervaka prestanda för Azure SQL-databaser, elastiska pooler och hanterade instanser i stor skala och över flera prenumerationer via ett enda fönster i glaset. Den samlar in och visar viktiga mått för Azure SQL Database-prestanda med inbyggd intelligens för prestandafelsökning.

Genom att använda mått som du samlar in med lösningen kan du skapa anpassade övervaknings regler och aviseringar. Lösningen hjälper dig att identifiera problem på varje skikt i program stacken. Azures diagnostiska mått används tillsammans med Azure Monitor vyer för att presentera data om alla dina Azure SQL-databaser, elastiska pooler och databaser i hanterade instanser i en enda Log Analytics arbets yta. Azure Monitor hjälper dig att samla in, korrelera och visualisera strukturerade och ostrukturerade data.

En praktisk översikt över hur du använder Azure SQL-analys lösning och för vanliga användnings scenarier finns i inbäddad video:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Anslutna källor

Azure SQL-analys är en övervaknings lösning för endast moln som stöder strömning av diagnostisk telemetri för Azure SQL-databaser: enkla, pooler och hanterade instans databaser. Eftersom lösningen inte använder agenter för att ansluta till Azure Monitor, kan lösningen inte hantera övervakning av SQL Server som finns lokalt eller i virtuella datorer, se tabellen Compatibility nedan.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Diagnostikinställningar](../platform/diagnostic-settings.md) | **Ja** | Azure Metric-och loggdata skickas till Azure Monitor loggar direkt av Azure. |
| [Azure Storage-konto](../platform/collect-azure-metrics-logs.md) | Nej | Azure Monitor läser inte data från ett lagrings konto. |
| [Windows-agenter](../platform/agent-windows.md) | Nej | Direkta Windows-agenter används inte av lösningen. |
| [Linux-agenter](../learn/quick-collect-linux-computer.md) | Nej | Direct Linux-agenter används inte av lösningen. |
| [System Center Operations Manager hanterings grupp](../platform/om-agents.md) | Nej | En direkt anslutning från Operations Manager agent till Azure Monitor används inte av lösningen. |

## <a name="configuration"></a>Konfiguration
Använd processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](../../azure-monitor/insights/solutions.md) för att lägga till Azure SQL-analys (för hands version)-lösningen i Log Analytics arbets ytan.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Konfigurera Azure SQL-databaser, elastiska pooler och hanterade instanser för att strömma telemetri för diagnostik

När du har skapat Azure SQL-analys lösning på arbets ytan måste du **Konfigurera varje** resurs som du vill övervaka för att strömma sin diagnostik-telemetri till lösningen. Följ detaljerade instruktioner på den här sidan:

- Aktivera Azure-diagnostik för att din Azure SQL-databas ska kunna [strömma telemetri till Azure SQL-analys](../../sql-database/sql-database-metrics-diag-logging.md).

Sidan ovan innehåller också instruktioner om hur du aktiverar stöd för övervakning av flera Azure-prenumerationer från en enda Azure SQL-analys arbets yta som ett enda fönster i glaset.

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till lösningen i din arbets yta läggs panelen Azure SQL-analys till i din arbets yta och visas i Översikt. Välj Visa sammanfattnings länk för att läsa in panelens innehåll.

![Azure SQL-analys sammanfattnings panel](./media/azure-sql/azure-sql-sol-tile-01.png)

När den har lästs in visar panelen antalet Azure SQL-databaser, elastiska pooler, hanterade instanser och databaser i hanterade instanser som lösningen tar emot telemetri från.

![Azure SQL-analys panel](./media/azure-sql/azure-sql-sol-tile-02.png)

Lösningen innehåller två separata vyer – en för övervakning av Azure SQL-databaser och elastiska pooler och den andra vyn för övervakning av hanterade instanser och databaser i hanterade instanser.

Om du vill visa Azure SQL-analys Monitoring Dashboard för Azure SQL-databaser och elastiska pooler klickar du på den övre delen av panelen. Om du vill visa Azure SQL-analys övervaknings instrument panel för hanterad instans och databaser i hanterad instans klickar du på den nedre delen av panelen.

### <a name="viewing-azure-sql-analytics-data"></a>Visa Azure SQL-analys data

Instrument panelen innehåller en översikt över alla databaser som övervakas via olika perspektiv. För att olika perspektiv ska fungera måste du aktivera lämpliga mått eller loggar på dina SQL-resurser för att strömma till Log Analytics arbets ytan.

Observera att om vissa mått eller loggar inte strömmas i Azure Monitor, fylls inte panelerna i lösningen med övervaknings information.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Vy över Azure SQL Database och elastisk pool

När Azure SQL-analys panelen för databasen har valts visas övervaknings instrument panelen.

![Översikt över Azure SQL-analys](./media/azure-sql/azure-sql-sol-overview.png)

Om du väljer någon av panelerna öppnas en detaljerad rapport i det aktuella perspektivet. När perspektivet är markerat öppnas rapporten för att öka detalj nivån.

![Azure SQL-analys tids gränser](./media/azure-sql/azure-sql-sol-metrics.png)

Varje perspektiv i den här vyn innehåller sammanfattningar av prenumeration, Server, elastisk pool och databas nivå. Dessutom visar varje perspektiv ett perspektiv som är särskilt för rapporten till höger. Genom att välja prenumeration, Server, pool eller databas i listan fortsätter du att öka detalj nivån.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Hanterad instans och databaser i vyn hanterad instans

När Azure SQL-analys panelen för databaserna är markerad visas övervaknings instrument panelen.

![Översikt över Azure SQL-analys](./media/azure-sql/azure-sql-sol-overview-mi.png)

Om du väljer någon av panelerna öppnas en detaljerad rapport i det aktuella perspektivet. När perspektivet är markerat öppnas rapporten för att öka detalj nivån.

Välja vyn hanterad instans, visar information om den hanterade instansen, databaser den innehåller och telemetri för de frågor som körs över instansen.

![Azure SQL-analys tids gränser](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspektiv

I tabellen nedan beskrivs perspektiv som stöds för två versioner av instrument panelen, en för Azure SQL Database och elastiska pooler, och den andra för en hanterad instans.

| Perspektivvyn | Beskrivning | Stöd för SQL Database och elastiska pooler | Stöd för hanterade instanser |
| --- | ------- | ----- | ----- |
| Resurs efter typ | Perspektiv som räknar alla övervakade resurser. | Ja | Ja |
| Insikter | Tillhandahåller hierarkisk visning av Intelligent Insights i prestanda. | Ja | Ja |
| Fel | Tillhandahåller hierarkisk visning av SQL-fel som har inträffat i databaserna. | Ja | Ja |
| Timeouter | Tillhandahåller hierarkisk visning av SQL-tidsgräns som har inträffat i databaserna. | Ja | Nej |
| Blockning | Tillhandahåller hierarkisk visning av SQL-block som har inträffat i databaserna. | Ja | Nej |
| Databasen väntar | Ger hierarkisk visning av SQL wait-statistik på databas nivå. Innehåller sammanfattningar av den totala vänte tiden och vänte tiden per wait-typ. |Ja | Ja |
| Frågans varaktighet | Tillhandahåller hierarkisk visning av statistik för frågekörning, till exempel fråge varaktighet, CPU-användning, data-i/o-användning, logg-i/o-användning. | Ja | Ja |
| Frågan väntar | Tillhandahåller hierarkisk visning i frågan vänta i statistiken efter kategorin vänta. | Ja | Ja |

### <a name="intelligent-insights-report"></a>Intelligent Insights rapport

Azure SQL Database [intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) kan du veta vad som händer med prestanda för alla Azure SQL-databaser. Alla Intelligent Insights som samlas in kan visualiseras och nås via insikter perspektivet.

![Azure SQL-analys insikter](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastisk pool och databas rapporter

Både elastiska pooler och SQL-databaser har sina egna specifika rapporter som visar alla data som har samlats in för resursen under den angivna tiden.

![Azure SQL-analys databas](./media/azure-sql/azure-sql-sol-database.png)

![Elastisk Azure SQL-pool](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Fråga rapporter

Genom frågans varaktighet och frågan väntar dig perspektiv, kan du korrelera prestandan för alla frågor via fråge rapporten. I den här rapporten jämförs frågans prestanda i olika databaser och det är enkelt att hitta databaser som utför den valda frågan och de som är långsamma.

![Azure SQL-analys frågor](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Behörigheter

För att kunna använda Azure SQL-analys måste användarna beviljas en lägsta behörighet för rollen läsare i Azure. Den här rollen tillåter dock inte användare att se frågetexten eller utföra automatiska justerings åtgärder. Fler tillåtna roller i Azure som gör det möjligt att använda lösningen i hela utsträckning är ägare, deltagare, SQL DB-deltagare eller SQL Server deltagare. Du kanske också vill överväga att skapa en anpassad roll i portalen med vissa behörigheter som krävs endast för att använda Azure SQL-analys och utan åtkomst till hantering av andra resurser.

### <a name="creating-a-custom-role-in-portal"></a>Skapa en anpassad roll i portalen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Genom att känna till att vissa organisationer tillämpar strikta behörighets kontroller i Azure hittar du följande PowerShell-skript som aktiverar skapandet av en anpassad roll "SQL Analytics Monitoring-operatör" i Azure Portal med de lägsta Läs-och skriv behörigheterna som krävs för att Använd Azure SQL-analys i hela utsträckning.

Ersätt "{SubscriptionId}" i skriptet nedan med ditt Azure-prenumerations-ID och kör skriptet som är inloggat som en ägare eller deltagar roll i Azure.

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

När den nya rollen har skapats tilldelar du rollen till varje användare som du behöver för att ge anpassade behörigheter att använda Azure SQL-analys.

## <a name="analyze-data-and-create-alerts"></a>Analysera data och skapa aviseringar

Data analys i Azure SQL-analys baseras på [Log Analytics språk](../log-query/get-started-queries.md) för din anpassade fråga och rapportering. Hitta en beskrivning av tillgängliga data som samlats in från databas resursen för anpassade frågor i [mått och loggar tillgängliga](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatiserad avisering i lösningen baseras på att skriva en Log Analytics fråga som utlöser en avisering när ett villkor uppfylls. Nedan finns flera exempel på Log Analytics frågor om vilka aviseringar som kan konfigureras i lösningen.

### <a name="creating-alerts-for-azure-sql-database"></a>Skapa aviseringar för Azure SQL Database

Du kan enkelt [skapa aviseringar](../platform/alerts-metric.md) med de data som kommer från Azure SQL Database-resurser. Här följer några användbara [logg frågor](../log-query/log-query-overview.md) som du kan använda med en logg avisering:

#### <a name="high-cpu-on-azure-sql-database"></a>Hög CPU på Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser i grundläggande mått till lösningen.
> - Ersätt MetricName-värdet cpu_percent med dtu_consumption_percent för att få höga DTU-resultat i stället.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Hög processor på Azure SQL Database elastiska pooler

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser i grundläggande mått till lösningen.
> - Ersätt MetricName-värdet cpu_percent med dtu_consumption_percent för att få höga DTU-resultat i stället.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Azure SQL Database lagring i genomsnitt över 95% under de senaste 1 timmena

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
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser i grundläggande mått till lösningen.
> - Den här frågan kräver att en varnings regel har kon figurer ATS för att utlösa en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på vissa databaser. Utdata är en lista över databas resurser som är ovanför storage_threshold i den time_range som definierats.
> - Utdata är en lista över databas resurser som är ovanför storage_threshold i den time_range som definierats.

#### <a name="alert-on-intelligent-insights"></a>Avisering om intelligenta insikter

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
> - Krav på att ställa in den här aviseringen är att den övervakade databaserna för SQLInsights-diagnostik i data strömmen till lösningen.
> - Den här frågan kräver att en varnings regel konfigureras att köras med samma frekvens som alert_run_interval för att undvika dubbla resultat. Regeln ska ställas in för att starta aviseringen när det finns resultat (> 0 resultat) från frågan.
> - Anpassa alert_run_interval för att ange tidsintervallet för att kontrol lera om villkoret har inträffat på databaser som har kon figurer ATS att strömma SQLInsights-loggen till lösningen.
> - Anpassa insights_string för att avbilda utdata från analys texten i rotor Saks analys. Det här är samma text som visas i användar gränssnittet för den lösning som du kan använda från befintliga insikter. Du kan också använda frågan nedan om du vill se texten för alla insikter som genererats i din prenumeration. Använd utdata från frågan för att skörda de distinkta strängarna för att ställa in aviseringar för insikter.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Skapar aviseringar för hanterad instans

#### <a name="managed-instance-storage-is-above-90"></a>Hanterad instans lagring är över 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Krav för att ställa in den här aviseringen är att övervakad hanterad instans har strömningen av ResourceUsageStats-logg som är aktive rad för lösningen.
> - Den här frågan kräver att en varnings regel har kon figurer ATS för att utlösa en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på den hanterade instansen. Utdatan är lagrings procent användning på den hanterade instansen.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Genomsnittlig förbrukning för hanterad instans CPU är över 95% under de senaste 1 timmena

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Krav för att ställa in den här aviseringen är att övervakad hanterad instans har strömningen av ResourceUsageStats-logg som är aktive rad för lösningen.
> - Den här frågan kräver att en varnings regel har kon figurer ATS för att utlösa en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på den hanterade instansen. Utdatan är genomsnitts förbrukningen i procent för processor användning i definierad period på den hanterade instansen.

### <a name="pricing"></a>Prissättning

Även om lösningen är kostnads fri att använda, kan användning av telemetri över de kostnads fria enheterna för data inmatning som tilldelas varje månad se [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/monitor). De kostnads fria enheterna av data inmatning möjliggör kostnads fri övervakning av flera databaser varje månad. Observera att fler aktiva databaser med tyngre arbets belastningar inhämtar mer data jämfört med inaktiva databaser. Du kan enkelt övervaka förbrukningen av data inmatning i lösningen genom att välja OMS-arbetsyta på navigerings menyn i Azure SQL-analys och sedan välja användning och beräknade kostnader.

## <a name="next-steps"></a>Nästa steg

- Använd [logg frågor](../log-query/log-query-overview.md) i Azure Monitor om du vill visa detaljerade Azure SQL-data.
- [Skapa egna instrument paneler](../learn/tutorial-logs-dashboards.md) som visar Azure SQL-data.
- [Skapa aviseringar](../platform/alerts-overview.md) när vissa Azure SQL-händelser inträffar.
