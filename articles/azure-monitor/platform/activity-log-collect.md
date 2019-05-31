---
title: Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsyta | Microsoft Docs
description: Samla in Azure-aktivitetsloggen i Azure Monitor-loggar och Använd lösningen för fjärrövervakning att analysera och söka i Azure-aktivitetsloggen i alla dina Azure-prenumerationer.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248136"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsyta i Azure Monitor
Den [Azure-aktivitetsloggen](activity-logs-overview.md) ger information om händelser på prenumerationsnivå som har inträffat i Azure-prenumerationen. Den här artikeln beskrivs hur du samlar in aktivitetsloggen i Log Analytics-arbetsytan och hur du använder Activity Log Analytics [övervakningslösning](../insights/solutions.md), vilket ger loggfrågor och vyer för att analysera data. 

Ansluta aktivitetsloggen till Log Analytics-arbetsytan ger följande fördelar:

- Konsolidera aktivitetsloggen från flera Azure-prenumerationer på en plats för analys.
- Store aktivitetsloggposter längre än 90 dagar.
- Korrelera aktivitetsloggen data med andra övervakningsdata som samlas in av Azure Monitor.
- Använd [logga frågor](../log-query/log-query-overview.md) att utföra avancerade analyser och få bättre insikt i aktivitetsloggen transaktioner.

## <a name="connect-to-log-analytics-workspace"></a>Anslut till Log Analytics-arbetsyta
En aktivitetslogg kan anslutas till endast en arbetsyta, men en enda arbetsyta kan anslutas till aktivitetsloggen för flera prenumerationer i samma Azure-klient. Samling över flera klienter, se [samla in Azure-aktivitetsloggar till Log Analytics-arbetsytan i alla prenumerationer i olika Azure Active Directory innehavare](activity-log-collect-tenants.md).

Använd följande procedur för att ansluta aktivitetsloggen till din Log Analytics-arbetsyta:

1. Från den **Log Analytics-arbetsytor** menyn i Azure-portalen, väljer arbetsytan för att samla in aktivitetsloggen.
1. I den **datakällor för arbetsyta** avsnitt i den arbetsytan väljer du **Azure-aktivitetsloggen**.
1. Klicka på den prenumeration som du vill ansluta till.

    ![Arbetsytor](media/activity-log-export/workspaces.png)

1. Klicka på **Connect** ansluta aktivitetsloggen i prenumerationen till den valda arbetsytan. Om prenumerationen är redan ansluten till en annan arbetsyta, klickar du på **Disconnect** först för att koppla från den.

    ![Ansluta arbetsytor](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analysera i Log Analytics-arbetsyta
När du ansluter en aktivitetslogg till Log Analytics-arbetsytan poster ska skrivas till arbetsytan i en tabell som heter **AzureActivity** som du kan hämta med en [loggfråga](../log-query/log-query-overview.md). Strukturen för den här tabellen varierar beroende på den [kategori av loggpost](activity-logs-overview.md#categories-in-the-activity-log). Se [Azure-aktivitetsloggen Händelseschema](activity-log-schema.md) en beskrivning av varje kategori.

## <a name="activity-logs-analytics-monitoring-solution"></a>Lösning för övervakning av aktivitet loggar Analytics
Azure Log Analytics-lösningen för fjärrövervakning ingår flera loggfrågor och vyer för att analysera poster aktivitetsloggen i Log Analytics-arbetsytan.

### <a name="install-the-solution"></a>Installera lösningen
Stegen nedan i [installera en övervakningslösning](../insights/solutions.md#install-a-monitoring-solution) att installera den **Activity Log Analytics** lösning. Det finns ingen ytterligare konfiguration krävs.

### <a name="use-the-solution"></a>Använd lösningen
Övervakningslösningar kan nås från den **övervakaren** menyn i Azure-portalen. Välj **mer** i den **Insights** avsnitt för att öppna den **översikt** sida med lösningen paneler. Den **Azure-aktivitetsloggar** panelen visar en uppräkning av antalet **AzureActivity** poster i din arbetsyta.

![Azure-aktivitetsloggar panel](media/collect-activity-logs/azure-activity-logs-tile.png)


Klicka på den **Azure-aktivitetsloggar** panelen för att öppna den **Azure-aktivitetsloggar** vy. Vyn består av visualiseringen delar i följande tabell. Varje del listar upp till 10 objekt som matchar den delar villkoren för det angivna tidsintervallet. Du kan köra en loggfråga som returnerar alla matchande poster genom att klicka på **se alla** längst ned på delen.

![Instrumentpanelen för Azure-aktivitetsloggar](media/collect-activity-logs/activity-log-dash.png)

| En del visualisering | Beskrivning |
| --- | --- |
| Azure-Aktivitetsloggposter | Visar ett stapeldiagram på den översta posten i Azure-aktivitetsloggen poster summor för datumintervall som du har valt och visar en lista över de översta 10 aktivitet anropare. Klicka på diagrammet för att köra en loggsökning för `AzureActivity`. Klicka på en anroparen objekt om du vill köra en loggsökning som returnerar alla poster i aktivitetsloggen för objektet. |
| Aktivitetsloggar efter Status | Visar ett ringdiagram för Azure-aktivitetsloggen status för det valda datumintervallet och en lista över de översta tio status-posterna. Klicka på diagrammet för att köra en loggfråga för `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Klicka på en statusobjekt om du vill köra en loggsökning som returnerar alla poster i aktivitetsloggen för status för posten. |
| Aktivitetsloggar efter resurs | Visar det totala antalet resurser med aktivitetsloggar och visar en lista över de översta tio resurser med post antalet för varje resurs. Klicka på den totala område för att köra en loggsökning för `AzureActivity | summarize AggregatedValue = count() by Resource`, som visar alla Azure-resurser tillgängliga för lösningen. Klicka på en resurs för att köra en loggfråga som returnerar alla aktivitetsposter för den resursen. |
| Aktivitetsloggar av Resursprovidern | Visar det totala antalet resursprovidrar som producerar aktivitetsloggar och visar en lista över tio främsta. Klicka på total område för att köra en loggfråga för `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, som visar alla leverantörer av Azure-resurs. Klicka på en resursprovider för att köra en loggfråga som returnerar alla aktivitetsposter för providern. |

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [aktivitetsloggen](activity-logs-overview.md).
- Läs mer om den [Azure Monitor dataplattform](data-platform.md).
- Använd [logga frågor](../log-query/log-query-overview.md) att visa detaljerad information från din aktivitetslogg.
