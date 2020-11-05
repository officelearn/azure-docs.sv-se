---
title: ta med fil
description: ta med fil
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 4ed290e861a0fdebeb76b9e08e62c03eda52be82
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375794"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Installera uppdateringar via Azure Portal

1. Gå till din StorSimple-enhetshanterare och välj **Enheter**. Välj i listan över enheter som är anslutna till tjänsten och klicka på den enhet som du vill uppdatera.

2. Under **Inställningar** klickar du på **enhets uppdateringar**.  

3. Du ser ett meddelande om programuppdateringar är tillgängliga. Du kan också klicka på **Scan** (Sök). Anteckna den program varu version som du kör. 

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Du får ett meddelande när genomsökningen startar och slutförs.
 
4. När uppdateringarna har genomsökts klickar du på alternativet för att **hämta uppdateringar**. Granska viktig information under **nya uppdateringar**. Observera också att när uppdateringarna har hämtats måste du bekräfta installationen. Klicka på **OK**.

    ![uppdatera enhet 2](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Du meddelas när överföringen startar och slutförs.

5. Under **enhets uppdateringar** klickar du på **Installera**.

     ![uppdatera enhet 3](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Under **nya uppdateringar** varnas du om att uppdateringen är störande. En virtuell matris är en enhet med en enda nod. Enheten startas om när den har uppdaterats. Detta avbryter pågående IO. Klicka på **OK** för att installera uppdateringarna.

    ![uppdatera enhet 4](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Du får ett meddelande när installationen startar.

7.  När installations jobbet har slutförts klickar du på **Visa jobb** länk. Den här åtgärden tar dig till bladet **Installera uppdateringar** . Du kan visa detaljerad information om jobbet här. 

    ![uppdatera enhet 5](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Om du startade med en virtuell matris som kör program version uppdatering 1 (10.0.10296.0) kör du nu uppdatering 1,1 och är färdig. Du kan hoppa över de återstående stegen. 

    Om du startade med en virtuell matris som kör program varu versionen uppdatering 0,6 (10.0.10293.0) har du nu uppdaterat uppdateringen 1,0. Ett annat meddelande visas som anger att uppdateringar är tillgängliga. Upprepa steg 4-7 för att installera uppdatering 1,1.

    

