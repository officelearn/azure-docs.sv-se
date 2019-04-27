---
title: Azure-underhållsscheman (förhandsversion) | Microsoft Docs
description: Schemaläggning av underhållsläge ger kunder möjlighet att planera runt de nödvändiga schemalagda underhållshändelser som Azure SQL Data Warehouse-tjänsten använder för att lansera nya funktioner, uppgraderingar och uppdateringar.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680442"
---
# <a name="change-a-maintenance-schedule"></a>Ändra ett underhållsschema 

## <a name="portal"></a>Portalen
Ett underhållsschema kan uppdateras eller ändras när som helst. Om den valda instansen genomgår en aktiv underhållsperiod, ska inställningarna sparas. De ska aktiveras under nästa identifierade underhållsperioden. [Läs mer](https://docs.microsoft.com/azure/service-health/resource-health-overview) om övervakning av ditt informationslager under en aktiv underhållshändelse. 

Om du vill använda underhållsscheman måste du välja två underhållsfönster under en period på sju dagar. Varje underhållsperiod kan vara tre till åtta timmar. Underhåll kan inträffa när som helst inom en underhållsperiod, men inträffar inte utanför tidsfönster utan föregående meddelande. Du får också en kort förlust av anslutning som tjänsten distribuerar ny kod till ditt informationslager. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identifiera primära och sekundära windows

Primära och sekundära windows måste ha separata dag intervall. Ett exempel är en primär tisdag – torsdag-fönstret och en sekundär fönstret av lördag – söndag.

Om du vill ändra underhållsschema för ditt informationslager, gör du följande:
1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Välj det datalager som du vill uppdatera. Sidan öppnas på översiktsbladet. 
3.  Öppna sidan för underhåll schemainställningar genom att välja den **underhållsschema (förhandsversion) sammanfattning** länk på översiktsbladet. Eller Välj den **underhållsschema** alternativet på vänster resurs-menyn.  

    ![Översikt över bladalternativ](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifiera den önskade dagsintervall på din primära underhållsperiod med hjälp av alternativen överst på sidan. Det här urvalet bestämmer om din primära fönstret sker på en vardag eller under helgen. Ditt val uppdateras listrutan-värden. Under förhandsperioden kan vissa regioner än stöder inte den fullständiga uppsättningen tillgängliga **dag** alternativ.

   ![Bladet med inställningar för underhåll](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Välj din önskade primära och sekundära underhållsfönster med hjälp av de nedrullningsbara listrutorna:
   - **Dag**: Prioriterade dag för att utföra underhåll under det markerade fönstret.
   - **Starttid**: Önskad starttid för underhållsperioden.
   - **Tidsfönster**: Önskade varaktigheten för tidsfönstret.

   Den **Schemalägg sammanfattning** området längst ned på bladet uppdateras baserat på de värden som du har valt. 
  
6. Välj **Spara**. Ett meddelande visas bekräftar att ditt nya schema är aktiv. 

   Om du sparar ett schema i en region som inte stöder Underhåll schemaläggning, visas följande meddelande. Dina inställningar sparas och aktiveras när funktionen blir tillgänglig i den valda regionen.    

   ![Meddelande om regiontillgänglighet](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Nästa steg
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om webhook-åtgärder för loggaviseringsregler.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health.


