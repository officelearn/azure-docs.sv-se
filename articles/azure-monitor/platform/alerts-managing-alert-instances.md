---
title: Hantera aviseringsinstanser
description: Hantera aviseringsinstanser i Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: cb93f38c05156d7ab5acb89ffff810949583e507
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089411"
---
# <a name="manage-alert-instances"></a>Hantera aviseringsinstanser
Med den [unified aviseringsgränssnittet](https://aka.ms/azure-alerts-overview) i Azure Monitor kan du nu se alla olika typer av aviseringar i Azure, med flera prenumerationer i en enda glasruta. Den här artikeln vägleder dig genom hur du kan visa din aviseringsinstanser och hur du djupdykning i portalen för att hitta specifika aviseringsinstanser för felsökning.

1. Det finns tre sätt att hamna på sidan aviseringar

   + I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet Monitor - **aviseringar**.  
     ![Övervakning](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Du kan navigera till aviseringar från inom ramen för en specifik **resource**. När en resurs har öppnat och gå via dess innehållsförteckningen till avsnittet övervakning och välj **aviseringar**, med landningssidan förväg som filtreras för aviseringar i den specifika resursen.
   
     ![Övervakning](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + Du kan navigera till aviseringar från inom ramen för en specifik **resursgrupp**. När en resursgrupp öppnas, navigera genom dess innehållsförteckningen till avsnittet övervakning och väljer **aviseringar**, med landningssidan förväg som filtreras för aviseringar i den specifika resursgruppen.    
   
     ![Övervakning](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Du hamnar på den **aviseringar sammanfattning** sida, vilket ger dig en översikt över alla dina aviseringar instanser i Azure. Du kan ändra sammanfattningsvyn genom att välja **flera prenumerationer** (högst 5) eller genom att filtrera över **resursgrupper**, specifika **resurser**, eller **tid intervall**. Klicka på antingen Totalt antal aviseringar eller något av de allvarlighetsgrad band att gå till listvy för dina aviseringar.     
   ![Sammanfattning av aviseringar](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Du hamnar på den **alla aviseringar** sidan där du kan se alla aviseringar instanser i Azure som är listat. Om du kommer till portalen från en avisering, kan du använda filter som är tillgängliga för att begränsa i på den specifika aviseringsinstansen. (**Obs**: Om du Kom till sidan genom att klicka på något av band allvarlighetsgrad i listan ska förväg filtrerad för den allvarlighetsgraden när hamnar du). Förutom de filter som är tillgängliga på föregående sida, kan du nu också filtrera på grundval av monitor-tjänsten (till exempel plattform för mått), övervakningsvillkor (utlösta eller löst), allvarlighetsgrad, aviseringstillståndet (ny/bekräftas/stängda) eller smart grupp-ID.

   ![Alla aviseringar](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Om du Kom till sidan genom att klicka på något av de allvarlighetsgrad band kommer listan att förväg filtrerad för den allvarlighetsgraden när hamnar du på den här sidan.
 
1. Klicka på valfri avisering instans öppnas den **aviseringsinformation** sidan, så att du kan fördjupa till information om den specifika aviseringsinstansen.   
   ![Aviseringsinformation](media/alerts-managing-alert-instances/alert-details.jpg)  

