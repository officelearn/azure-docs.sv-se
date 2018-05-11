---
title: Skapa en anpassad instrumentpanel i Azure Log Analytics | Microsoft Docs
description: Den här guiden hjälper dig att förstå hur logganalys instrumentpaneler kan visualisera alla dina sparad logg-sökningar, vilket ger dig en enda lins att visa din miljö.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7eaa74655b009e3154e2cc4f09d5599040ec4589
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Skapa en anpassad instrumentpanel för användning i logganalys

Den här guiden hjälper dig att förstå hur logganalys instrumentpaneler kan visualisera alla dina sparad logg-sökningar, vilket ger dig en enda lins att visa din miljö.

>[!NOTE]
> Du kan inte längre redigera din befintliga **min instrumentpanel**. Den här funktionen håller inaktuell.

![Exempel instrumentpanelen](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Alla anpassade instrumentpaneler som du skapar i OMS-portalen är också tillgängliga i OMS-Mobilapp. Finns på följande sidor för mer information om apparna.

* [OMS mobilappar från Microsoft-Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [OMS mobila appar från Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![mobila instrumentpanelen](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Hur skapar jag min instrumentpanel
Börja, gå till sidan Översikt över OMS. Du ser den **min instrumentpanel** panelen till vänster. Klicka på den detaljnivån i instrumentpanelen.

![Översikt](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Lägga till en panel
I instrumentpaneler drivs paneler av sparad logg-sökningar. OMS levereras med många färdiga sparad logg sökningar, så att du kan börja direkt. Använd följande steg som beskriver hur du börjar.

I vyn instrumentpanel för Mina Klicka bara på **anpassa** ange anpassningsläge.

![Illustrationer](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 Panelen som öppnas till höger på sidan visas alla ditt arbetsområde sparad logg sökningar. Om du vill visualisera en sparad logg sökning som en panel hovra över en sparad sökning och klicka sedan på den **plus** symbolen.

![Lägg till paneler 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

När du klickar på den **plus** symboler, en ny panel visas i den här instrumentpanelsvyn.

![Lägg till paneler 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Redigera en panel
I vyn instrumentpanel för Mina Klicka bara på **anpassa** ange anpassningsläge. Klicka på panelen som du vill redigera. De högra panelen ändringarna att redigera, och ger ett antal alternativ:

![Redigera sida vid sida](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Redigera sida vid sida](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Panelen visualiseringar
Det finns tre typer av panelen visualiseringar kan välja mellan:

| diagramtyp | syfte |
| --- | --- |
| ![Liggande stapeldiagram](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Visar en tidslinje för din sparad logg sökresultat som ett stapeldiagram eller en lista med resultat av ett fält beroende på om sökningen loggen aggregerar resultaten efter ett fält eller inte. |
| ![mått](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Visar träffar din totala Sök resultatet som ett tal i en panel. Mått paneler kan du ange ett tröskelvärde som ska markeras panelen när tröskelvärdet har uppnåtts. |
| ![raden](./media/log-analytics-dashboards/oms-dashboards-line.png) |Visar en tidslinje sparad logg Sök resultatet träffar med värden som ett linjediagram. |

### <a name="threshold"></a>Tröskelvärde
Du kan skapa ett tröskelvärde på en panel med mått visualiseringen. Välj för att skapa ett tröskelvärde på panelen. Välj om du vill markera panelen när värdet ligger över eller under det valda tröskelvärdet och ange sedan tröskelvärdet nedan.

## <a name="organizing-the-dashboard"></a>Ordna instrumentpanelen
För att organisera instrumentpanelen, navigera till den här instrumentpanelsvyn och klickar på **anpassa** ange anpassningsläge. Klicka och dra panelen du vill flytta och placera den där du vill att din panel ska vara.

![Ordna din instrumentpanel](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Ta bort en panel
Om du vill ta bort en panel, navigera till den här instrumentpanelsvyn och klickar på **anpassa** ange anpassningsläge. Välj panelen du vill ta bort och klicka sedan på den högra panelen väljer **ta bort panelen**.

![Ta bort en panel](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Nästa steg
* Skapa [aviseringar](log-analytics-alerts.md) i logganalys att generera meddelanden och åtgärda problem.
