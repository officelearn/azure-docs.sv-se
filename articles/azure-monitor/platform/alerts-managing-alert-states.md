---
title: Hantera tillstånd för aviseringar och smarta grupper
description: Hantera tillstånd för avisering och smart-instanser
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 2aa521b0552b60e5a875a5f46ab9887c6e5b6e3e
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472066"
---
# <a name="manage-alert-and-smart-group-states"></a>Hantera tillstånd för aviseringar och smarta grupper
Aviseringar i Azure Monitor har nu en [Avisera tillstånd och ett övervakningsvillkor](https://aka.ms/azure-alerts-overview) och Smart grupper på samma sätt som har en [smart grupptillstånd](https://aka.ms/smart-groups). Ändringar av tillståndet fångas nu i historik som är kopplade till respektive avisering eller smart grupp. Den här artikeln vägleder dig genom processen för att ändra på statusen för både en avisering och smart gruppen.

## <a name="change-the-state-of-an-alert"></a>Ändra tillståndet för en avisering
1. Du kan ändra tillståndet för en avisering på följande sätt: 
    * Klicka på kryssrutan bredvid de aviseringar som du vill ändra tillståndet för alla aviseringar på sidan och klicka på Ändra tillstånd.   
    ![Övervakning](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * På sidan aviseringsinformation för en viss avisering instans kan du klicka på Ändra tillstånd   
    ![Övervakning](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * I fönstret Smart grupp på sidan aviseringsinformation för specifika aviseringsinstansen Klicka på kryssrutan bredvid de aviseringar som du vill    
    ![Övervakning](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * På sidan Smart gruppinformation i listan över medlemmar aviseringar kan du klickar du på kryssrutan bredvid de aviseringar som du vill ändra status för och klicka på Ändra Stateto ändra tillståndet för och klicka på Ändra tillstånd.   
    ![Övervakning](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. När du klickar på Ändra tillstånd, öppnas ett popup-fönster så att du kan markera läget (nytt/godkänd/stängd) och ange en kommentar om det behövs.   
![Övervakning](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. När detta är gjort registreras tillståndsändringen i historiken för respektive aviseringen. Detta kan visas genom att öppna sidan med respektive och kontrollera avsnittet tidigare.    
![Övervakning](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Ändra tillståndet för en smart grupp
1. Du kan ändra tillståndet för en smart grupp på följande sätt:
    1. I sidan med Smart grupp kan du klicka på kryssrutan bredvid de smarta grupper som du vill ändra tillståndet för och klicka på Ändra tillstånd  
    ![Övervakning](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Smart gruppinformation på sidan kan du klicka på Ändra tillstånd        
    ![Övervakning](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. När du klickar på Ändra tillstånd, öppnas ett popup-fönster så att du kan markera läget (nytt/godkänd/stängd) och ange en kommentar om det behövs. 
![Övervakning](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Ändra tillståndet för en smart grupp ändrar inte tillståndet för aviseringarna som enskild medlem.

1. När detta är gjort registreras tillståndsändringen i historiken för respektive smart grupp. Detta kan visas genom att öppna sidan med respektive och kontrollera avsnittet tidigare.     
![Övervakning](./media/alerts-managing-alert-states/state-sg-history.jpg)

