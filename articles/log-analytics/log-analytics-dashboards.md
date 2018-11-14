---
title: Skapa en anpassad instrumentpanel i Azure Log Analytics | Microsoft Docs
description: Den här guiden hjälper dig att förstå hur instrumentpanelerna i Log Analytics kan visualisera alla dina sparade loggsökningar, vilket ger dig en enskild lins och visa din miljö.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 8afefa95e0a7b4f10f4a2bf6c7f099ba9eb4a957
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613227"
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Skapa en anpassad instrumentpanel för användning i Log Analytics

Den här guiden hjälper dig att förstå hur instrumentpanelerna i Log Analytics kan visualisera alla dina sparade loggsökningar, vilket ger dig en enskild lins och visa din miljö.

>[!NOTE]
> Du kan inte längre redigera din befintliga **min instrumentpanel**. Den här funktionen håller blir inaktuella.

![Instrumentpanelen för exemplet](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Alla anpassade instrumentpaneler som du skapar i OMS-portalen är också tillgängliga i OMS Mobile-appen. Finns på följande sidor för mer information om appar.

* [OMS-mobilappen från Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [OMS-mobilappen från Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![mobila instrumentpanel](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Hur skapar jag min instrumentpanel?
Börja genom att gå till sidan Översikt över OMS. Du ser den **min instrumentpanel** panelen till vänster. Klicka på den för att granska nedåt i din instrumentpanel.

![Översikt](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Lägga till en panel
I instrumentpaneler drivs paneler av dina sparade loggsökningar. OMS levereras med många färdiga sparade loggsökningar, så att du kan börja direkt. Använd följande steg som beskriver hur du börjar.

I vyn instrumentpanel för Mina Klicka bara på **anpassa** ange anpassningsläge.

![Illustrationer](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 På panelen som öppnas till höger på sidan visas alla sparade loggsökningar i din arbetsyta. Om du vill visualisera en loggsökning som du har sparat som en panel, hovra över en sparad sökning och klicka sedan på den **plus** symbolen.

![Lägg till paneler 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

När du klickar på den **plus** symbolen, en ny panel visas i vyn instrumentpanel för min.

![Lägg till paneler 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Redigera en panel
I vyn instrumentpanel för Mina Klicka bara på **anpassa** ange anpassningsläge. Klicka på panelen som du vill redigera. De högra panelen ändringarna att redigera, och ger en uppsättning alternativ:

![Redigera panel](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Redigera panel](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Brickvisualiseringar
Det finns tre typer av brickvisualiseringar att välja mellan:

| diagramtyp | Den gör |
| --- | --- |
| ![Liggande stapeldiagram](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Visar en tidslinje för sökningen sparad logg resultatet som ett stapeldiagram eller en lista med resultat efter ett fält beroende på om din loggsökning aggregerar resultaten efter ett fält eller inte. |
| ![mått](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Visar de totala log search resultatet träffar som ett tal i en panel. Metrisk paneler kan du ange ett tröskelvärde som belyser panelen när tröskelvärdet har uppnåtts. |
| ![Rad](./media/log-analytics-dashboards/oms-dashboards-line.png) |Visar en tidslinje för sparade log search resultatet träffar med värden som ett linjediagram. |

### <a name="threshold"></a>Tröskelvärde
Du kan skapa ett tröskelvärde på en panel med hjälp av mätvärden visualiseringen. Välj på för att skapa ett tröskelvärde på panelen. Välj om du vill markera panelen när värdet är över eller under det valda tröskelvärdet och ange sedan tröskelvärdet nedan.

## <a name="organizing-the-dashboard"></a>Ordna instrumentpanelen
Gå till vyn min instrumentpanel för att organisera din instrumentpanel, och klicka på **anpassa** ange anpassningsläge. Klicka och dra den panel som du vill flytta och flytta den dit du vill att din panel ska vara.

![Ordna din instrumentpanel](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Ta bort en panel
Om du vill ta bort en panel, gå till vyn min instrumentpanel och klicka på **anpassa** ange anpassningsläge. Välj panelen du vill ta bort och klicka sedan på den högra rutan väljer **ta bort panelen**.

![Ta bort en panel](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Nästa steg
* Skapa [aviseringar](../monitoring-and-diagnostics/monitoring-overview-alerts.md) i Log Analytics för att generera meddelanden och åtgärda problem.
