---
title: Hantera åtkomstkontrollposter i StorSimple | Microsoft Docs
description: Beskriver hur du använder åtkomstkontrollposter (åtkomstkontrollposter) för att fastställa vilka värdar som kan ansluta till en volym på StorSimple-enheten.
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
ms.openlocfilehash: ade7da25d2307a382c17e7a3cbb26b601c34ef78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60321628"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Använda StorSimple Manager-tjänsten för att hantera åtkomstkontrollposter

## <a name="overview"></a>Översikt
Åtkomstkontrollposter (åtkomstkontrollposter) kan du ange vilka värdar som kan ansluta till en volym på StorSimple-enheten. Åtkomstkontrollposter är inställd på en specifik volym och innehålla iSCSI-kvalificerade namn (kvalificerade) på värdarna. När en värd försöker ansluta till en volym, kontrollerar enheten som är associerade med den volymen för namnet på ACR och om det finns en matchning, upprättas anslutningen. Åtkomstkontrollen poster i den **Configuration** på din StorSimple Device Manager service-bladet visas alla åtkomstkontrollposter med motsvarande kvalificerade värdar.

Den här självstudien beskrivs följande vanliga ACR-relaterade uppgifter:

* Lägg till en åtkomstkontrollpost
* Redigera en åtkomstkontrollpost
* Ta bort en åtkomstkontrollpost

> [!IMPORTANT]
> * När du tilldelar en ACR till en volym, noga att volymen inte samtidigt används av mer än en icke-klustrad värd eftersom detta kan skada volymen.
> * När du tar bort en ACR från en volym, se till att motsvarande värden inte har åtkomst till volymen eftersom borttagningen kan resultera i en skrivskyddad avbrott.

## <a name="get-the-iqn"></a>Hämta IQN

Utför följande steg för att hämta IQN för en Windows-värd som kör Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Lägg till en åtkomstkontrollpost
Du använder den **Configuration** avsnittet i bladet för StorSimple Device Manager-tjänsten att lägga till åtkomstkontrollposter. Normalt kan kopplar du en ACR en volym.

Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-acr"></a>Att lägga till en ACR

1. Gå till StorSimple Device Manager-tjänsten, dubbelklickar du på namnet på tjänsten och sedan inom den **Configuration** klickar du på **åtkomstkontrollposter**.
2. I den **åtkomstkontrollposter** bladet klickar du på **+ Lägg till ACR**.

    ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. I den **Lägg till ACR** bladet gör följande:

    1. Ange ett namn för din ACR.
    
    2. Ange namnet på din Windows Server-värd under **iSCSI initierare namn (IQN)**.

    3. Klicka på **Lägg till** att skapa i ACR.

        ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nyligen tillagda ACR visas i listan med åtkomstkontrollposter.

    ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Redigera en åtkomstkontrollpost
Du använder den **Configuration** avsnittet i bladet för StorSimple Device Manager-tjänsten att redigera åtkomstkontrollposter.

> [!NOTE]
> Vi rekommenderar att du ändrar dessa åtkomstkontrollposter som för närvarande inte används. Om du vill redigera en ACR som är associerade med en volym som används, måste du först vidta volymen offline.

Utför följande steg om du vill redigera en ACR.

#### <a name="to-edit-an-access-control-record"></a>Så här redigerar du en åtkomstkontrollpost
1.  Gå till StorSimple Device Manager-tjänsten, dubbelklickar du på namnet på tjänsten och sedan inom den **Configuration** klickar du på **åtkomstkontrollposter**.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klicka i listan med åtkomstkontrollposterna, och välj ACR som du vill ändra.

    ![Redigera åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/editacr1.png)

3. I den **redigera åtkomstkontrollpost** bladet ger en annan IQN för en annan värd.

    ![Redigera åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klicka på **Spara**. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. 

    ![Redigera åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Du meddelas när ACR uppdateras. Tabular lista också uppdateras för att avspegla ändringen.

   
## <a name="delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost
Du använder den **Configuration** avsnittet i bladet för StorSimple Device Manager-tjänsten att ta bort åtkomstkontrollposter.

> [!NOTE]
> Du kan ta bort dessa åtkomstkontrollposter som för närvarande inte används. Om du vill ta bort en ACR som är associerade med en volym som används, måste du först vidta volymen offline.

Utför följande steg för att ta bort en åtkomstkontrollpost.

#### <a name="to-delete-an-access-control-record"></a>Att ta bort en åtkomstkontrollpost
1.  Gå till StorSimple Device Manager-tjänsten, dubbelklickar du på namnet på tjänsten och sedan inom den **Configuration** klickar du på **åtkomstkontrollposter**.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klicka i listan med åtkomstkontrollposterna, och välj ACR som du vill ta bort.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Högerklicka för att öppna snabbmenyn och välj **ta bort**.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. När du uppmanas att bekräfta granskar du informationen och klicka sedan på **ta bort**.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Du meddelas när borttagningen är klar. Listan uppdateras tagits bort.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hantera StorSimple-volymer](storsimple-8000-manage-volumes-u2.md).
* Läs mer om [med StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

