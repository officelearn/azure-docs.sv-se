---
title: Azure SQL Analytics-lösningen i Log Analytics | Microsoft Docs
description: Azure SQL Analytics-lösningen hjälper dig att hantera dina Azure SQL-databaser
services: log-analytics
documentationcenter: ''
author: danimir
manager: carmonm
ms.reviewer: carlrab
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: v-daljep
ms.component: na
ms.openlocfilehash: 47069f0af7409d87cb2d4fbbbce9dda0b1c2056e
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886568"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Övervaka Azure SQL-databaser med hjälp av Azure SQL Analytics (förhandsversion)

![Azure SQL Analytics-symbol](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics är ett moln som övervakningslösning för övervakning av prestanda för Azure SQL-databaser i stor skala över flera elastiska pooler och prenumerationer. Den samlar in och visar viktiga mått för Azure SQL Database-prestanda med inbyggd intelligens för prestandafelsökning längst upp. 

Genom att använda mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar. Lösningen hjälper dig att identifiera problem på varje lager i din programstack. Azure diagnostikmått tillsammans med vyer för Log Analytics används för att presentera data om dina Azure SQL-databaser och elastiska pooler i en enda Log Analytics-arbetsyta. Log Analytics hjälper dig att samla in, korrelera och visualisera strukturerade och Ostrukturerade data.

Den här förhandsversionen-lösningen stöder för närvarande upp till 150 000 Azure SQL-databaser och 5 000 elastiska SQL-pooler per arbetsyta.

Finns det inbäddade videoklippet för en praktisk översikt om hur du använder Azure SQL Analytics-lösningen och för vanliga Användningsscenarier:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Anslutna källor

Azure SQL Analytics är ett moln som övervakning lösning stödjande strömning av diagnostiktelemetri för Azure SQL-databaser och elastiska pooler. Eftersom den inte använder agenter för att ansluta till tjänsten Log Analytics kan lösningen stöder inte anslutning med Windows, Linux eller SCOM-resurser finns i tabellen kompatibilitet.

| Ansluten källa | Support | Beskrivning |
| --- | --- | --- |
| **[Azure-diagnostik](log-analytics-azure-storage.md)** | **Ja** | Azure mått och loggfiler data skickas till Log Analytics direkt av Azure. |
| [Azure Storage-konto](log-analytics-azure-storage.md) | Nej | Log Analytics läsa inte data från ett lagringskonto. |
| [Windows-agenter](log-analytics-windows-agent.md) | Nej | Direkta Windows-agenter används inte av lösningen. |
| [Linux-agenter](log-analytics-linux-agents.md) | Nej | Direct Linux-agenter används inte av lösningen. |
| [SCOM-hanteringsgrupp](log-analytics-om-agents.md) | Nej | En direktanslutning till Log Analytics från SCOM-agenten används inte av lösningen. |

## <a name="configuration"></a>Konfiguration

Utför följande steg för att lägga till Azure SQL Analytics-lösningen till din arbetsyta.

1. Lägga till Azure SQL Analytics-lösningen i din arbetsyta från [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. I Azure-portalen klickar du på **+ skapa en resurs**, Sök sedan efter **Azure SQL Analytics**.  
    ![Övervakning och hantering](./media/log-analytics-azure-sql/monitoring-management.png)
3. Välj **Azure SQL Analytics (förhandsversion)** i listan
4. I den **Azure SQL Analytics (förhandsversion)** området klickar du på **skapa**.  
    ![Skapa](./media/log-analytics-azure-sql/portal-create.png)
5. I den **Skapa ny lösning** område, skapa en ny eller välj en befintlig arbetsyta som du vill lägga till lösningen och klicka sedan på **skapa**.  
    ![Lägg till arbetsytan](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Konfigurera Azure SQL-databaser och elastiska pooler till stream-diagnostiktelemetri

När du har skapat Azure SQL Analytics-lösningen i din arbetsyta, för att övervaka prestanda för Azure SQL-databaser och/eller elastiska pooler behöver du **konfigurera var och en** av Azure SQL-databas och elastisk pool-resurs som du vill övervaka för att strömma dess diagnostiktelemetri till lösningen.

- Aktivera Azure Diagnostics för din Azure SQL-databaser och elastiska pooler och [konfigurera dem för att skicka sina data till Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Konfigurera flera Azure-prenumerationer

För att stödja flera prenumerationer, använder du PowerShell-skriptet från [aktivera Azure resource mått loggning med hjälp av PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Ange resurs-ID för arbetsyta som en parameter när du kör skriptet för att skicka diagnostikdata från resurser i en Azure-prenumeration till en arbetsyta i en annan Azure-prenumeration.

**Exempel**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till lösningen till din arbetsyta, Azure SQL Analytics-panel har lagts till din arbetsyta och den visas i Översikt. Panelen visar antal Azure SQL-databaser och elastiska Azure SQL-pooler som lösningen är ansluten till.

![Azure SQL Analytics-ikonen](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Visa Azure SQL Analytics-data

Klicka på den **Azure SQL Analytics** panelen för att öppna instrumentpanelen för Azure SQL Analytics. Instrumentpanelen innehåller en översikt över alla databaser som övervakas via olika perspektiv. För olika perspektiv ska fungera måste du aktivera rätt mått eller loggar på din SQL-resurser för att strömmas till Azure Log Analytics-arbetsyta.

![Översikt över Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Att välja någon av panelerna, öppnar en rapport för nedåt i specifika perspektiv. När perspektiv är markerad öppnas rapporten nedåt.

![Timeout för Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Varje perspektiv innehåller sammanfattningar om prenumeration, server, elastisk pool och databasnivå. Dessutom kan visar varje perspektiv ett perspektiv specifika i rapporten till höger. Att välja prenumeration, server, poolen eller databasen i listan fortsätter att gå nedåt.

| Perspektiv | Beskrivning |
| --- | --- |
| Resursen efter typ | Perspektiv som räknar alla resurser som övervakas. Detaljnivå innehåller en sammanfattning av DTU och GB-mått. |
| Insikter | Tillhandahåller hierarkisk nedåt till smarta insikter. Läs mer om intelligenta insikter. |
| Fel | Tillhandahåller hierarkisk nedåt till SQL-fel som inträffat på databaserna. |
| Timeouter | Tillhandahåller hierarkisk nedåt till SQL-timeouter som inträffat på databaserna. |
| Blockeringar | Tillhandahåller hierarkisk nedåt till SQL-blockings som inträffat på databaserna. |
| Databasen väntar | Tillhandahåller hierarkisk nedåt till SQL vänta statistik på databasnivå. Omfattar sammanfattningar av total Väntetid och väntetiden per typ av vänta. |
| Frågevaraktighet | Tillhandahåller hierarkisk detaljnivå om frågestatistik för körning som frågevaraktigheten, CPU-användning, Data-i/o-användning, logg-i/o-användning. |
| Frågan väntar | Tillhandahåller hierarkisk detaljnivå om vänta frågestatistik efter vänta kategori. |

### <a name="intelligent-insights-report"></a>Intelligent Insights-rapport

Azure SQL Database [smarta insikter](../sql-database/sql-database-intelligent-insights.md) gör att du vet vad som händer med din databasprestanda. Alla smarta insikter som samlas in kan visualiseras och nås via Insights perspektiv.

![Azure SQL Analytics insikter](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastiska pooler och rapporter

Både elastiska pooler och databaser har sina egna specifika rapporter som visar alla data som samlas in för resursen i den angivna tiden.

![Azure SQL Analytics-databas](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Azure SQL Analytics elastisk Pool](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Fråga rapporter

Du kan jämföra prestanda för alla frågor via fråga rapporten via frågevaraktigheten och frågan väntar perspektiv. Den här rapporten jämför prestanda för frågor mellan olika databaser och gör det enkelt att identifiera databaser som utför den valda frågan bra jämfört med de som är långsamma.

![Azure SQL Analytics-frågor](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analysera data och skapa aviseringar

Du kan enkelt [skapa aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) med data från Azure SQL Database-resurser. Här är några användbara [loggsökning](log-analytics-log-searches.md) frågor som du kan använda med en logg-avisering:

*Hög CPU i Azure SQL-databas*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Före kravet för att ställa in den här aviseringen är den övervakade databaser stream diagnostikmåtten (alternativet ”alla mått”) till lösningen.
> - Ersätt MetricName värdet cpu_percent med dtu_consumption_percent att få hög DTU-resultaten i stället.

*Hög CPU på elastiska pooler i Azure SQL Database*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Före kravet för att ställa in den här aviseringen är den övervakade databaser stream diagnostikmåtten (alternativet ”alla mått”) till lösningen.
> - Ersätt MetricName värdet cpu_percent med dtu_consumption_percent att få hög DTU-resultaten i stället.

*Azure SQL Database-lagring i genomsnitt över 95% i den senaste 1 tim*

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
> - Före kravet för att ställa in den här aviseringen är den övervakade databaser stream diagnostikmåtten (alternativet ”alla mått”) till lösningen.
> - Den här frågan kräver en aviseringsregel som ställas in för att ställa in en avisering när det finns resultat (> 0 resultat) från frågan anger att villkoret finns på vissa databaser. Utdata är en lista över databasresurser som är ovanför storage_threshold inom time_range som definierats.
> - Utdata är en lista över databasresurser som är ovanför storage_threshold inom time_range som definierats.

*Avisera om smarta insikter*

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
> - Före kravet för att ställa in den här aviseringen är övervakade databaser stream SQLInsights diagnostikloggen till lösningen.
> - Den här frågan kräver en varningsregel konfigureras att köras med samma frekvens som alert_run_interval för att undvika dubbla resultat. Regeln ska ställas in för att utlösa aviseringen när det finns resultat (> 0 resultat) från frågan.
> - Anpassa alert_run_interval för att ange tidsintervall för att kontrollera om villkoret har uppstått på databaser som har konfigurerats till stream SQLInsights loggen till lösningen.
> - Anpassa insights_string för att samla in utdata från insikter rot orsak analysis text. Det här är samma text som visas i Användargränssnittet för den lösning som du kan använda från de befintliga insikterna. Du kan också använda frågan nedan för att se texten i alla information som genererats för din prenumeration. Använda utdata från frågan för att inhämta distinkta strängar för att ställa in aviseringar på insikter.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| distinct rootCauseAnalysis_s
```

## <a name="next-steps"></a>Nästa steg

- Använd [Loggsökningar](log-analytics-log-searches.md) i Log Analytics för att visa detaljerad Azure SQL-data.
- [Skapa dina egna instrumentpaneler](log-analytics-dashboards.md) visar Azure SQL-data.
- [Skapa aviseringar](log-analytics-alerts.md) när specifika Azure SQL-händelser inträffar.
