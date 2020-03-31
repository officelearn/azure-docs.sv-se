---
title: Diskmått som har utfasning i Azure-portalen | Microsoft-dokument
description: Ta reda på vilka diskmått som har inaktuella och hur du uppdaterar måttaviseringarna för att använda nya mått.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299808"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Diskmått som har återkallats i Azure-portalen

Inaktuella diskrelaterade mått tas snart bort från Azure-portalen. En ny version av varje inaktuellt mått är tillgänglig för dig att använda. Den här artikeln visar vilka mått som är nya och hur du uppdaterar dina måttaviseringar för att använda dem.

## <a name="list-of-new-metrics"></a>Lista över nya mått

Den här tabellen mappar varje inaktuellt mått till motsvarande nya mått. 

|Inaktuellt mått|Nytt (ersättnings)mått|
|----|----|
|Datadisk QD (föråldrad)|Djup för datadiskkö (förhandsgranskning)|
|Läsbyten för datadisk/sek (inaktuellt)|Läsbyte för datadisk per sekund (förhandsgranskning)|
|Läsåtgärder för datadiskar/sek (inaktuella)|Läsåtgärder för datadisk/sek (förhandsgranskning)|
|Skrivbyte för datadisk/sek (inaktuellt)|Skrivbyte för datadisk/sek (förhandsgranskning)|
|Datadiskskrivningsoperationer/sek (inaktuell)|Datadiskskrivningsåtgärder/sek (förhandsgranskning)|
|OS QD (föråldrad)|Os-ködjup (förhandsgranskning)|
|Os läsbyten/sek (inaktuella)|Os läs byte/sek (förhandsgranskning)|
|Os-läsåtgärder/sek (inaktuella)|Os-läsåtgärder/sek (förhandsgranskning)|
|OS skrivbyten/sek (inaktuella)|OS skrivbyten/sek (förhandsgranskning)|
|OS-skrivåtgärder/sek (inaktuella)|OS-skrivåtgärder/sek (förhandsgranskning)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrera mått i dina måttaviseringar

Uppdatera dina måttaviseringar för att använda nya mått.

1. Sök efter **aviseringar**i Azure-portalen . Välj sedan **Aviseringar**i avsnittet **Tjänster** .

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Välj knappen **Hantera aviseringsregler** på sidan **Aviseringar.** 

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Markera kryssrutan **Virtuella datorer** i listrutan **Resursgrupp** och markera kryssrutan **Mått** i listrutan **Signaltyp.** 

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Identifiera villkor som relaterar till diskar i listan över mått. Klicka på namnet på regeln. 

   Namnet visas som en hyperlänk i kolumnen **Namn** i tabellen.

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Klicka på villkor för aviseringen i avsnittet **Villkor** på sidan **Regelhantering.** 

   Villkoret visas som en hyperlänk.  

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Sidan **Konfigurera signallogik** visas och inställningarna för villkoret visas i avsnittet **Varningslogik** på den sidan.

6. Gör en registrering av dessa inställningar när de försvinner när du tar bort det inaktuella måttet.

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Överväg att fånga dessa inställningar i en skärmdump eller i en textfil. 

7. Klicka på länken **Tillbaka för att signalera markering.**

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. På sidan **Konfigurera signallogik** väljer du lämpligt ersättningsmått (nytt mått). Använd [tabellen](#update-metrics) som visas tidigare i den här artikeln för att identifiera namnet på det nya måttet.

   > [!TIP] 
   > Börja skriva i sökfältet för att begränsa listan med måttnamn. 

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Välj knappen **Klar.** 

   > [!div class="mx-imgBorder"]
   > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Genomför ändringarna genom att välja knappen **Spara.** 

    > [!div class="mx-imgBorder"]
    > ![Bildbeskrivning](./media/portal-disk-metrics-deprecation/save-new-metric.png)






