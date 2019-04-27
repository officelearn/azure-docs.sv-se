---
title: Ändra lösenordet för StorSimple Virtual Array enhetsadministratörer | Microsoft Docs
description: Beskriver hur du använder Azure portal eller StorSimple Virtual Array webbgränssnittet för att ändra enhetens administratörslösenord.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580410"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Ändra administratörslösenordet för StorSimple Virtual Array enheter via StorSimple Device Manager

## <a name="overview"></a>Översikt

När du använder Windows PowerShell-gränssnittet för att få åtkomst till StorSimple Virtual Array kan behöver du ange ett administratörslösenord för enheten. När StorSimple-enheten etablerats först och igång, standardlösenordet är *Password1*. För att skydda dina data, standardlösenordet upphör att gälla den första gången du loggar in och du måste ändra lösenordet.

Du kan också använda det lokala webbgränssnittet eller Azure-portalen för att ändra enhetens administratörslösenord när som helst efter att enheten har distribuerats i produktionsmiljön. Var och en av de här procedurerna beskrivs i den här artikeln.

 ![Enhetsbladet](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Använd Azure portal för att ändra lösenordet

Utför följande steg om du vill ändra enhetens administratörslösenord via Azure portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Ändra enhetens administratörslösenord via Azure portal

1. Välj din tjänst på landningssidan för tjänsten, dubbelklickar du på namnet på tjänsten och sedan inom den **Management** klickar du på **enheter**. Då öppnas det **enheter** blad som visar en lista över alla StorSimple Virtual Array-enheter.

2. I den **enheter** bladet dubbelklickar du på den enhet som kräver en ändring av lösenord.

3. I den **inställningar** bladet för din enhet klickar du på **Security**.

4. I den **säkerhetsinställningar** bladet gör du följande:
   
   1. Rulla ned till den **administratörslösenordet för enheten** avsnittet. Ange ett administratörslösenord som innehåller mellan 8 och 15 tecken.
   2. Bekräfta lösenordet.
   3. Klicka på **Spara** överst på bladet.

Administratörslösenord för enheten har uppdaterats. Du kan använda den här ändrade lösenord för att hantera enheten lokalt.

![Bladet med inställningar för säkerhet](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Använd det lokala webbgränssnittet för att ändra lösenordet

Utför följande steg om du vill ändra enhetens administratörslösenord via det lokala webbgränssnittet.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Ändra enhetens administratörslösenord via det lokala webbgränssnittet

1. I det lokala webbgränssnittet klickar du på **Underhåll** > **lösenordsändring** för din enhet.
   
    ![Ändra password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Ange den **nuvarande lösenord**.
3. Ange en **nytt lösenord**. Lösenordet måste vara minst 8 tecken långt. Det måste innehålla 3 av 4 av följande: versaler, gemener, siffror och specialtecken.
   
    Observera att lösenordet inte får vara samma som de senaste 24 lösenord.
4. Ange lösenordet igen för att bekräfta det.
   
    ![Ändra Lösenord2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Längst ned på sidan klickar du på **tillämpa**. Det nya lösenordet används nu. Om det inte går att genomföra ändra lösenordet, se följande fel:
   
    ![felaktigt lösenord](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    När lösenordet har uppdaterats, du får ett meddelande. Du kan sedan använda den här ändrade lösenord för att hantera enheten lokalt.


## <a name="next-steps"></a>Nästa steg
Lär dig hur du [administrera StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

