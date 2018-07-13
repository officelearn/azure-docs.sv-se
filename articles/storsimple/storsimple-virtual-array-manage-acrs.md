---
title: Hantera åtkomstkontrollposter för StorSimple Virtual Array | Microsoft Docs
description: Beskriver hur du hanterar åtkomstkontrollposter (åtkomstkontrollposter) för att avgöra vilka värdar som kan ansluta till en volym på StorSimple Virtual Array.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38718894"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Använd StorSimple Device Manager för att hantera åtkomstkontrollposter för StorSimple Virtual Array

## <a name="overview"></a>Översikt

Åtkomstkontrollposter (åtkomstkontrollposter) kan du ange vilka värdar som kan ansluta till en volym på StorSimple Virtual Array (även kallat den lokala virtuella enheten StorSimple). Åtkomstkontrollposter är inställd på en specifik volym och innehålla iSCSI-kvalificerade namn (kvalificerade) på värdarna. När en värd försöker ansluta till en volym, enheten kontrollerar ACR som är associerade med den volymen för IQN-namnet och om det finns en matchning, upprättas anslutningen. Den **åtkomstkontrollposter** bladet inom den **Configuration** avsnittet i Device Manager-tjänsten visar alla åtkomstkontrollposter med motsvarande kvalificerade värdar.

![Hantera åtkomstkontrollposter](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Den här självstudien beskrivs följande vanliga ACR-relaterade uppgifter:

* Hämta IQN
* Lägg till en åtkomstkontrollpost
* Redigera en åtkomstkontrollpost
* Ta bort en åtkomstkontrollpost

> [!IMPORTANT]
> 
> * När du tilldelar en ACR till en volym, noga att volymen inte samtidigt används av mer än en icke-klustrad värd eftersom detta kan skada volymen.
> * När du tar bort en ACR från en volym, se till att motsvarande värden inte har åtkomst till volymen eftersom borttagningen kan resultera i en skrivskyddad avbrott.


## <a name="get-the-iqn"></a>Hämta IQN

Utför följande steg för att hämta IQN för en Windows-värd som kör Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Lägg till en Åtkomstkontrollpost

Du använder **åtkomstkontrollposter** bladet inom den **Configuration** delen av din StorSimple Device Manager-tjänsten att lägga till åtkomstkontrollposter. Normalt kan associera du en ACR med en volym.

Information om hur du kopplar en ACR med en volym, går du till [Lägg till en volym](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> När du tilldelar en ACR till en volym, noga att volymen inte samtidigt används av mer än en icke-klustrad värd eftersom detta kan skada volymen.


Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-acr"></a>Att lägga till en ACR

1. Välj din tjänst på landningssidan för tjänsten, dubbelklickar du på namnet på tjänsten och sedan inom den **Configuration** klickar du på **åtkomstkontrollposter**.
2. I den **åtkomstkontrollposter** bladet klickar du på **Lägg till**.
3. I den **Lägg till ACR** bladet gör du följande:
   
    1. Ange ett **namn** för din ACR.
    
    2. Under **iSCSI-Initierarnamn**, ange IQN-namnet på din Windows-värd. Om du vill hämta IQN för Windows Server-värd, gör du följande:
   
    3. Starta Microsoft iSCSI-initieraren på din Windows-värd. I fönstret iSCSI-Initieraregenskaper på den **Configuration** fliken, markerar och kopierar strängen från den **Initierarnamn** fält.
    Klistra in den här strängen i den **IQN** i den **Lägg till ACR** bladet.
   
    6. Klicka på **Lägg till** att lägga till i ACR.  
   
        ![Lägg till åtkomstkontrollposter](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Listan uppdateras för att återspegla det här tillägget.

## <a name="edit-an-acr"></a>Redigera en ACR

Du använder den **åtkomstkontrollposter** bladet inom den **Configuration** avsnittet i Device Manager-tjänsten i Azure portal för att redigera åtkomstkontrollposter.

> [!NOTE]
> Du bör inte ändra en ACR som används för närvarande. Om du vill redigera en ACR som är associerade med en volym som används för närvarande, bör du först koppla från volymen.


Utför följande steg om du vill redigera en ACR.

#### <a name="to-edit-an-acr"></a>Så här redigerar du en ACR

1. Välj din tjänst på landningssidan för tjänsten, dubbelklickar du på namnet på tjänsten och sedan inom den **Configuration** avsnittet **åtkomstkontrollposter**.
2. I den **åtkomstkontrollposter** bladet från listan med åtkomstkontrollposter, dubbelklicka på ACR som du vill ändra.
3. I den **redigera åtkomstkontrollposter** bladet gör du följande:
   
    1. Ange det kvalificerade iSCSI-namnet för ACR.
   
    2. Klicka på **spara** överst på bladet för att spara ändrade ACR. Du ser följande bekräftelsemeddelande:
   
        ![Redigera åtkomstkontrollposter](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost

Du använder den **Configuration** sida i Azure portal för att ta bort åtkomstkontrollposter.

> [!NOTE]
> 
> * Du bör inte ta bort en ACR som används för närvarande. Om du vill ta bort en ACR som är associerade med en volym som används för närvarande måste bör du först koppla från volymen.
> * När du tar bort en ACR från en volym, se till att motsvarande värden inte har åtkomst till volymen eftersom borttagningen kan resultera i en skrivskyddad avbrott.


Utför följande steg för att ta bort en åtkomstkontrollpost.

#### <a name="to-delete-an-access-control-record"></a>Att ta bort en åtkomstkontrollpost

1. Välj din tjänst på landningssidan för tjänsten, dubbelklickar du på namnet på tjänsten och sedan inom den **Configuration** avsnittet **åtkomstkontrollposter**.

2. I den **åtkomstkontrollposter** bladet från listan med åtkomstkontrollposter, dubbelklicka på ACR som du vill ta bort.

3. I bladet redigera access control poster klickar du på **ta bort**.
   
    ![Ta bort Åtkomstkontrollposter](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. När du uppmanas att bekräfta klickar du på **ta bort** att ta bort. Listan uppdateras tagits bort.
   
   ![Varningsmeddelande](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [lägga till volymer och konfigurera åtkomstkontrollposter](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

