---
title: Automatisk skalning i Azure med ett anpassat mått
description: Lär dig mer om att skala din resurs med anpassat mått i Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 97836c4160349b8095ba2095176783ae17b46e82
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216207"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Komma igång med automatisk skalning med anpassat mått i Azure
Den här artikeln beskriver hur du skalar din resurs genom ett anpassat mått i Azure-portalen.

Azure Monitor-Autoskala gäller enbart för Virtual Machine Scale Sets (VMSS), molntjänster, app service-planer och app service-miljöer. 

# <a name="lets-get-started"></a>Kan komma igång
Den här artikeln förutsätter att du har en webbapp med application insights har konfigurerats. Om du inte har ett redan, kan du [ställa in Application Insights för ASP.NET-webbplats][1]

- Öppna [Azure-portalen][2]
- Klicka på Azure Monitor-ikonen i det vänstra navigeringsfönstret.
  ![Starta Azure Monitor][3]
- Klicka på inställningen för automatisk skalning att visa alla resurser för vilka automatisk skalning är tillämpligt, tillsammans med dess aktuella status för autoskalning ![identifiera automatisk skalning i Azure monitor][4]
- Öppna ”Autoskala'-bladet i Azure Monitor och välj en resurs som du vill skala
> Obs! Stegen nedan använder en app service-plan som är associerade med en webbapp som har konfigurerats appinsikter.
- Observera att det aktuella instansantalet är 1 i bladet skala inställningen för resursen. Klicka på Aktivera automatisk skalning.
  ![Skalinställningen för ny webbapp][5]
- Ange ett namn för skalinställningen och klicka på ”Lägg till en regel”. Observera regeln skalningsalternativ som öppnas som en kontext-rutan på höger sida. Som standard anger alternativ för skalning din instansantalet med 1 om CPU-percetage på resursen överskrider 70%. Ändra måttkälla längst upp till ”Application Insights”, Välj app insights-resurs i listrutan ”resurs” och välj sedan den anpassade mått baserat på vilket du vill skala.
  ![Skala med anpassat mått][6]
- Ett liknande sätt steget ovan, lägga till en skala-regel som ska skalas i och minska skalningsantalet med 1 om anpassat mått som ligger under ett tröskelvärde.
  ![Skala baserat på cpu][7]
- Ange du instansgränser. Till exempel om du vill skala mellan 2 – 5 instanser beroende på de anpassade mått variationerna, ange lägsta till '2', maximalt '5' och 'default' till ' 2'
> Obs: om det finns ett problem med att läsa mätvärden för resurs och den aktuella kapaciteten är under standardkapacitet, sedan för att säkerställa tillgängligheten för resursen, automatisk skalning skalas ut till standardvärdet. Om den aktuella kapaciteten är högre än standardkapacitet, kommer autoskalning inte att skala in.
- Klicka på ”Spara”

Grattis! Du nu har skapat din skalinställning för att automatiskt skala din webbapp baserat på ett anpassat mått.

> Obs: Likadant gäller att komma igång med en VMSS eller cloud service-roll.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
