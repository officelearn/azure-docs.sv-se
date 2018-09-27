---
title: Azure-underhållsscheman (förhandsversion) | Microsoft Docs
description: Schemalägga Underhåll kan kunderna planera runt händelserna behövs schemalagt underhåll Azure SQL Data warehouse-tjänsten använder för att lansera nya funktioner, uppgraderingar och uppdateringar.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228453"
---
# <a name="change-a-maintenance-schedule"></a>Ändra ett underhållsschema 

## <a name="portal"></a>Portalen
Ett underhållsschema kan uppdateras eller ändras när som helst. Men om den valda instansen är för närvarande går igenom active underhållscykeln inställningarna sparas och bara aktiveras under nästa identifierade underhållsperioden. [Läs mer](https://docs.microsoft.com/azure/service-health/resource-health-overview) om övervakning av ditt informationslager under en aktiv underhållshändelse. 

I förhandsversion, kommer vi ber dig att välja två underhållsfönster under en period på 7 dagar. Varje underhållsperiod kan vara mellan 3 och 8 timmar varje, med 3 timmar som för närvarande är det kortaste tillgängliga alternativet. Underhåll kan inträffa när som helst inom en identifierade underhållsfönster men kommer inte ske utanför de identifieras tidsfönster med föregående meddelande, kommer du att också upplevelse vara kort förlust av anslutning som tjänsten distribuerar ny kod till dina data datalager. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identifiera primära och sekundära windows

Primära och sekundära windows måste identifieras i separata dag intervall (dvs, primär fönstret (tisdag – torsdag), sekundära fönster (lördag – söndag)

Utför följande steg om du vill ändra underhållsschemat som kopplats till ditt informationslager i portalen.
1.  Logga in på [Azure-portalen](https://portal.azure.com/).
2.  Välj det datalager som du vill uppdatera. Sidan öppnas på översiktsbladet. 
3.  Inställningssidan Underhåll schemat kan användas av antingen genom att klicka på Underhåll schema (förhandsversion) sammanfattning av länken i översiktsbladet eller via alternativet Underhåll schema i den vänstra menyn för resursen.  

    ![Översikt över bladalternativ](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Du kan identifiera den önskade dagsintervall på din primära underhållsperiod med hjälp av alternativknapparna överst på sidan. Det här urvalet bestämmer om din primära fönstret sker på en vardag eller under helgen. Ditt val kommer uppdateras också listrutan värdena nedan. Under förhandsversionen stöder vissa regioner än inte den fullständiga uppsättningen alternativen i dag. Dessa värden uppdateras under de kommande månaderna.

   ![Bladet med inställningar för underhåll](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Välj din önskade primära och sekundära underhållsfönster med hjälp av dagen, starttid och tid fönstret listrutorna nedan. Schemalägg Sammanfattning längst ned på bladet uppdateras baserat på listrutan värden som väljs.

#### <a name="dropdown-options"></a>Alternativ för listrutan
- Dag: Önskade dag för att utföra underhåll under det markerade fönstret.
- Starttid: önskad starttid underhåll.
- Tidsfönster: önskade varaktigheten för tidsfönstret.

  Klicka på Spara när du har valt dina önskade underhållsfönster. Ett bekräftelsemeddelande visas bekräftar ditt nya schema inte är aktiv. Om du sparar ett schema i en region som inte stöder ännu Underhåll schemaläggning, visas följande meddelande. Dina inställningar sparas och aktiveras när funktionen blir tillgänglig i den valda regionen.    

    ![Inte aktiv i regionen popup](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Nästa steg
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om Webhook-åtgärder för loggaviseringsregler.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health


