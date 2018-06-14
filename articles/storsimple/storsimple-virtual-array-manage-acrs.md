---
title: Hantera access control-poster för virtuell StorSimple-matrisen | Microsoft Docs
description: Beskriver hur du hanterar access control-poster (ACRs) för att avgöra vilka värdar som kan ansluta till en volym på den virtuella StorSimple-matrisen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875944"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Använd StorSimple Enhetshanteraren för att hantera access control-poster för virtuell StorSimple-matris

## <a name="overview"></a>Översikt

Access control-poster (ACRs) kan du ange vilka värdar som kan ansluta till en volym på den virtuella StorSimple-matrisen (kallas även den lokala virtuella enheten StorSimple). ACRs är inställd på en viss volym och innehålla iSCSI-kvalificerade namn (kvalificerade) på värdarna. När en värd försöker ansluta till en volym enhetskontroller ACR som är kopplad till volymen för IQN namn och om det finns en matchning, upprättas anslutningen. Den **Access control-poster** bladet inom den **Configuration** avsnitt i Device Manager-tjänsten visar alla access control poster med motsvarande kvalificerade av värdarna.

![Hantera access control-poster](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Den här självstudiekursen beskriver följande vanliga ACR-relaterade aktiviteter:

* Hämta IQN
* Lägg till en åtkomstkontrollpost
* Redigera en åtkomstkontrollpost
* Ta bort en åtkomstkontrollpost

> [!IMPORTANT]
> 
> * När du tilldelar en ACR till en volym, se till att volymen inte samtidigt kan kommas åt av mer än en icke-klustrad värd eftersom detta kan skada volymen.
> * När du tar bort en ACR från en volym, se till att motsvarande värden inte har åtkomst till volymen eftersom borttagningen kan resultera i en skrivskyddad avbrott.


## <a name="get-the-iqn"></a>Hämta IQN

Utför följande steg för att hämta IQN för en Windows-värd som kör Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Lägg till en ACR

Du använder **Access control-poster** bladet inom den **Configuration** avsnitt i Enhetshanteraren för StorSimple-tjänsten för att lägga till ACRs. Normalt kan du associera en ACR med en volym.

Information om hur du kopplar en ACR med en volym, gå till [lägga till en volym](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> När du tilldelar en ACR till en volym, se till att volymen inte samtidigt kan kommas åt av mer än en icke-klustrad värd eftersom detta kan skada volymen.


Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-acr"></a>Att lägga till en ACR

1. Markera din tjänst på Landningssida för tjänsten, dubbelklickar du på tjänstens namn och sedan inom den **Configuration** klickar du på **Access control-poster**.
2. I den **Access control-poster** bladet, klickar du på **Lägg till**.
3. I den **lägga till ACR** bladet gör du följande:
   
    1. Ange ett **namn** för din ACR.
    
    2. Under **iSCSI-Initierarnamn**, ange IQN namnet på din Windows-värd. Om du vill hämta IQN för Windows Server-värd, gör du följande:
   
    3. Starta Microsoft iSCSI-initieraren på din Windows-värd. I fönstret iSCSI-Initieraregenskaper på den **Configuration** , markera och kopiera strängen från den **Initierarnamn** fältet.
    Klistra in den här strängen i den **IQN** i den **lägga till ACR** bladet.
   
    6. Klicka på **Lägg till** att lägga till ACR.  
   
        ![Lägg till access control-poster](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabell listan uppdateras det här tillägget.

## <a name="edit-an-acr"></a>Redigera en ACR

Du använder den **Access control-poster** bladet inom den **Configuration** avsnitt i Enhetshanteraren tjänsten i Azure portal för att redigera ACRs.

> [!NOTE]
> Du bör inte ändra en ACR som används för närvarande. Om du vill redigera en ACR som är kopplade till en volym som används för närvarande, bör du först se volymen offline.


Utför följande steg om du vill redigera en ACR.

#### <a name="to-edit-an-acr"></a>Så här redigerar du en ACR

1. Markera din tjänst på Landningssida för tjänsten, dubbelklickar du på tjänstens namn och sedan inom den **Configuration** avsnittet **Access control-poster**.
2. I den **Access control-poster** bladet från tabular lista av access control-poster, dubbelklicka på ACR som du vill ändra.
3. I den **access control poster** bladet gör du följande:
   
    1. Ange det kvalificerade iSCSI-namnet för ACR.
   
    2. Klicka på **spara** längst upp på bladet för att spara ändrade ACR. Du ser följande bekräftelsemeddelande:
   
        ![Redigera access control-poster](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost

Du använder den **Configuration** sida i Azure portal för att ta bort ACRs.

> [!NOTE]
> 
> * Du bör inte ta bort en ACR som används för närvarande. Om du vill ta bort en ACR som är kopplade till en volym som används måste ha du först volymen offline.
> * När du tar bort en ACR från en volym, se till att motsvarande värden inte har åtkomst till volymen eftersom borttagningen kan resultera i en skrivskyddad avbrott.


Utför följande steg för att ta bort en åtkomstkontrollpost.

#### <a name="to-delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost

1. Markera din tjänst på Landningssida för tjänsten, dubbelklickar du på tjänstens namn och sedan inom den **Configuration** avsnittet **Access control-poster**.

2. I den **Access control-poster** bladet från tabular lista av access control-poster, dubbelklicka på ACR som du vill ta bort.

3. Klicka på Redigera access control poster bladet **ta bort**.
   
    ![Ta bort ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. När du uppmanas att bekräfta, klickar du på **ta bort** att ta bort. Tabell listan uppdateras borttagningen.
   
   ![Varningsmeddelande](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [lägga till volymer och konfigurera ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

