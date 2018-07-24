---
title: Översikt över mått i Azure Monitor
description: Lär dig hur du anpassar Övervakningsdiagrammen i Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/06/2017
ms.author: robb
ms.component: metrics
ms.openlocfilehash: 44daf6461a062e75435ec6f70fbc3cf10327e799
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213052"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Översikt över mått i Microsoft Azure
Alla Azure-tjänster spårar nyckelvärden så att du kan övervaka hälsa, prestanda, tillgänglighet och användningen av dina tjänster. Du kan visa de här måtten i Azure-portalen och du kan också använda den [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) eller [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) att få åtkomst till den fullständiga uppsättningen mätvärden via programmering.

För vissa tjänster kan du behöva slå på diagnostik för att se alla mått. Du får en grundläggande uppsättning mått för andra, till exempel virtuella datorer, men behöver för att aktivera fullständiga ange hög frekvens mått. Se [aktivera övervakning och diagnostik](insights-how-to-use-diagnostics.md) vill veta mer.

## <a name="using-monitoring-charts"></a>Använd övervakningsdiagram
Du kan skapa diagram över någon av mått som dem över en tidsperiod som du väljer.

1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **Bläddra**, och sedan en resurs du är intresserad av övervakning.
2. Den **övervakning** avsnittet innehåller de viktigaste mått för varje Azure-resurs. Till exempel en webbapp har **förfrågningar och fel**, där som en virtuell dator skulle ha **CPU-procent** och **Disk läsning och skrivning**: ![övervakning lins](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. När du klickar på ett diagram visar den **mått** bladet. På bladet, förutom i diagrammet är en tabell som visar sammanställning av mått (t.ex average, lägsta och högsta tidsintervall som du har valt). Nedan som är Varningsregler för resursen.
    ![Bladet mått](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Om du vill anpassa de rader som visas, klickar du på den **redigera** knappen i diagrammet, eller, **redigera diagram** på bladet mått.
5. På bladet Redigera fråga kan du göra tre saker:
   
   * Ändra tidsintervallet
   * Växla mellan utseende stapel och linje
   * Välj olika metics ![Redigera fråga](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Ändra tidsintervallet är det lika enkelt som att välja ett annat intervall (till exempel **senaste timmen**) och klicka på **spara** längst ned på bladet. Du kan också välja **anpassade**, vilket gör att du kan välja varje tidsperiod under de senaste 2 veckorna. Du kan till exempel se hela två veckor, eller, bara en timme från igår. Ange i textrutan för att ange en annan timme.
    ![Anpassat tidsintervall](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Under tidsintervallet väljer du ändring valfritt antal mått för att visa i diagrammet.
8. När du klickar på Spara sparas dina ändringar för just den resursen. Till exempel om du har två virtuella datorer och du ändrar ett diagram på någon, påverkar inte den andra.

## <a name="creating-side-by-side-charts"></a>Skapa sida-vid-sida-diagram
Du kan använda kraftfulla anpassningen i portalen för att lägga till så många diagram som du vill ha.

1. I den **...**  menyn överst på bladet klickar du på **lägga till paneler**:  
    ![Menyn Lägg till](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Du kan markera ett diagram från den **galleriet** på höger sida av skärmen: ![galleriet](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Om du inte ser måtten du vill kan du kan alltid lägga till något av de förinställda mått och **redigera** diagrammet för att visa måtten som du behöver.

## <a name="monitoring-usage-quotas"></a>Övervaka användningskvoter
De flesta mått visar trender över tid, men vissa data, till exempel användningskvoter, är point-in-time-information med ett tröskelvärde.

Du kan också se användningskvoter på bladet för resurser som har kvoter:

![Användning](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Som med mått, kan du använda den [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) eller [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) att få åtkomst till den fullständiga uppsättningen användningskvoter programmässigt.

## <a name="next-steps"></a>Nästa steg
* [Få varningsmeddelanden](insights-receive-alert-notifications.md) när ett mått överskrider ett tröskelvärde.
* [Aktivera övervakning och diagnostik](insights-how-to-use-diagnostics.md) att samla in detaljerade mätvärden för hög frekvens på din tjänst.
* [Skala instansantalet automatiskt](insights-how-to-scale.md) att kontrollera att tjänsten är tillgänglig och svarar.
* [Övervaka programprestanda](../application-insights/app-insights-azure-web-apps.md) om du vill förstå exakt hur din kod fungerar i molnet.
* Använd [Application Insights för JavaScript-appar och webbsidor](../application-insights/app-insights-web-track-usage.md) att hämta klienten analyser om webbläsare som besöker en webbsida.
* [Övervaka tillgänglighet och svarstider på en webbsida](../application-insights/app-insights-monitor-web-app-availability.md) med Application Insights så att du kan läsa mer om din sida är nere.

