---
title: Automatisk skalning i Azure med ett anpassat mått
description: Lär dig hur du skalar din resurs efter anpassat mått i Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7758c440c75af5819099110dcbdaf5a86a1d2a04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425127"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Komma igång med automatisk skalning efter anpassat mått i Azure
I den här artikeln beskrivs hur du skalar din resurs efter ett anpassat mått i Azure-portalen.

Automatisk skalning av Azure Monitor gäller endast för [skalningsuppsättningar för virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/)datorer, [Molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), Azure Data Explorer [Cluster](https://azure.microsoft.com/services/data-explorer/) ,   
Integration Service Miljö och [API Management tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Låt oss komma igång
Den här artikeln förutsätter att du har en webbapp med programinsikter konfigurerade. Om du inte redan har någon kan du [konfigurera Application Insights för din ASP.NET webbplats][1]

- Öppna [Azure-portalen][2]
- Klicka på Azure Monitor-ikonen i det vänstra navigeringsfönstret.
  ![Starta Azure Monitor][3]
- Klicka på inställningen Automatisk skalning för att visa alla resurser som automatisk ![skalning är tillämpligt för, tillsammans med dess aktuella automatisk skalningsstatus Upptäck automatisk skalning i Azure-övervakaren][4]
- Öppna bladet Automatisk skalning i Azure Monitor och välj en resurs som du vill skala
  > Stegen nedan använder en apptjänstplan som är associerad med en webbapp som har appinsikter konfigurerade.
- Observera att det aktuella antalet instanser är 1 i skalinställningsbladet för resursen. Klicka på "Aktivera automatisk skalning".
  ![Skalningsinställning för ny webbapp][5]
- Ange ett namn för skalningsinställningen och klicka på "Lägg till en regel". Lägg märke till skalningsregelalternativen som öppnas som ett sammanhangsfönster på höger sida. Som standard anges alternativet att skala antalet instanser med 1 om processorprocenten för resursen överstiger 70 %. Ändra måttkällan högst upp till "Application Insights", välj appstatistikresursen i listrutan Resurs och välj sedan det anpassade mått baserat på vilket du vill skala.
  ![Skala efter anpassat mått][6]
- I likhet med steget ovan lägger du till en skalningsregel som skalar in och minskar skalningsantalet med 1 om det anpassade måttet ligger under ett tröskelvärde.
  ![Skala baserad på cpu][7]
- Ange instansgränser. Om du till exempel vill skala mellan 2–5 instanser beroende på de anpassade måttfluktuationerna anger du "minimum" till "2", "maximum" till '5' och 'default' till '2'
  > Om det finns ett problem med att läsa resursmåtten och den aktuella kapaciteten ligger under standardkapaciteten och sedan för att säkerställa resursens tillgänglighet skalas automatisk skalning ut till standardvärdet. Om den aktuella kapaciteten redan är högre än standardkapaciteten skalas inte automatisk skalning in.
- Klicka på "Spara"

Grattis! Du har nu skapat skalningsinställningen för att automatiskt skala din webbapp baserat på ett anpassat mått.

> Samma steg gäller för att komma igång med en VMSS- eller molntjänstroll.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

