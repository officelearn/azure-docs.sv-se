---
title: Hantera aviseringsinstanser
description: Hantera aviserings instanser i Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: dbc7d37ce311d203ee051b5a23632d2bf401e27a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034829"
---
# <a name="manage-alert-instances"></a>Hantera aviseringsinstanser
Med den [enhetliga aviserings upplevelsen](https://aka.ms/azure-alerts-overview) i Azure Monitor kan du nu se alla olika typer av aviseringar i Azure, som sträcker sig över flera prenumerationer i ett enda fönster i glaset. Den här artikeln vägleder dig igenom hur du kan visa dina aviserings instanser och hur du går vidare till portalen för att hitta vissa varnings instanser för fel sökning.

> [!NOTE]
   >  Endast aviseringar som genererats under de senaste 30 dagarna kan nås i UX-eller REST-API: erna.

1. Det finns tre sätt att landa på aviserings Sidan

   + I [portalen](https://portal.azure.com/)väljer du **övervaka** och under Monitor-avsnittet – väljer du **aviseringar**.  
     ![Övervakning](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Du kan navigera till aviseringar inom kontexten för en speciell **resurs**. När en resurs har öppnats navigerar du genom dess innehålls förteckning till avsnittet övervakning och väljer **aviseringar**, där landnings sidan filtreras för aviseringar på den aktuella resursen.
   
     ![Övervakning](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + Du kan navigera till aviseringar inom kontexten för en speciell **resurs grupp**. När en resurs grupp har öppnats navigerar du genom dess innehålls förteckning till avsnittet övervakning och väljer **aviseringar**, där landnings sidan filtreras för aviseringar i den aktuella resurs gruppen.    
   
     ![Övervakning](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Du kommer att hamna på sidan **aviserings Sammanfattning** , som ger dig en översikt över alla aviserings instanser i Azure. Du kan ändra sammanfattningsvyn genom att välja **flera prenumerationer** (högst 5) eller genom att filtrera över **resurs grupper**, vissa **resurser**eller **tidsintervall**. Klicka på antingen totala aviseringar eller något av allvarlighets graderna för att gå till listvyn för dina aviseringar.     
   ![Aviserings Sammanfattning](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Du kommer att hamna på sidan **alla aviseringar** där du ser alla varnings instanser i Azure i listan. Om du kommer till portalen från ett aviserings meddelande kan du använda de filter som är tillgängliga för att begränsa den specifika varnings instansen. (**Obs!** om du kom till sidan genom att klicka på något av allvarlighets graderna, kommer listan att filtreras för den allvarlighets graden när du landar. Förutom de filter som är tillgängliga på föregående sida kan du nu även filtrera utifrån övervaknings tjänsten (till exempel plattform för mått), övervaka villkor (utlöst eller löst), allvarlighets grad, aviserings tillstånd (nytt/bekräftat/stängt) eller det smarta grupp-ID: t.

   ![Alla aviseringar](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Om du har kommit till sidan genom att klicka på något av allvarlighets graderna, kommer listan att filtreras i förväg för den allvarlighets graden när du landar på den här sidan.
 
1. Om du klickar på en varnings instans öppnas sidan **aviserings information** , så att du kan få detaljerad information om den aktuella varnings instansen.   
   ![Aviserings information](media/alerts-managing-alert-instances/alert-details.jpg)  

