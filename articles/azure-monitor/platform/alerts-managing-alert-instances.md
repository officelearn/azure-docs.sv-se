---
title: Hantera varningsinstanser i Azure Monitor
description: Hantera varningsinstanser i Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667626"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Hantera varningsinstanser med enhetliga aviseringar

Med [den enhetliga aviseringar erfarenhet](https://aka.ms/azure-alerts-overview) i Azure Monitor, kan du se alla dina olika typer av aviseringar i Azure. Detta sträcker sig över flera prenumerationer i en enda ruta. Den här artikeln visar hur du kan visa dina varningsinstanser och hur du hittar specifika varningsinstanser för felsökning.

> [!NOTE]
> Du kan bara komma åt aviseringar som genererats under de senaste 30 dagarna.

## <a name="go-to-the-alerts-page"></a>Gå till sidan aviseringar

Du kan gå till sidan aviseringar på något av följande sätt:

- Välj > **Övervakaraviseringar**i [Azure-portalen](https://portal.azure.com/). **Monitor**  

     ![Skärmbild av övervakarvarningar](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Använd kontexten för en viss resurs. Öppna en resurs, gå till avsnittet **Övervakning** och välj **Aviseringar**. Målsidan är förfiltrerade för aviseringar på den specifika resursen.

     ![Skärmbild av resursövervakningsaviseringar](media/alerts-managing-alert-instances/alert-resource.JPG)

- Använd kontexten för en viss resursgrupp. Öppna en resursgrupp, gå till avsnittet **Övervakning** och välj **Aviseringar**. Målsidan är förfiltrerade för aviseringar i den specifika resursgruppen.    

     ![Skärmbild av övervakningsaviseringar för resursgrupp](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Hitta varningsinstanser

På sidan Sammanfattning av **aviseringar** får du en översikt över alla dina aviseringsinstanser i Azure. Du kan ändra sammanfattningsvyn genom att välja **flera prenumerationer** (upp till högst 5) eller genom att filtrera mellan **resursgrupper,** specifika **resurser**eller **tidsintervall**. Välj **Totalt antal aviseringar**eller något av allvarlighetsgradbanden för att gå till listvyn för dina aviseringar.     

![Skärmbild av sammanfattningssidan för aviseringar](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
På sidan **Alla aviseringar** visas alla aviseringsinstanser över Azure. Om du kommer till portalen från ett aviseringsmeddelande kan du använda de filter som är tillgängliga för att begränsa in på den specifika aviseringsinstansen.

> [!NOTE]
> Om du kom till sidan genom att välja något av allvarlighetsgradsintervallen är listan förfiltrerade för den allvarlighetsgraden.

Förutom de filter som finns tillgängliga på föregående sida kan du också filtrera på grundval av övervakartjänst (till exempel plattform för mått), övervaka villkor (sparken eller löst), allvarlighetsgrad, varningstillstånd (ny/bekräftad/stängd) eller smart grupp-ID.

![Skärmbild av sidan Alla aviseringar](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Om du kom till sidan genom att välja något av allvarlighetsgradsintervallen är listan förfiltrerade för den allvarlighetsgraden.

Om du väljer en aviseringsinstans öppnas sidan **Aviseringsinformation,** så att du kan se mer information om den specifika varningsinstansen.   

![Skärmbild av sidan Varningsinformation](media/alerts-managing-alert-instances/alert-details.jpg)  

