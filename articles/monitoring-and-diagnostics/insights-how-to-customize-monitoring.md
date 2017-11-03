---
title: "Översikt över mått i Microsoft Azure | Microsoft Docs"
description: "Lär dig hur du anpassar övervakning diagram i Azure."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c36031eb-4df5-4cd5-9479-311d493a40d2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: robb
ms.openlocfilehash: 3f9ebb0f5737714dd685f0dcc1ff4b1c0c89528f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Översikt över mått i Microsoft Azure
Alla Azure-tjänster spåra nyckelvärden så att du kan övervaka hälsotillstånd, prestanda, tillgänglighet och användning av dina tjänster. Du kan visa de här måtten i Azure portal och du kan också använda den [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) eller [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) åtkomst till en fullständig uppsättning mätvärden via programmering.

För vissa tjänster kan du behöva aktivera diagnostik för att kunna se några mått. Du får en grundläggande uppsättning mått för övriga, till exempel virtuella datorer, men måste aktivera hela angetts hög frekvens mått. Se [aktivera övervakning och diagnostik](insights-how-to-use-diagnostics.md) vill veta mer.

## <a name="using-monitoring-charts"></a>Med hjälp av övervakning diagram
Du kan skapa diagram över någon av mätvärdena som dem under en tidsperiod som du väljer.

1. I den [Azure Portal](https://portal.azure.com/), klickar du på **Bläddra**, och sedan en resurs du vill övervaka.
2. Den **övervakning** avsnittet innehåller de viktigaste mätvärdena för varje Azure-resurs. Ett webbprogram har till exempel **begäranden och fel**, där som en virtuell dator skulle ha **prosessorprocent** och **Disk läsning och skrivning**: ![övervakning lins](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Klicka på diagram visas de **mått** bladet. På bladet, förutom i diagrammet är en tabell som visar aggregeringar för mått (till exempel genomsnittlig lägsta och högsta tidsintervall du väljer). Nedan som är Varningsregler för resursen.
    ![Mått bladet](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Om du vill anpassa de rader som visas, klickar du på den **redigera** knappen i diagrammet eller, **redigera diagram** på bladet mått.
5. I bladet Redigera fråga kan du göra tre saker:
   
   * Ändra tidsintervallet
   * Växla mellan utseende stapel och linje
   * Välj olika metics ![Redigera fråga](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Ändra tidsintervallet är lika enkelt som att välja ett annat intervall (exempel **senaste timmen**) och klicka på **spara** längst ned på bladet. Du kan också välja **anpassad**, där du kan välja tid under de senaste 2 veckorna. Du kan till exempel se den hela två veckor, eller, från igår bara 1 timme. Ange i textrutan för att ange en annan timme.
    ![Anpassat tidsintervall](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Under tidsintervallet du kanal välja valfritt antal mått ska visas i diagrammet.
8. När du klickar på Spara kommer dina ändringar att sparas i just den resursen. Till exempel om du har två virtuella datorer och du ändrar ett diagram på en, påverkar inte andra.

## <a name="creating-side-by-side-charts"></a>Skapa sida-vid-sida-diagram
Du kan använda kraftfulla anpassningen i portalen för att lägga till så många diagram som du vill.

1. I den **...**  menyn längst upp på bladet Klicka **Lägg till paneler**:  
    ![Menyn Lägg till](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Sedan kan du kan välja väljer ett diagram från den **galleriet** på höger sida av skärmen: ![Gallery](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Om du inte ser måtten du vill, du kan alltid lägga till en av de förinställda mätvärdena och **redigera** diagrammet för att visa måtten som du behöver.

## <a name="monitoring-usage-quotas"></a>Övervakning av användning kvoter
De flesta mått visar trender över tid, men vissa data, till exempel användning kvoter finns i tidpunkt information med ett tröskelvärde.

Du kan också se användning kvoter på bladet för resurser som har kvoter:

![Användning](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Som med statistik och, du kan använda den [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) eller [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) åtkomst till en fullständig uppsättning användning kvoter programmässigt.

## <a name="next-steps"></a>Nästa steg
* [Få aviseringar](insights-receive-alert-notifications.md) när en måttet överskrider ett tröskelvärde.
* [Aktivera övervakning och diagnostik](insights-how-to-use-diagnostics.md) att samla in detaljerade hög frekvens mått på din tjänst.
* [Skala instansantalet automatiskt](insights-how-to-scale.md) att kontrollera att tjänsten är tillgänglig och svarstid.
* [Övervaka programprestanda](../application-insights/app-insights-azure-web-apps.md) om du vill förstå exakt hur din kod genomför i molnet.
* Använd [Programinsikter för JavaScript-appar och webbsidor](../application-insights/app-insights-web-track-usage.md) få klienten analytics om de webbläsare som besöker en webbsida.
* [Övervaka tillgänglighet och svarstider på en webbsida](../application-insights/app-insights-monitor-web-app-availability.md) med Application Insights så att du kan läsa mer om sidan inte är tillgänglig.

