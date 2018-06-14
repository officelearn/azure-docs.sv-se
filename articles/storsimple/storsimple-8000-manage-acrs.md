---
title: Hantera access control-poster i StorSimple | Microsoft Docs
description: Beskriver hur du använder access control-poster (ACRs) för att avgöra vilka värdar som kan ansluta till en volym på StorSimple-enheten.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874908"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Använda StorSimple Manager-tjänsten för att hantera access control-poster

## <a name="overview"></a>Översikt
Access control-poster (ACRs) kan du ange vilka värdar som kan ansluta till en volym på StorSimple-enheten. ACRs är inställd på en viss volym och innehålla iSCSI-kvalificerade namn (kvalificerade) på värdarna. När en värd försöker ansluta till en volym kontrollerar enheten ACR som är kopplad till volymen för IQN namn och om det finns en matchning, upprättas anslutningen. Åtkomstkontrollen registrerar i den **Configuration** avsnitt i din StorSimple Enhetshanteraren service bladet visa alla åtkomstkontroll poster med motsvarande kvalificerade värdar.

Den här självstudiekursen beskriver följande vanliga ACR-relaterade aktiviteter:

* Lägg till en åtkomstkontrollpost
* Redigera en åtkomstkontrollpost
* Ta bort en åtkomstkontrollpost

> [!IMPORTANT]
> * När du tilldelar en ACR till en volym, se till att volymen inte samtidigt kan kommas åt av mer än en icke-klustrad värd eftersom detta kan skada volymen.
> * När du tar bort en ACR från en volym, se till att motsvarande värden inte har åtkomst till volymen eftersom borttagningen kan resultera i en skrivskyddad avbrott.

## <a name="get-the-iqn"></a>Hämta IQN

Utför följande steg för att hämta IQN för en Windows-värd som kör Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Lägg till en åtkomstkontrollpost
Du använder den **Configuration** avsnitt i bladet StorSimple Enhetshanteraren tjänsten för att lägga till ACRs. Normalt kopplar du en ACR en volym.

Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-acr"></a>Att lägga till en ACR

1. Gå till Enhetshanteraren för StorSimple-tjänsten genom att dubbelklicka på tjänstens namn och sedan inom den **Configuration** klickar du på **Access control-poster**.
2. I den **Access control-poster** bladet, klickar du på **+ Lägg till ACR**.

    ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. I den **lägga till ACR** bladet gör du följande:

    1. Ange ett namn för din ACR.
    
    2. Ange namnet på Windows Server-värd under IQN **iSCSI Initiator namn (IQN)**.

    3. Klicka på **Lägg till** att skapa ACR.

        ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Den nyligen tillagda ACR visas i tabellformat lista över ACRs.

    ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Redigera en åtkomstkontrollpost
Du använder den **Configuration** avsnitt i StorSimple Enhetshanteraren service bladet för att redigera ACRs.

> [!NOTE]
> Vi rekommenderar att du ändrar dessa ACRs som för närvarande inte används. Om du vill redigera en ACR som är kopplade till en volym som används, måste du först göra volymen offline.

Utför följande steg om du vill redigera en ACR.

#### <a name="to-edit-an-access-control-record"></a>Så här redigerar du en åtkomstkontrollpost
1.  Gå till Enhetshanteraren för StorSimple-tjänsten genom att dubbelklicka på tjänstens namn och sedan inom den **Configuration** klickar du på **Access control-poster**.

    ![Gå till access control-poster](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klicka i tabellform lista av access control-poster, och välj ACR som du vill ändra.

    ![Redigera access control-poster](./media/storsimple-8000-manage-acrs/editacr1.png)

3. I den **redigera åtkomstkontrollpost** bladet, ange en annan IQN för en annan värd.

    ![Redigera access control-poster](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klicka på **Spara**. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. 

    ![Redigera access control-poster](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Du meddelas när ACR uppdateras. Tabell listar också uppdateras för att avspegla ändringen.

   
## <a name="delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost
Du använder den **Configuration** avsnitt i bladet StorSimple Enhetshanteraren service att ta bort ACRs.

> [!NOTE]
> Du kan ta bort dessa ACRs som för närvarande inte används. Om du vill ta bort en ACR som är kopplade till en volym som används, måste du först göra volymen offline.

Utför följande steg för att ta bort en åtkomstkontrollpost.

#### <a name="to-delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost
1.  Gå till Enhetshanteraren för StorSimple-tjänsten genom att dubbelklicka på tjänstens namn och sedan inom den **Configuration** klickar du på **Access control-poster**.

    ![Gå till access control-poster](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klicka i tabellform lista av access control-poster, och välj ACR som du vill ta bort.

    ![Gå till access control-poster](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Högerklicka på att anropa snabbmenyn och välj **ta bort**.

    ![Gå till access control-poster](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. När du uppmanas att bekräfta, granska informationen och klicka sedan på **ta bort**.

    ![Gå till access control-poster](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Du meddelas när borttagningen är klar. Tabell listan uppdateras borttagningen.

    ![Gå till access control-poster](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [hantera StorSimple-volymer](storsimple-8000-manage-volumes-u2.md).
* Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

