---
title: Samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta | Microsoft Docs
description: Samla in Azure aktivitets logg i Azure Monitor loggar och Använd övervaknings lösningen för att analysera och söka i Azure aktivitets loggen i alla dina Azure-prenumerationer.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: f4612232acfe6099c56d365e482cbc82c8436dee
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745624"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta i Azure Monitor

> [!WARNING]
> Du kan nu samla in aktivitets loggen på en Log Analytics arbets yta med en diagnostisk inställning som liknar hur du samlar in resurs loggar. Se [samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta i Azure Monitor](diagnostic-settings-subscription.md).

[Azure aktivitets loggen](platform-logs-overview.md) ger inblick i händelser på prenumerations nivå som har inträffat i din Azure-prenumeration. Den här artikeln beskriver hur du samlar in aktivitets loggen på en Log Analytics arbets yta och hur du använder Aktivitetslogganalys [övervaknings lösning](../insights/solutions.md)som innehåller logg frågor och vyer för att analysera dessa data. 

Att ansluta aktivitets loggen till en Log Analytics arbets yta ger följande fördelar:

- Konsolidera aktivitets loggen från flera Azure-prenumerationer till en plats för analys.
- Lagra aktivitets logg poster under längre tid än 90 dagar.
- Korrelera aktivitets logg data med andra övervaknings data som samlas in av Azure Monitor.
- Använd [logg frågor](../log-query/log-query-overview.md) för att utföra komplexa analyser och få djupgående insikter om aktivitets logg poster.

## <a name="connect-to-log-analytics-workspace"></a>Anslut till Log Analytics arbets yta
En enskild arbets yta kan anslutas till aktivitets loggen för flera prenumerationer i samma Azure-klient. För insamling över flera klienter, se [samla in Azure-aktivitets loggar i en Log Analytics arbets yta mellan prenumerationer i olika Azure Active Directory klienter](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Du kan få ett fel med följande procedur om resurs leverantörerna Microsoft. OperationalInsights och Microsoft. OperationsManagement inte har registrerats för din prenumeration. Se [Azures resurs leverantörer och typer](../../azure-resource-manager/resource-manager-supported-services.md) för att registrera dessa leverantörer.

Använd följande procedur för att ansluta aktivitets loggen till din Log Analytics-arbetsyta:

1. Från menyn **Log Analytics arbets ytor** i Azure Portal väljer du arbets ytan för att samla in aktivitets loggen.
1. I avsnittet **data källor för arbets yta** på menyn för arbets ytan väljer du **Azure aktivitets logg**.
1. Klicka på den prenumeration som du vill ansluta.

    ![Arbetsytor](media/activity-log-export/workspaces.png)

1. Klicka på **Anslut** för att ansluta aktivitets loggen i prenumerationen till den valda arbets ytan. Om prenumerationen redan är ansluten till en annan arbets yta, klickar du på **Koppla från** först för att koppla bort den.

    ![Anslut arbets ytor](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analysera i Log Analytics arbets ytan
När du ansluter en aktivitets logg till en Log Analytics arbets yta skrivs poster till arbets ytan i en tabell med namnet **AzureActivity** som du kan hämta med en [logg fråga](../log-query/log-query-overview.md). Strukturen för den här tabellen varierar beroende på vilken [kategori logg posten](activity-log-view.md#categories-in-the-activity-log)har. Se [händelse schema för Azure aktivitets logg](activity-log-schema.md) för en beskrivning av varje kategori.

## <a name="activity-logs-analytics-monitoring-solution"></a>Aktivitets loggarna analys övervaknings lösning
Azure Log Analytics Monitoring-lösningen innehåller flera logg frågor och vyer för att analysera aktivitets logg poster i Log Analytics-arbetsytan.

### <a name="install-the-solution"></a>Installera lösningen
Använd proceduren i [installera en övervaknings lösning](../insights/solutions.md#install-a-monitoring-solution) för att installera **Aktivitetslogganalys** lösning. Ingen ytterligare konfiguration krävs.

### <a name="use-the-solution"></a>Använd lösningen
Övervaknings lösningar nås från **Monitor** -menyn i Azure Portal. Välj **mer** i avsnittet om **insikter** för att öppna **översikts** sidan med lösnings panelerna. I panelen **Azure aktivitets loggar** visas antalet **AzureActivity** -poster i din arbets yta.

![Azure-aktivitetsloggar panel](media/collect-activity-logs/azure-activity-logs-tile.png)


Klicka på panelen **Azure aktivitets loggar** för att öppna vyn **Azure aktivitets loggar** . Vyn innehåller visualiserings delarna i följande tabell. Varje del visar upp till 10 objekt som matchar delarnas kriterier för det angivna tidsintervallet. Du kan köra en logg fråga som returnerar alla matchande poster genom att klicka på **Visa alla** längst ned i delen.

![Instrumentpanelen för Azure-aktivitetsloggar](media/collect-activity-logs/activity-log-dash.png)

| Visualiserings del | Beskrivning |
| --- | --- |
| Azure-Aktivitetsloggposter | Visar ett stapeldiagram för de översta posterna i Azures aktivitets logg post för det datum intervall som du har valt och visar en lista över de 10 främsta aktivitetens anropare. Klicka på diagrammet för att köra en loggsökning för `AzureActivity`. Klicka på ett anroparens objekt för att köra en loggs ökning som returnerar alla aktivitets logg poster för objektet. |
| Aktivitetsloggar efter Status | Visar ett ring diagram för Azures aktivitets logg status för det valda datum intervallet och en lista över de tio främsta status posterna. Klicka på diagrammet för att köra en logg fråga för `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Klicka på ett status objekt om du vill köra en loggs ökning som returnerar alla aktivitets logg poster för den status posten. |
| Aktivitetsloggar efter resurs | Visar det totala antalet resurser med aktivitets loggar och listar de tio främsta resurserna med antal poster för varje resurs. Klicka på den totala område för att köra en loggsökning för `AzureActivity | summarize AggregatedValue = count() by Resource`, som visar alla Azure-resurser tillgängliga för lösningen. Klicka på en resurs för att köra en logg fråga som returnerar alla aktivitets poster för resursen. |
| Aktivitetsloggar av Resursprovidern | Visar det totala antalet resurs leverantörer som skapar aktivitets loggar och listar de tio främsta. Klicka på det totala utrymmet för att köra en logg fråga för `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, som visar alla Azure Resource providers. Klicka på en resurs leverantör för att köra en logg fråga som returnerar alla aktivitets poster för providern. |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [aktivitets loggen](platform-logs-overview.md).
- Läs mer om [Azure Monitor data plattform](data-platform.md).
- Använd [logg frågor](../log-query/log-query-overview.md) för att visa detaljerad information från aktivitets loggen.
