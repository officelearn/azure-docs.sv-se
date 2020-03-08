---
title: Azure SQL-analys lösning i Azure Monitor | Microsoft Docs
description: Azure SQL Analytics-lösningen hjälper dig att hantera dina Azure SQL-databaser
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365425"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Övervaka Azure SQL Database med Azure SQL Analytics (förhandsversion)

![Azure SQL Analytics-symbol](./media/azure-sql/azure-sql-symbol.png)

Azure SQL-analys är en avancerad lösning för moln övervakning för att övervaka prestanda för alla dina Azure SQL-databaser i stor skala och över flera prenumerationer i en enda vy. Azure SQL-analys samlar in och visualiserar viktiga prestanda mått med inbyggd intelligens för prestanda fel sökning.

Genom att använda dessa insamlade mått kan du skapa anpassade övervaknings regler och aviseringar. Azure SQL-analys hjälper dig att identifiera problem på varje lager i program stacken. Azure Diagnostic-mått används tillsammans med Azure Monitor vyer för att presentera data om alla dina Azure SQL-databaser på en enda Log Analytics arbets yta. Azure Monitor hjälper dig att samla in, korrelera och visualisera strukturerade och ostrukturerade data.

Finns det inbäddade videoklippet för en praktisk översikt om hur du använder Azure SQL Analytics-lösningen och för vanliga Användningsscenarier:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Anslutna källor

Azure SQL-analys är en övervaknings lösning för endast moln som stöder strömning av diagnostisk telemetri för alla dina Azure SQL-databaser. Eftersom Azure SQL-analys inte använder agenter för att ansluta till Azure Monitor, stöder den inte övervakning av SQL Server som finns lokalt eller i virtuella datorer.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Diagnostikinställningar](../platform/diagnostic-settings.md) | **Ja** | Azure Metric-och loggdata skickas till Azure Monitor loggar direkt av Azure. |
| [Azure Storage-konto](../platform/collect-azure-metrics-logs.md) | Nej | Azure Monitor läser inte data från ett lagrings konto. |
| [Windows-agenter](../platform/agent-windows.md) | Nej | Direkta Windows-agenter används inte av Azure SQL-analys. |
| [Linux-agenter](../learn/quick-collect-linux-computer.md) | Nej | Direct Linux-agenter används inte av Azure SQL-analys. |
| [System Center Operations Manager hanterings grupp](../platform/om-agents.md) | Nej | En direkt anslutning från Operations Manager agent till Azure Monitor används inte av Azure SQL-analys. |

## <a name="azure-sql-analytics-options"></a>Azure SQL-analys alternativ

I tabellen nedan visas vilka alternativ som stöds för två versioner av Azure SQL-analys-instrumentpanelen, en för enskilda databaser och elastiska pooler och den andra för hanterade instanser och instans databaser.

| Azure SQL-analys alternativ | Beskrivning | Stöd för enkel och poolad databas och elastiska pooler | Stöd för hanterade instanser och instans databaser |
| --- | ------- | ----- | ----- |
| Resursen efter typ | Perspektiv som räknar alla resurser som övervakas. | Ja | Ja |
| Insikter | Tillhandahåller hierarkisk nedåt till smarta insikter i prestanda. | Ja | Ja |
| Fel | Tillhandahåller hierarkisk nedåt till SQL-fel som inträffat på databaserna. | Ja | Ja |
| Timeouter | Tillhandahåller hierarkisk nedåt till SQL-timeouter som inträffat på databaserna. | Ja | Nej |
| Blockeringar | Tillhandahåller hierarkisk nedåt till SQL-blockings som inträffat på databaserna. | Ja | Nej |
| Databasen väntar | Tillhandahåller hierarkisk nedåt till SQL vänta statistik på databasnivå. Omfattar sammanfattningar av total Väntetid och väntetiden per typ av vänta. |Ja | Nej |
| Frågevaraktighet | Tillhandahåller hierarkisk detaljnivå om frågestatistik för körning som frågevaraktigheten, CPU-användning, Data-i/o-användning, logg-i/o-användning. | Ja | Ja |
| Frågan väntar | Tillhandahåller hierarkisk detaljnivå om vänta frågestatistik efter vänta kategori. | Ja | Ja |

## <a name="configuration"></a>Konfiguration

Använd processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](../../azure-monitor/insights/solutions.md) för att lägga till Azure SQL-analys (för hands version) i Log Analytics arbets ytan.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Konfigurera Azure SQL-databaser till Stream Diagnostics-telemetri

När du har skapat Azure SQL-analys lösning i din arbets yta måste du **Konfigurera varje** resurs som du vill övervaka för att strömma sin diagnostik-telemetri till Azure SQL-analys. Följ instruktionerna på sidan:

- Aktivera Azure-diagnostik för att din Azure SQL-databas ska kunna [strömma telemetri till Azure SQL-analys](../../sql-database/sql-database-metrics-diag-logging.md).

Sidan ovan innehåller också anvisningar om hur du aktiverar stöd för övervakning av flera Azure-prenumerationer från en enda Azure SQL Analytics-arbetsyta som en enda glasruta.

## <a name="using-azure-sql-analytics"></a>Använda Azure SQL-analys

När du lägger till Azure SQL-analys på din arbets yta läggs panelen Azure SQL-analys till i din arbets yta och visas i Översikt. Välj Visa sammanfattnings länk för att läsa in panelens innehåll.

![Azure SQL-analys sammanfattnings panel](./media/azure-sql/azure-sql-sol-tile-01.png)

När den har lästs in visar panelen antalet databaser med enkel databas, elastiska pooler, hanterade instanser och hanterade instans databaser från vilka Azure SQL-analys tar emot telemetri för diagnostik.

![Azure SQL Analytics-ikonen](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL-analys innehåller två separata vyer – en för övervakning av enskilda databaser och databaser i pooler och elastiska pooler och den andra vyn för övervakning av hanterade instanser och instans databaser.

Klicka på den övre delen av panelen om du vill visa Azure SQL-analys övervaknings instrument panel för enskilda databaser och elastiska pooler. Om du vill visa instrument panelen för Azure SQL-analys övervakning för hanterade instanser och instans databaser klickar du på den nedre delen av panelen.

### <a name="viewing-azure-sql-analytics-data"></a>Visa Azure SQL Analytics-data

Instrumentpanelen innehåller en översikt över alla databaser som övervakas via olika perspektiv. För att olika perspektiv ska fungera måste du aktivera lämpliga mått eller loggar på dina SQL-resurser för att strömma till Log Analytics arbets ytan.

Om vissa mått eller loggar inte strömmas i Azure Monitor, fylls inte panelerna i Azure SQL-analys med övervaknings information.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Enkel-och poolbaserade databaser och vyn elastiska pooler

När Azure SQL Analytics-ikonen för databasen är valt visas instrumentpanelen för övervakning.

![Översikt över Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Att välja någon av panelerna, öppnar en rapport för nedåt i specifika perspektiv. När perspektiv är markerad öppnas rapporten nedåt.

![Timeout för Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Varje perspektiv i den här vyn innehåller sammanfattningar i prenumerationen, servern, elastisk pool och databas nivåer. Dessutom kan visar varje perspektiv ett perspektiv specifika i rapporten till höger. Att välja prenumeration, server, poolen eller databasen i listan fortsätter att gå nedåt.

### <a name="managed-instance-and-instances-databases-view"></a>Vy över hanterade instanser och instans databaser

När Azure SQL Analytics-ikonen för databaserna är valt visas instrumentpanelen för övervakning.

![Översikt över Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Att välja någon av panelerna, öppnar en rapport för nedåt i specifika perspektiv. När perspektiv är markerad öppnas rapporten nedåt.

Genom att välja vyn hanterad instans visas information om användningen av hanterade instanser, databaser den innehåller och telemetri för de frågor som körs över instansen.

![Timeout för Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights-rapport

Azure SQL Database [intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) kan du veta vad som händer med prestanda för alla Azure SQL-databaser. Alla smarta insikter som samlas in kan visualiseras och nås via Insights perspektiv.

![Azure SQL Analytics insikter](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Elastiska pooler och databas rapporter

Både elastiska pooler och databaser har sina egna specifika rapporter som visar alla data som har samlats in för resursen under den angivna tiden.

![Azure SQL Analytics-databas](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL-databaspool](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Fråga rapporter

Genom frågans varaktighet och frågan väntar dig perspektiv, kan du korrelera prestandan för alla frågor via fråge rapporten. Den här rapporten jämför prestanda för frågor mellan olika databaser och gör det enkelt att identifiera databaser som utför den valda frågan bra jämfört med de som är långsamma.

![Azure SQL Analytics-frågor](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Behörigheter

Om du vill använda Azure SQL Analytics måste användare tilldelas en minsta behörighet för rollen läsare i Azure. Den här rollen kan dock inte tillåta användare att se frågetexten eller utföra någon automatisk justering åtgärder. Fler tillåtna roller i Azure som gör det möjligt att använda Azure SQL-analys i hela utsträckning är ägare, deltagare, SQL DB-deltagare eller SQL Server deltagare. Du kanske också vill du skapa en anpassad roll i portalen med specifika behörigheter som krävs endast för att använda Azure SQL Analytics och ingen åtkomst till hantering av andra resurser.

### <a name="creating-a-custom-role-in-portal"></a>Skapa en anpassad roll i portalen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Upptäcker att vissa organisationer framtvinga strikt behörighetskontroller i Azure, hitta följande PowerShell-skript som aktiverar en anpassad roll ”SQL Analytics övervakning-operatör” i Azure-portalen med minsta Läs- och skrivbehörighet krävs för att skapa använda Azure SQL Analytics så stort som möjligt.

Ersätt ”{SubscriptionId}” i den med ditt Azure-prenumerations-ID-skriptet nedan och kör skriptet med inloggad som en ägare eller deltagare roll i Azure.

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

När den nya rollen har skapats kan du tilldela den här rollen till varje användare som du måste tilldela anpassade behörigheter för att använda Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analysera data och skapa aviseringar

Data analys i Azure SQL-analys baseras på [Log Analytics språk](../log-query/get-started-queries.md) för din anpassade fråga och rapportering. Hitta en beskrivning av tillgängliga data som samlats in från databas resursen för anpassade frågor i [mått och loggar tillgängliga](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatiserad avisering i Azure SQL-analys baseras på att skriva en Log Analytics fråga som utlöser en avisering när ett villkor uppfylls. Det finns flera exempel på hur du Log Analytics frågor om vilka aviseringar som kan konfigureras i Azure SQL-analys.

### <a name="creating-alerts-for-azure-sql-database"></a>Skapa aviseringar för Azure SQL Database

Du kan enkelt [skapa aviseringar](../platform/alerts-metric.md) med de data som kommer från Azure SQL Database-resurser. Här följer några användbara [logg frågor](../log-query/log-query-overview.md) som du kan använda med en logg avisering:

#### <a name="high-cpu-on-azure-sql-database"></a>Hög CPU i Azure SQL-databas

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
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser med grundläggande mått för att Azure SQL-analys.
> - Ersätt MetricName värdet cpu_percent med dtu_consumption_percent att få hög DTU-resultaten i stället.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Hög CPU på Azure SQL Database elastiska pooler

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
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser med grundläggande mått för att Azure SQL-analys.
> - Ersätt MetricName värdet cpu_percent med dtu_consumption_percent att få hög DTU-resultaten i stället.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Azure SQL Database-lagring i genomsnitt över 95% i den senaste 1 tim

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
> - Före kravet på att ställa in den här aviseringen är att övervakade databaser med grundläggande mått för att Azure SQL-analys.
> - Den här frågan kräver en aviseringsregel som ställas in för att ställa in en avisering när det finns resultat (> 0 resultat) från frågan anger att villkoret finns på vissa databaser. Utdata är en lista över databasresurser som är ovanför storage_threshold inom time_range som definierats.
> - Utdata är en lista över databasresurser som är ovanför storage_threshold inom time_range som definierats.

#### <a name="alert-on-intelligent-insights"></a>Avisera om smarta insikter

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
> - Före kravet på att ställa in den här aviseringen är att den övervakade databaserna för SQLInsights-diagnostik i databasen Azure SQL-analys.
> - Den här frågan kräver en varningsregel konfigureras att köras med samma frekvens som alert_run_interval för att undvika dubbla resultat. Regeln ska ställas in för att utlösa aviseringen när det finns resultat (> 0 resultat) från frågan.
> - Anpassa alert_run_interval för att ange tidsintervallet för att kontrol lera om tillståndet har inträffat på databaser som har kon figurer ATS att strömma SQLInsights-loggen till Azure SQL-analys.
> - Anpassa insights_string för att samla in utdata från insikter rot orsak analysis text. Det här är samma text som visas i användar gränssnittet för Azure SQL-analys som du kan använda från befintliga insikter. Du kan också använda frågan nedan för att se texten i alla information som genererats för din prenumeration. Använda utdata från frågan för att inhämta distinkta strängar för att ställa in aviseringar på insikter.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Skapa aviseringar för hanterade instanser

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
>
> - Krav för att ställa in den här aviseringen är att övervakad den hanterade instansen har strömningen av ResourceUsageStats logg aktive rad till Azure SQL-analys.
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
>
> - Krav för att ställa in den här aviseringen är att den övervakade hanterade instansen har strömningen av ResourceUsageStats logg aktive rad till Azure SQL-analys.
> - Den här frågan kräver att en varnings regel har kon figurer ATS för att utlösa en avisering när det finns resultat (> 0 resultat) från frågan, vilket anger att villkoret finns på den hanterade instansen. Utdatan är genomsnitts förbrukningen i procent för processor användning i definierad period på den hanterade instansen.

### <a name="pricing"></a>Priser

Även om Azure SQL-analys är kostnads fritt att använda, kan förbrukningen av diagnostik över de kostnads fria enheterna för inmatningen av data som tilldelas varje månad användas, se [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/monitor). De kostnadsfria enheterna för datainmatning tillhandahålls aktivera kostnadsfri övervakning av flera databaser varje månad. Fler aktiva databaser med tyngre arbets belastningar inhämtar mer data jämfört med inaktiva databaser. Du kan enkelt övervaka förbrukningen av data inmatning i Azure SQL-analys genom att välja OMS-arbetsyta på navigerings menyn i Azure SQL-analys och sedan välja användning och beräknade kostnader.

## <a name="next-steps"></a>Nästa steg

- Använd [logg frågor](../log-query/log-query-overview.md) i Azure Monitor om du vill visa detaljerade Azure SQL-data.
- [Skapa egna instrument paneler](../learn/tutorial-logs-dashboards.md) som visar Azure SQL-data.
- [Skapa aviseringar](../platform/alerts-overview.md) när vissa Azure SQL-händelser inträffar.
