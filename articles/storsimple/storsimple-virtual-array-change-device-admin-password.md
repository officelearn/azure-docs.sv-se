---
title: "Ändra virtuella StorSimple-matris enhetslösenord admin | Microsoft Docs"
description: "Beskriver hur du använder Azure-portalen eller virtuella StorSimple-matris webbgränssnittet för att ändra enhetens administratörslösenord."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 260a23003d705e6598da8c51bb5a96f2539a0014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Ändra virtuella StorSimple-matris enhetens administratörslösenord via StorSimple Enhetshanteraren

## <a name="overview"></a>Översikt

När du använder Windows PowerShell-gränssnittet för att få åtkomst till den virtuella StorSimple-matris, måste du ange ett administratörslösenord för enheten. När StorSimple-enheten först etablerats och igång, standardlösenordet är *Password1*. För att skydda dina data, standardlösenordet upphör att gälla för första gången du loggar in och du måste ändra lösenordet.

Du kan också använda lokala webbgränssnittet eller Azure-portalen för att ändra enhetens administratörslösenord när som helst efter att enheten har distribuerats i produktionsmiljön. Var och en av de här procedurerna beskrivs i den här artikeln.

 ![Bladet för enheter](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Använda Azure portal för att ändra lösenordet

Utför följande steg om du vill ändra enhetens administratörslösenord via Azure-portalen.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Ändra enhetens administratörslösenord via Azure portal

1. Markera din tjänst på Landningssida för tjänsten, dubbelklickar du på tjänstens namn och sedan inom den **Management** klickar du på **enheter**. Då öppnas den **enheter** blad som visar en lista över alla virtuella StorSimple-matris-enheter.

2. I den **enheter** bladet dubbelklickar du på enheten som kräver en ändring av lösenord.

3. I den **inställningar** bladet för din enhet klickar du på **säkerhet**.

4. I den **säkerhetsinställningar** bladet gör du följande:
   
   1. Rulla ned till den **administratörslösenordet för enheten** avsnitt. Ange ett administratörslösenord som innehåller mellan 8 och 15 tecken.
   2. Bekräfta lösenordet.
   3. Klicka på **spara** längst upp på bladet.

Administratörslösenord för enheten har uppdaterats. Du kan använda den här ändrade lösenord för att komma åt enheten lokalt.

![Säkerhet inställningsbladet](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Använda lokala webbgränssnittet för att ändra lösenordet

Utför följande steg om du vill ändra enhetens administratörslösenord via lokala webbgränssnittet.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Ändra enhetens administratörslösenord via lokala webbgränssnittet

1. Klicka på det lokala webbgränssnittet **Underhåll** > **lösenordsändring** för din enhet.
   
    ![Ändra password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Ange den **nuvarande lösenord**.
3. Ange en **nytt lösenord**. Lösenordet måste vara minst 8 tecken. Det måste innehålla 3 av 4 av följande: versaler, gemener, siffror och särskilda tecken.
   
    Observera att lösenordet inte får vara samma som de senaste 24 lösenord.
4. Ange lösenordet för att bekräfta det. igen.
   
    ![Ändra Lösenord2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Längst ned på sidan klickar du på **tillämpa**. Det nya lösenordet används nu. Om det inte lyckas ändra lösenordet, se följande fel:
   
    ![felaktigt lösenord](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    När lösenordet har uppdaterats, visas ett meddelande. Du kan sedan använda detta ändrade lösenord för att komma åt enheten lokalt.


## <a name="next-steps"></a>Nästa steg
Lär dig hur du [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

