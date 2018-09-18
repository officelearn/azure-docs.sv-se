---
title: ta med fil
description: ta med fil
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 4285ee94d2f2a9b5fff9d1896cdf7013f88801ab
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979214"
---
#### <a name="to-create-a-new-service"></a>Skapa en ny tjänst

1.  Använd dina Microsoft-autentiseringsuppgifter och logga in på Azure Portal på denna URL: <https://portal.azure.com/>. Om du distribuerar enhet i Government portal, kan du logga in på: <https://portal.azure.us/>

2.  I Azure-portalen klickar du på **+ skapa en resurs** &gt; **Storage** &gt; **virtuella StorSimple-serien**.

    ![Skapa ny tjänst](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  I den **StorSimple Device Manager** bladet som öppnas, gör du följande:

    1.  Ange ett unikt **resursnamn** för tjänsten. Resursnamnet är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 2 och 50 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

    2.  Välj en **prenumeration** från listrutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet syns inte om du bara har en prenumeration.

    3.  För **resursgrupp**väljer du en befintlig eller skapa en ny grupp. Mer information finns i avsnittet om [Azure-resursgrupper](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Ange en **plats** för din tjänst. Se [Azure-regionerna](https://azure.microsoft.com/regions/#services) för mer information om vilka tjänster som är tillgängliga i vilken region. I allmänhet väljer du en **plats** närmast den geografiska region där du vill distribuera enheten. Du kan också ta med följande i beräkningarna:

        -   Om du har befintliga arbetsbelastningar i Azure som du också tänker distribuera med din StorSimple-enhet, rekommenderar vi att du använder det datacentret.

        -   Din StorSimple Device Manager och Azure-lagring kan vara på två olika platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat. Om du vill skapa ett Azure storage-konto, gå till Azure Storage i Azure portal och följ stegen som beskrivs i [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). När du har skapat kontot lägger du till det till StorSimple Device Manager-tjänsten genom att följa stegen i [Konfigurera ett nytt lagringskonto för tjänsten](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Om du distribuerar den virtuella enheten i Government Portal, är StorSimple Device Manager-tjänsten tillgänglig i USA Iowa och US Virginia platser.

    5.  Välj **skapar ett nytt Azure storage-konto** att automatiskt skapa ett lagringskonto med tjänsten. Ange en **lagringskontonamn**. Avmarkera kryssrutan om du behöver ha din data på en annan plats.

    6.  Markera **Fäst på instrumentpanelen** om du vill skapa en snabblänk till tjänsten på instrumentpanelen.

    7.  Skapa StorSimple Device Manager genom att klicka på **Skapa**.

        ![Skapa ny tjänst](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Du dirigeras till den **Service** landningssida. Det tar några minuter att skapa tjänsten. När tjänsten har skapats, kommer du att meddelas och tjänstens status kommer att ändras till **Aktiv**.


