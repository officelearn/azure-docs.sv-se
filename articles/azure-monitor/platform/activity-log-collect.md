---
title: Samla in Azure-aktivitetslogg i Log Analytics-arbetsytan
description: Samla in Azure Activity Log i Azure Monitor Logs och använd övervakningslösningen för att analysera och söka i Azure-aktivitetsloggen i alla dina Azure-prenumerationer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055313"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsytan i Azure Monitor

> [!WARNING]
> Du kan nu samla in aktivitetsloggen till en Log Analytics-arbetsyta med hjälp av en diagnostikinställning som liknar hur du samlar in resursloggar. Se [Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsytan i Azure Monitor](diagnostic-settings-legacy.md).

[Azure-aktivitetsloggen](platform-logs-overview.md) ger insikt i händelser på prenumerationsnivå som har inträffat i din Azure-prenumeration. I den här artikeln beskrivs hur du samlar in aktivitetsloggen på en log analytics-arbetsyta och hur du använder [övervakningslösningen](../insights/solutions.md)Aktivitetslogganalys , som tillhandahåller loggfrågor och vyer för att analysera dessa data. 

Att ansluta aktivitetsloggen till en log analytics-arbetsyta ger följande fördelar:

- Konsolidera aktivitetsloggen från flera Azure-prenumerationer till en plats för analys.
- Lagra aktivitetsloggposter längre än 90 dagar.
- Korrelera aktivitetsloggdata med andra övervakningsdata som samlas in av Azure Monitor.
- Använd [loggfrågor](../log-query/log-query-overview.md) för att utföra komplexa analyser och få djupa insikter om aktivitetsloggposter.

## <a name="connect-to-log-analytics-workspace"></a>Ansluta till log analytics-arbetsyta
En enda arbetsyta kan anslutas till aktivitetsloggen för flera prenumerationer i samma Azure-klientorganisation. Insamling över flera klienter finns i [Samla in Azure-aktivitetsloggar i en Log Analytics-arbetsyta över prenumerationer i olika Azure Active Directory-klienter](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Ett felmeddelande kan visas med följande procedur om resursleverantörerna Microsoft.OperationalInsights och Microsoft.OperationsManagement inte är registrerade för din prenumeration. Se [Azure-resursleverantörer och -typer](../../azure-resource-manager/management/resource-providers-and-types.md) för att registrera dessa leverantörer.

Använd följande procedur för att ansluta aktivitetsloggen till logganalysarbetsytan:

1. På menyn **Logganalysarbetsytor** i Azure-portalen väljer du arbetsytan för att samla in aktivitetsloggen.
1. I avsnittet **Datakällor på arbetsytan** på arbetsytans meny väljer du **Azure Activity log**.
1. Klicka på den prenumeration som du vill ansluta.

    ![Arbetsytor](media/activity-log-export/workspaces.png)

1. Klicka på **Anslut** om du vill ansluta aktivitetsloggen i prenumerationen till den valda arbetsytan. Om prenumerationen redan är ansluten till en annan arbetsyta klickar du på **Koppla från** först för att koppla från den.

    ![Ansluta arbetsytor](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analysera i Log Analytics-arbetsyta
När du ansluter en aktivitetslogg till en Log Analytics-arbetsyta skrivs poster till arbetsytan till en tabell som heter **AzureActivity** som du kan hämta med en [loggfråga](../log-query/log-query-overview.md). Strukturen för den här tabellen varierar beroende på [kategorin loggpost](activity-log-view.md#categories-in-the-activity-log). Se [Azure Activity Log händelseschema](activity-log-schema.md) för en beskrivning av varje kategori.

## <a name="activity-logs-analytics-monitoring-solution"></a>Övervakningslösning för Aktivitetsloggar Analytics
Övervakningslösningen för Azure Log Analytics innehåller flera loggfrågor och vyer för att analysera aktivitetsloggposterna på din Log Analytics-arbetsyta.

### <a name="install-the-solution"></a>Installera lösningen
Använd proceduren i [Installera en övervakningslösning](../insights/solutions.md#install-a-monitoring-solution) för att installera **lösningen för Aktivitetslogganalys.** Det krävs ingen ytterligare konfiguration.

### <a name="use-the-solution"></a>Använd lösningen
Övervakningslösningar nås från **Monitor-menyn** i Azure-portalen. Välj **Mer** i avsnittet **Insikter** om du vill öppna sidan **Översikt** med lösningspanelerna. Panelen **Azure Activity Logs** visar antalet **AzureActivity-poster** på arbetsytan.

![Panel för Azure-aktivitetsloggar](media/collect-activity-logs/azure-activity-logs-tile.png)


Klicka på panelen **Azure-aktivitetsloggar** för att öppna vyn **Azure-aktivitetsloggar.** Vyn innehåller visualiseringsdelarna i följande tabell. Varje del listar upp till 10 objekt som matchar de delarnas villkor för det angivna tidsintervallet. Du kan köra en loggfråga som returnerar alla matchande poster genom att klicka på **Visa alla** längst ned i delen.

![Instrumentpanelen Azure Activity Logs](media/collect-activity-logs/activity-log-dash.png)

| Visualiseringsdel | Beskrivning |
| --- | --- |
| Azure-aktivitetsloggposter | Visar ett stapeldiagram över de översta summorna för Azure Activity Log-postsumman för det datumintervall som du har valt och visar en lista över de 10 främsta aktivitetssamtalen. Klicka på stapeldiagrammet om `AzureActivity`du vill köra en loggsökning efter . Klicka på ett uppringarobjekt om du vill köra en loggsökning som returnerar alla aktivitetsloggtransaktioner för objektet. |
| Aktivitetsloggar efter status | Visar ett ringdiagram för Azure Activity Log-status för det valda datumintervallet och en lista över de tio bästa statusposterna. Klicka på diagrammet om du `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`vill köra en loggfråga för . Klicka på ett statusobjekt om du vill köra en loggsökning som returnerar alla aktivitetsloggtransaktioner för den statusposten. |
| Aktivitetsloggar efter resurs | Visar det totala antalet resurser med aktivitetsloggar och listar de tio största resurserna med postantal för varje resurs. Klicka på det totala området `AzureActivity | summarize AggregatedValue = count() by Resource`för att köra en loggsökning för , som visar alla Azure-resurser som är tillgängliga för lösningen. Klicka på en resurs om du vill köra en loggfråga som returnerar alla aktivitetsposter för den resursen. |
| Aktivitetsloggar efter resursprovider | Visar det totala antalet resursleverantörer som producerar aktivitetsloggar och listar de tio bästa. Klicka på det totala området `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`för att köra en loggfråga för , som visar alla Azure-resursleverantörer. Klicka på en resursprovider om du vill köra en loggfråga som returnerar alla aktivitetsposter för providern. |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [aktivitetsloggen](platform-logs-overview.md).
- Läs mer om [Azure Monitor-dataplattformen](data-platform.md).
- Använd [loggfrågor](../log-query/log-query-overview.md) för att visa detaljerad information från aktivitetsloggen.
