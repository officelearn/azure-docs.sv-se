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
ms.openlocfilehash: 4ba5c8b69776b39d8a6640744b0c24600f3a0d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187530"
---
#### <a name="to-create-a-new-service"></a>Skapa en ny tjänst

1.  Logga in på Azure-portalen med hjälp av <https://portal.azure.com/>dina Microsoft-kontouppgifter: . Om du distribuerar enheten i regeringsportalen loggar du in på:<https://portal.azure.us/>

2.  Klicka på + **Skapa en** &gt; **lagringslagring** &gt; **i StorSimple Virtual Series i Azure-portalen**.

    ![Skapa ny tjänst](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Gör följande i bladet **StorSimple Enhetshanteraren** som öppnas:

    1.  Ange ett unikt **resursnamn** för tjänsten. Resursnamnet är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 2 och 50 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

    2.  Välj en **prenumeration** från listrutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet syns inte om du bara har en prenumeration.

    3.  För **resursgrupp**väljer du en befintlig eller skapar en ny grupp. Mer information finns i avsnittet om [Azure-resursgrupper](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Ange en **plats** för din tjänst. Mer information om vilka tjänster som är tillgängliga i vilken region finns i [Azure-regioner.](https://azure.microsoft.com/regions/#services) I allmänhet väljer du en **plats** närmast den geografiska region där du vill distribuera enheten. Du kan också ta med följande i beräkningarna:

        -   Om du har befintliga arbetsbelastningar i Azure som du också tänker distribuera med din StorSimple-enhet rekommenderar vi att du använder det datacentret.

        -   Din StorSimple Enhetshanteraren och Azure-lagring kan finnas på två separata platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat. Om du vill skapa ett Azure-lagringskonto navigerar du till Azure Storage i Azure-portalen och följer stegen som beskrivs i [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). När du har skapat kontot lägger du till det till StorSimple Device Manager-tjänsten genom att följa stegen i [Konfigurera ett nytt lagringskonto för tjänsten](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Om distributionen av den virtuella enheten i regeringsportalen är StorSimple Device Manager-tjänsten tillgänglig på platser i USA Iowa och US Virginia.

    5.  Välj **Skapa ett nytt Azure-lagringskonto** om du automatiskt vill skapa ett lagringskonto med tjänsten. Ange ett **lagringskontonamn**. Avmarkera kryssrutan om du behöver ha din data på en annan plats.

    6.  Markera **Fäst på instrumentpanelen** om du vill skapa en snabblänk till tjänsten på instrumentpanelen.

    7.  Skapa StorSimple Device Manager genom att klicka på **Skapa**.

        ![Skapa ny tjänst](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Du dirigeras till **Tjänstens** målsida. Det tar några minuter att skapa tjänsten. När tjänsten har skapats, kommer du att meddelas och tjänstens status kommer att ändras till **Aktiv**.


