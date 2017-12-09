---
title: "Kom igång med Autoskala efter anpassade mått i Azure | Microsoft Docs"
description: "Lär dig mer om att skala din resurs med anpassade mått i Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.openlocfilehash: 72b6a68d0dbad4639f21aa701ec4865f36409f0a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Kom igång med Autoskala efter anpassade mått i Azure
Den här artikeln beskriver hur du skala resursen med ett anpassat mått i Azure-portalen.

Azure övervakaren Autoskala gäller enbart för Virtual Machine Scale uppsättningar (VMSS), cloud services, apptjänstplaner och apptjänstmiljöer. 

# <a name="lets-get-started"></a>Kan komma igång
Den här artikeln förutsätter att du har ett webbprogram med application insights konfigureras. Om du inte har någon redan, kan du [konfigurera Application Insights för ASP.NET-webbplats][1]

- Öppna [Azure-portalen][2]
- Klicka på ikonen för Azure i det vänstra navigeringsfönstret.
  ![Starta Azure-övervakning][3]
- Klicka på autoskalningsinställning som du vill visa alla resurser för vilka automatisk skalning är tillämpligt, tillsammans med dess aktuella status Autoskala ![identifiera Autoskala i Azure-monitor][4]
- Öppna 'Autoskala-bladet i Azure-Monitor och välj en resurs som du vill skala
> Obs: Stegen nedan använder en app service-plan som är associerade med ett webbprogram som har app insights konfigureras.
- Observera att den aktuella standardinstansantalet är 1 i bladet skala inställningen för resursen. Klicka på ”Aktivera Autoskala'.
  ![Skalinställningen för ny webbapp][5]
- Ange ett namn för inställningen skala och klicka på ”Lägg till en regel”. Lägg märke till regeln skalningsalternativ som öppnas som en kontext panel i den högra sidan. Som standard anges möjligheten att skala din instanser av 1 om CPU-percetage på resursen överskrider 70%. Ändra mått källan längst upp till ”Application Insights”, Välj app insights-resurs i listrutan 'resurs- och väljer sedan anpassade mått baserade på som du vill skala.
  ![Skala med anpassade mått][6]
- Liknar steg ovan, lägga till en regel för skalan som skala och minska antalet skala med 1 om anpassade mått är under ett tröskelvärde.
  ![Skala baserat på cpu][7]
- Ange den du instansen gränser. Till exempel om du vill skala mellan 2 och 5 instanser beroende på de anpassade mått variationerna in minsta till '2', högsta '5' och 'default' till ' 2'
> Obs: om det inte går att läsa resurs mått och den aktuella kapaciteten är lägre än standardkapaciteten sedan för att säkerställa tillgängligheten för resursen, Autoskala skalas ut till standardvärdet. Om den aktuella kapaciteten redan är högre än standardkapaciteten, skalar inte Autoskala i.
- Klicka på ”Save”

Grattis! Du kan nu nu har skapat din skala inställningen för Automatisk skala ditt webbprogram baserat på ett anpassat mått.

> Obs: Samma steg kan användas för att komma igång med en rolltjänst för VMSS eller molnet.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
