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
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408757"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Installera uppdateringar via Azure Portal

1. Gå till din StorSimple-enhetshanterare och välj **Enheter**. Välj i listan över enheter som är anslutna till tjänsten och klicka på den enhet som du vill uppdatera.

2. Under **inställningar**, klickar du på **enhetsuppdateringar**.  

3. Du ser ett meddelande om programuppdateringar är tillgängliga. Du kan också klicka på **Scan** (Sök). Anteckna programvaruversion som du kör. 

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Du meddelas när genomsökningen startar och slutförs.
 
4. När uppdateringarna har genomsökts klickar du på alternativet för att **hämta uppdateringar**. Under **nya uppdateringar**, granska viktig information. Observera också att du måste bekräfta installationen när uppdateringarna har hämtats. Klicka på **OK**.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    Du meddelas när överföringen startar och slutförs.

5. Under **enhetsuppdateringar**, klickar du på **installera**.

     ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Under **nya uppdateringar**, du får en varning att uppdateringen är störande. En virtuell matris är en enhet med en enda nod. Enheten startas om när den har uppdaterats. Detta avbryter pågående IO. Klicka på **OK** för att installera uppdateringarna.

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    Du får ett meddelande när installationen startar.

7.  När installationen är klar klickar du på **visa jobb** länk. Den här åtgärden tar dig till den **installera uppdateringar** bladet. Du kan visa detaljerad information om jobbet här. 

    ![uppdatera enhet](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Om du utgick från en virtuell matris som kör programvaruversion uppdatering 1 (10.0.10296.0) måste du nu kör Update 1.1 och är klar. Du kan hoppa över de återstående stegen. 

    Om du utgick från en virtuell matris som kör programvaruversion uppdatering 0.6 (10.0.10293.0) har nu uppdaterats till version 1.0. Du ser ett annat meddelande som anger att uppdateringar är tillgängliga. Upprepa steg 4 – 7 för att installera uppdateringen 1.1.

    

