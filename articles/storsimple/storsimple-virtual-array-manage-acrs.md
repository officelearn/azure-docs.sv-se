---
title: Hantera åtkomstkontrollposter för StorSimple Virtual Array | Microsoft-dokument
description: Beskriver hur du hanterar åtkomstkontrollposter (ACRs) för att avgöra vilka värdar som kan ansluta till en volym på StorSimple Virtual Array.
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
ms.openlocfilehash: 1dfc1b0e0576402624bfe62de0e206d9bd7cd1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724427"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Använda StorSimple Device Manager för att hantera åtkomstkontrollposter för StorSimple Virtual Array

## <a name="overview"></a>Översikt

Med åtkomstkontrollposter (ACRs) kan du ange vilka värdar som kan ansluta till en volym på StorSimple Virtual Array (kallas även den lokala virtuella storsimpleenheten). AKU:er är inställda på en viss volym och innehåller iSCSI Qualified Names (IQNs) för värdarna. När en värd försöker ansluta till en volym kontrollerar enheten ACR som är associerad med den volymen för IQN-namnet, och om det finns en matchning upprättas anslutningen. **Bladet För åtkomstkontroll registrerar** bladet i avsnittet **Konfiguration** i tjänsten Enhetshanteraren visar alla åtkomstkontrollposter med motsvarande IQN för värdarna.

![Hantera åtkomstkontrollposter](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

I den här självstudien beskrivs följande vanliga ACR-relaterade uppgifter:

* Hämta IQN
* Lägga till en åtkomstkontrollpost
* Redigera en åtkomstkontrollpost
* Ta bort en åtkomstkontrollpost

> [!IMPORTANT]
> 
> * När du tilldelar en ACR till en volym, se till att volymen inte samtidigt nås av mer än en icke-klustrade värd eftersom detta kan skada volymen.
> * När du tar bort en ACR från en volym kontrollerar du att motsvarande värd inte kommer åt volymen eftersom borttagningen kan resultera i en läs-skriv-störning.


## <a name="get-the-iqn"></a>Hämta IQN

Gör följande för att hämta IQN för en Windows-värd som kör Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Lägga till en ACR

Du använder **bladet För åtkomstkontrollposter** i avsnittet **Konfiguration i** Tjänsten StorSimple Device Manager för att lägga till åtkomstkontrollistor. Vanligtvis associerar du en ACR med en volym.

Information om hur du associerar en ACR med en volym finns i lägga till [en volym](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> När du tilldelar en ACR till en volym, se till att volymen inte samtidigt nås av mer än en icke-klustrade värd eftersom detta kan skada volymen.


Utför följande steg för att lägga till en ACR.

#### <a name="to-add-an-acr"></a>Så här lägger du till en ACR

1. På tjänstens målsida väljer du din tjänst, dubbelklickar på tjänstnamnet och klickar sedan på **Åtkomstkontrollposter**i avsnittet **Konfiguration.**
2. Klicka på **Lägg till**i bladet **För åtkomstkontrollposter.**
3. Gör följande i **bladet Lägg till ACR:**
   
    1. Ange ett **namn** för din ACR.
    
    2. Ange IQN-namnet för Windows-värden under **iSCSI Initiator Name.** Så här hämtar du IQN för din Windows Server-värd:
   
    3. Starta Microsoft iSCSI-initieraren på din Windows-värd. I fönstret för iSCSI-initieraregenskaper, fliken **Konfiguration**, markerar och kopierar du strängen från fältet **Namn på initieraren**.
    Klistra in strängen i **IQN-fältet** i **bladet Lägg till ACR.**
   
    6. Klicka på **Lägg till** om du vill lägga till ACR.  
   
        ![Lägga till åtkomstkontrollposter](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabelllistan uppdateras för att återspegla det här tillägget.

## <a name="edit-an-acr"></a>Redigera en ACR

Du kan använda bladet **Åtkomstkontrollposter** i avsnittet **Konfiguration** i enhetshanteraren i Azure-portalen för att redigera åtkomstkontrollistor.

> [!NOTE]
> Du bör inte ändra en ACR som används för tillfället. Om du vill redigera en ACR som är associerad med en volym som används bör du först koppla från volymen.


Gör följande för att redigera en ACR.

#### <a name="to-edit-an-acr"></a>Så här redigerar du en ACR

1. På tjänstens målsida väljer du din tjänst, dubbelklickar på tjänstnamnet och visar sedan **behörighetsposter i**avsnittet **Konfiguration** .
2. **Dubbelklicka** på den acr som du vill ändra från tabelllistan i listan över åtkomstkontrollposter i bladet För åtkomstkontrollposter.
3. Gör följande i bladet **Redigera åtkomstkontrollposter:**
   
    1. Ange IQN för ACR.
   
    2. Klicka på **Spara** högst upp på bladet för att spara den modifierade ACR. Följande bekräftelsemeddelande visas:
   
        ![Redigera åtkomstkontrollposter](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Ta bort en åtkomstkontrollpost

Du kan använda **konfigurationssidan** i Azure-portalen för att ta bort ACRs.

> [!NOTE]
> 
> * Du bör inte ta bort en ACR som används för tillfället. Om du vill ta bort en ACR som är associerad med en volym som används bör du först koppla från volymen.
> * När du tar bort en ACR från en volym kontrollerar du att motsvarande värd inte kommer åt volymen eftersom borttagningen kan resultera i en läs-skriv-störning.


Utför följande steg för att ta bort en åtkomstkontrollpost.

#### <a name="to-delete-an-access-control-record"></a>Så här tar du bort en åtkomstkontrollpost

1. På tjänstens målsida väljer du din tjänst, dubbelklickar på tjänstnamnet och visar sedan **behörighetsposter i**avsnittet **Konfiguration** .

2. **Dubbelklicka** på den acr som du vill ta bort från tabelllistan för åtkomstkontrollposterna i bladet För åtkomstkontrollposter.

3. Klicka på **Ta bort**i bladet Redigera åtkomstkontrollposter .
   
    ![Ta bort ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. När du uppmanas att bekräfta klickar du på **Ta bort** för att fortsätta med borttagningen. Tabelllistan uppdateras för att återspegla borttagningen.
   
   ![Varningsmeddelande](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att lägga till volymer och konfigurera ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

