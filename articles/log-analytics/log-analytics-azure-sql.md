---
title: Azure SQL Analytics lösning i Log Analytics | Microsoft Docs
description: Azure SQL Analytics lösningen hjälper dig att hantera dina Azure SQL-databaser.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 722a10e853f6d61bb5349e92754954e3bb199225
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Övervaka Azure SQL Database med Azure SQL Analytics (förhandsgranskning) i logganalys

![Azure SQL Analytics symbol](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics lösningen i Azure Log Analytics samlar in och visualizes viktiga mått för prestanda för SQL Azure. Med det mått som du samlar in med lösningen kan skapa du anpassade regler för övervakning och aviseringar. Du kan övervaka Azure SQL Database och elastisk pool mått över flera Azure-prenumerationer och elastiska pooler och visualisera dem. Lösningen hjälper dig att identifiera problem på varje nivå i program-stacken.  Den använder [Azure diagnostisk mått](log-analytics-azure-storage.md) tillsammans med logganalys vyer presentera data om dina Azure SQL-databaser och elastiska pooler i en enda logganalys-arbetsyta.

Den här preview-lösningen stöder för närvarande, upp till 150 000 Azure SQL-databaser och 5 000 SQL elastiska pooler per arbetsytan.

Azure SQL Analytics-lösningen, precis som andra tillgängliga för Log Analytics hjälper dig att övervaka och ta emot meddelanden om hälsotillståndet för dina Azure-resurser – i det här fallet, Azure SQL Database. Microsoft Azure SQL Database är en skalbar relationsdatabas-tjänst som tillhandahåller välbekanta SQL-Server-liknande funktioner till program som körs i Azure-molnet. Logganalys hjälper dig att samla in, korrelera och visualisera strukturerade och Ostrukturerade data.

För en praktisk översikt om hur du använder Azure SQL Analytics lösning och vanliga Användningsscenarier finns inbäddad video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Anslutna källor

Azure SQL Analytics lösningen använder inte agenter för att ansluta till Log Analytics-tjänsten.

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](log-analytics-windows-agent.md) | Nej | Direkt Windows-agenter som inte används av lösningen. |
| [Linux-agenter](log-analytics-linux-agents.md) | Nej | Direkt Linux-agenter som inte används av lösningen. |
| [SCOM-hanteringsgrupp](log-analytics-om-agents.md) | Nej | En direkt anslutning från SCOM-agent till logganalys används inte av lösningen. |
| [Azure Storage-konto](log-analytics-azure-storage.md) | Nej | Logganalys inte att läsa data från ett lagringskonto. |
| [Azure Diagnostics](log-analytics-azure-storage.md) | Ja | Azure mått och loggar data skickas till logganalys direkt av Azure. |

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon, kan du skapa en för [ledigt](https://azure.microsoft.com/free/).
- Logganalys-arbetsytan. Du kan använda en befintlig eller kan du [skapa en ny](log-analytics-quick-create-workspace.md) innan du börjar använda den här lösningen.
- Aktivera Azure-diagnostik för din Azure SQL-databaser och elastiska pooler och [konfigurera dem att skicka data till logganalys](../sql-database/sql-database-metrics-diag-logging.md).

## <a name="configuration"></a>Konfiguration

Utför följande steg för att lägga till Azure SQL Analytics lösningen till din arbetsyta.

1. Lägg till Azure SQL Analytics-lösning till arbetsytan från [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).
2. I Azure-portalen klickar du på **skapar du en resurs** > **övervakning + Management**.  
    ![Övervakning och hantering](./media/log-analytics-azure-sql/monitoring-management.png)
3. I den **övervakning + Management** lista Klicka **se alla**.
4. I den **rekommenderas** klickar du på **mer**, och sedan i den nya listan **Azure SQL Analytics (förhandsgranskning)** och markera den.  
    ![Azure SQL Analytics-lösning](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. I den **Azure SQL Analytics (förhandsgranskning)** området klickar du på **skapa**.  
    ![Skapa](./media/log-analytics-azure-sql/portal-create.png)
6. I den **Skapa ny lösning** området, Välj arbetsytan som du vill lägga till lösningen på och klicka sedan på **skapa**.  
    ![Lägg till arbetsytan](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Så här konfigurerar du Azure-prenumerationer

För att stödja flera prenumerationer, använder du PowerShell-skriptet från [aktivera Azure resource mått loggning med hjälp av PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Ange arbetsytan resurs-ID som en parameter när du kör skriptet för att skicka diagnostikdata från resurser i en Azure-prenumeration till en arbetsyta i en annan Azure-prenumeration.

**Exempel**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till lösningen till din arbetsyta Azure SQL Analytics panel har lagts till din arbetsyta och det visas i Översikt. Panelen visar antalet Azure SQL-databaser och elastiska pooler i Azure SQL som lösningen är ansluten till.

![Azure SQL Analytics sida vid sida](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Visa analysdata för Azure SQL

Klicka på den **Azure SQL Analytics** öppna Azure SQL Analytics-instrumentpanelen. Instrumentpanelen innehåller en översikt över alla databaser som övervakas via olika perspektiv. För olika perspektiv ska fungera måste du aktivera rätt mått eller loggarna på SQL-resurser strömmas till Azure logganalys-arbetsyta.

![Översikt över Azure SQL-Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Att välja någon av panelerna, öppnas en detaljerad rapport i specifika perspektiv. När perspektiv är markerad har nedåt rapporten öppnats.

![Azure SQL Analytics tidsgränser](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Varje perspektiv innehåller sammanfattningar för prenumerationen, server, elastisk pool och databasnivå. Dessutom kan varje perspektiv visas ett perspektiv för rapporten till höger. Att välja prenumeration, server, pool eller databasen i listan fortsätter nedrullningen.

| Perspektiv | Beskrivning |
| --- | --- |
| Resurs av typen | Perspektiv som räknar alla resurser som övervakas. Nedåt innehåller en sammanfattning av DTU och GB mått. |
| Insikter | Ger hierarkiska nedåt till Intelligent insikter. Läs mer om intelligent insikter. |
| Fel | Erbjuder hierarkiska nedåt i SQL-fel som inträffat på databaserna. |
| Tidsgränser | Erbjuder hierarkiska nedåt i SQL-tidsgränser som inträffat på databaserna. |
| Blockings | Erbjuder hierarkiska nedåt i SQL-blockings som inträffat på databaserna. |
| Databasen väntar | Erbjuder hierarkiska nedåt i SQL vänta statistik på databasnivå. Innehåller sammanfattningar av totala väntetiden och väntetiden per typ vänta. |
| frågan varaktighet | Erbjuder hierarkiska nedåt i frågan körning av statistik, till exempel fråga varaktighet, CPU-användning, Data-i/o-användning, logg-i/o-användning. |
| Timeout | Ger hierarkiska nedåt i frågan vänta statistik genom att vänta kategori. |

### <a name="intelligent-insights-report"></a>Intelligent insikter rapport

Azure SQL Database [Intelligent insikter](../sql-database/sql-database-intelligent-insights.md) gör att du vet vad som händer med databasens prestanda. Alla Intelligent insikter som samlas in kan visualiseras och nås via insikter perspektiv.

![Azure SQL Analytics insikter](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastiska poolen och databasen rapporter

Både elastiska pooler och databaser har sina egna specifika rapporter som visar alla data som samlas in för resursen i den angivna tiden.

![Analytics Azure SQL-databas](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Azure SQL Analytics elastisk Pool](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Frågan rapporter

Genom att fråga varaktighet och perspektiv för fråga väntar korrelera du prestanda för alla frågor via frågan rapporten. Den här rapporten jämför frågeprestandan mellan olika databaser och gör det enkelt att identifiera databaser som utför den valda frågan väl jämfört med de som tar lång tid.

![Azure SQL Analytics-frågor](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analysera data och skapa varningar

Du kan enkelt [skapa aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) med data från Azure SQL Database-resurser. Här följer några användbara [loggen Sök](log-analytics-log-searches.md) frågor som du kan använda med en avisering i loggen:



*Hög DTU på Azure SQL-databas*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Hög DTU på Azure SQL Database-elastisk Pool*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Nästa steg

- Använd [loggen sökningar](log-analytics-log-searches.md) i logganalys att visa detaljerad Azure SQL-data.
- [Skapa dina egna instrumentpaneler](log-analytics-dashboards.md) Azure SQL data visas.
- [Skapa aviseringar](log-analytics-alerts.md) när specifika Azure SQL-händelser inträffar.
