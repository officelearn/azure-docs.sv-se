---
title: Hantera åtkomstkontrollposter i StorSimple | Microsoft-dokument
description: Beskriver hur du använder åtkomstkontrollposter (ACL) för att avgöra vilka värdar som kan ansluta till en volym på StorSimple-enheten.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64693242"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Använda Tjänsten StorSimple Manager för att hantera åtkomstkontrollposter

## <a name="overview"></a>Översikt
Med åtkomstkontrollposter (ACRs) kan du ange vilka värdar som kan ansluta till en volym på StorSimple-enheten. AKU:er är inställda på en viss volym och innehåller iSCSI Qualified Names (IQNs) för värdarna. När en värd försöker ansluta till en volym kontrollerar enheten ACR som är associerad med den volymen för IQN-namnet och om det finns en matchning upprättas anslutningen. Åtkomstkontrollposterna i **avsnittet Konfiguration** i tjänstbladet StorSimple Device Manager visar alla åtkomstkontrollposter med motsvarande IQN:er för värdarna.

I den här självstudien beskrivs följande vanliga ACR-relaterade uppgifter:

* Lägga till en åtkomstkontrollpost
* Redigera en åtkomstkontrollpost
* Ta bort en åtkomstkontrollpost

> [!IMPORTANT]
> * När du tilldelar en ACR till en volym, se till att volymen inte samtidigt nås av mer än en icke-klustrade värd eftersom detta kan skada volymen.
> * När du tar bort en ACR från en volym kontrollerar du att motsvarande värd inte kommer åt volymen eftersom borttagningen kan resultera i en läs-skriv-störning.

## <a name="get-the-iqn"></a>Hämta IQN

Gör följande för att hämta IQN för en Windows-värd som kör Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Lägga till en åtkomstkontrollpost
Du kan använda avsnittet **Konfiguration** i tjänstbladet StorSimple Enhetshanteraren för att lägga till ACL:er. Vanligtvis associerar du en ACR med en volym.

Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-acr"></a>Så här lägger du till en ACR

1. Gå till tjänsten StorSimple Device Manager, dubbelklicka på tjänstnamnet och klicka sedan på **Åtkomstkontrollposter**i avsnittet **Konfiguration** .
2. Klicka på + Lägg **till ACR**i bladet **För åtkomstkontrollposter** .

    ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Gör följande i bladet **Lägg till ACR:**

    1. Ange ett namn på acr.
    
    2. Ange IQN-namnet på Windows Server-värden under **iSCSI Initiator Name (IQN)**.

    3. Klicka på **Lägg till** om du vill skapa ACR.

        ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Den nyligen tillagda ACR visas i tabelllistan för akuskar.

    ![Klicka på Lägg till ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Redigera en åtkomstkontrollpost
Du kan använda avsnittet **Konfiguration** i tjänstbladet StorSimple Enhetshanteraren för att redigera åtkomstkontrollistor.

> [!NOTE]
> Vi rekommenderar att du bara ändrar de ACL:er som för närvarande inte används. Om du vill redigera en ACR som är associerad med en volym som används måste du först koppla från volymen.

Gör följande för att redigera en ACR.

#### <a name="to-edit-an-access-control-record"></a>Så här redigerar du en åtkomstkontrollpost
1.  Gå till tjänsten StorSimple Device Manager, dubbelklicka på tjänstnamnet och klicka sedan på **Åtkomstkontrollposter**i avsnittet **Konfiguration** .

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klicka på och välj den ACR som du vill ändra i tabelllistan för åtkomstkontrollposterna.

    ![Redigera åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Ange ett annat IQN som motsvarar en annan värd i **bladet Redigera åtkomstkontroll.**

    ![Redigera åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klicka på **Spara**. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. 

    ![Redigera åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Du meddelas när ACR uppdateras. Tabelllistan uppdateras också för att återspegla ändringen.

   
## <a name="delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost
Du kan använda avsnittet **Konfiguration** i tjänstbladet StorSimple Enhetshanteraren för att ta bort ACRs.

> [!NOTE]
> Du kan bara ta bort de ACL:er som för närvarande inte används. Om du vill ta bort en ACR som är associerad med en volym som används måste du först koppla från volymen.

Utför följande steg för att ta bort en åtkomstkontrollpost.

#### <a name="to-delete-an-access-control-record"></a>Så här tar du bort en åtkomstkontrollpost
1.  Gå till tjänsten StorSimple Device Manager, dubbelklicka på tjänstnamnet och klicka sedan på **Åtkomstkontrollposter**i avsnittet **Konfiguration** .

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Klicka på och välj den ACR som du vill ta bort i tabelllistan för åtkomstkontrollposterna.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Högerklicka om du vill anropa snabbmenyn och välj **Ta bort**.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. När du uppmanas att bekräfta, granska informationen och klicka sedan på **Ta bort**.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Du meddelas när borttagningen är klar. Tabelllistan uppdateras för att återspegla borttagningen.

    ![Gå till åtkomstkontrollposter](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [hanterar StorSimple-volymer](storsimple-8000-manage-volumes-u2.md).
* Läs mer om [hur du använder StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

