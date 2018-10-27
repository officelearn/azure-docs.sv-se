---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 350dbc286c060ad10c21ebe1e8715b910871f837
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165833"
---
<!--author=alkohli last changed: 11/02/17 -->

#### <a name="to-install-updates-via-the-azure-portal"></a>Installera uppdateringar via Azure Portal

1. Gå till din StorSimple-enhetshanterare och välj **Enheter**. Välj i listan över enheter som är anslutna till tjänsten och klicka på den enhet som du vill uppdatera.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. I bladet **Inställningar** klickar du på **Enhetsuppdateringar**.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Du ser ett meddelande om programuppdateringar är tillgängliga. Du kan också klicka på **Scan** (Sök). Anteckna programvaruversion som du kör. 

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Du meddelas när genomsökningen startar och slutförs.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. När uppdateringarna har genomsökts klickar du på alternativet för att **hämta uppdateringar**.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. I den **nya uppdateringar** bladet granska viktig information. Observera också att du måste bekräfta installationen när uppdateringarna har hämtats. Klicka på **OK**.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Du meddelas när överföringen startar och slutförs.

     ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. På bladet **Enhetsuppdateringar** klickar du på **Installera**.

     ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. På bladet **Nya uppdateringar** får du en varning om att uppdateringen är störande. En virtuell matris är en enhet med en enda nod. Enheten startas om när den har uppdaterats. Detta avbryter pågående IO. Klicka på **OK** för att installera uppdateringarna.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Du får ett meddelande när installationen startar.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  När installationen är klar klickar du på länken **Visa jobb** på bladet **Enhetsuppdateringar** för att övervaka installationen. 

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Den här åtgärden tar dig till den **installera uppdateringar** bladet. Du kan visa detaljerad information om jobbet här.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Om du utgick från en virtuell matris som kör programvaruversion uppdatering 0.6 (10.0.10293.0) måste du nu kör uppdatering 1 och är klar. Du kan hoppa över de återstående stegen. Om du utgick från en virtuell matris som kör en programvaruversion före uppdatering 0.6 (10.0.10293.0) måste uppdaterat du nu till uppdatering 0.6. Du ser ett annat meddelande som anger att uppdateringar är tillgängliga. Upprepa steg 4 – 8 för att installera uppdatering 1.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

