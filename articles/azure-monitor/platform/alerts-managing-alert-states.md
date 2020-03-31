---
title: Hantera tillstånd för aviseringar och smarta grupper
description: Hantera tillstånden för varnings- och smartgruppsinstanserna
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667524"
---
# <a name="manage-alert-and-smart-group-states"></a>Hantera tillstånd för aviseringar och smarta grupper

Aviseringar i Azure Monitor har nu ett [varningstillstånd och ett övervakarvillkor](https://aka.ms/azure-alerts-overview) och smartgrupper har på samma sätt ett [smart grupptillstånd](https://aka.ms/smart-groups). Ändringar i tillståndet fångas nu in i historiken som är associerad med respektive avisering eller smart grupp. I den här artikeln får du hjälp med hur du ändrar tillståndet, både för en avisering och en smart grupp.

## <a name="change-the-state-of-an-alert"></a>Ändra tillståndet för en avisering

1. Du kan ändra tillståndet för en avisering på följande sätt: 
    * På sidan Alla aviseringar klickar du på kryssrutan bredvid de aviseringar som du vill ändra tillståndet för och klickar på Ändra tillstånd.   
    ![Övervakning](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * På sidan Aviseringsinformation för en viss varningsinstans kan du klicka på ändra tillstånd   
    ![Övervakning](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * På sidan Aviseringsinformation för en viss aviseringsinstans kan du klicka på kryssrutan bredvid de aviseringar du vill ha i fönstret Smart grupp    
    ![Övervakning](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * På sidan Information om smarta grupper kan du i listan med medlemsaviseringar klicka på kryssrutan bredvid de aviseringar som du vill ändra tillståndet för och klicka på Ändra tillstånd om du vill ändra tillståndet för och klicka på Ändra tillstånd.   
    ![Övervakning](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. När du klickar på Ändra tillstånd öppnas en popup så att du kan välja tillstånd (Ny/Bekräftad/Stängd) och ange en kommentar om det behövs.   
![Övervakning](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. När detta är gjort registreras tillståndsändringen i respektive aviserings historia. Detta kan visas genom att öppna respektive informationssida och kontrollera historikavsnittet.    
![Övervakning](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Ändra tillståndet för en smart grupp
1. Du kan ändra tillståndet för en smart grupp på följande sätt:
    1. På listsidan för Smart grupp kan du klicka på kryssrutan bredvid de smarta grupper som du vill ändra tillståndet för och klicka på Ändra tillstånd  
    ![Övervakning](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. På sidan Information om smart grupp kan du klicka på ändra tillstånd        
    ![Övervakning](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. När du klickar på Ändra tillstånd öppnas en popup så att du kan välja tillstånd (Ny/Bekräftad/Stängd) och ange en kommentar om det behövs. 
![Övervakning](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Om du ändrar tillståndet för en smart grupp ändras inte tillståndet för de enskilda medlemsaviseringarna.

1. När detta är gjort registreras tillståndsändringen i respektive smart grupps historia. Detta kan visas genom att öppna respektive informationssida och kontrollera historikavsnittet.     
![Övervakning](./media/alerts-managing-alert-states/state-sg-history.jpg)

