---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b4c3fcb86fb098263840accc561785a40b767952
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187531"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Installera uppdateringar via Azure Portal

1. Gå till din StorSimple-enhetshanterare och välj **Enheter**. Välj i listan över enheter som är anslutna till tjänsten och klicka på den enhet som du vill uppdatera.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. I bladet **Inställningar** klickar du på **Enhetsuppdateringar**.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Du ser ett meddelande om programuppdateringar är tillgängliga. Du kan också klicka på **Scan** (Sök). Anteckna den program varu version som du kör. 

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Du får ett meddelande när genomsökningen startar och slutförs.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. När uppdateringarna har genomsökts klickar du på alternativet för att **hämta uppdateringar**.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. På bladet **nya uppdateringar** granskar du viktig information. Observera också att när uppdateringarna har hämtats måste du bekräfta installationen. Klicka på **OK**.

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

    Den här åtgärden tar dig till bladet **Installera uppdateringar** . Du kan visa detaljerad information om jobbet här.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Om du startade med en virtuell matris som kör program varu versionen uppdatering 0,6 (10.0.10293.0) kör du nu uppdatering 1 och är färdig. Du kan hoppa över de återstående stegen. Om du startade med en virtuell matris som kör en program varu version före uppdateringen 0,6 (10.0.10293.0) har du nu uppdaterat uppdateringen 0,6. Ett annat meddelande visas som anger att uppdateringar är tillgängliga. Upprepa steg 4-8 för att installera uppdatering 1.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

