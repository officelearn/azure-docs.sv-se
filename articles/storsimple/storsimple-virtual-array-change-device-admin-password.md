---
title: Ändra admin-lösenord för StorSimple Virtual Array-enhet | Microsoft-dokument
description: Beskriver hur du använder webbgränssnittet Azure-portalen eller StorSimple Virtual Array för att ändra lösenordet för enhetsadministratören.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580410"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Ändra administratörslösenordet för StorSimple Virtual Array-enheten via StorSimple Enhetshanteraren

## <a name="overview"></a>Översikt

När du använder Windows PowerShell-gränssnittet för att komma åt StorSimple Virtual Array måste du ange ett lösenord för enhetsadministratören. När StorSimple-enheten först etableras och startas är standardlösenordet *Password1*. För att dina data ska bli säkerhetsasst går standardlösenordet ut första gången du loggar in och du måste ändra lösenordet.

Du kan också använda antingen det lokala webbgränssnittet eller Azure-portalen för att ändra enhetsadministratörens lösenord när som helst efter att enheten har distribuerats i din produktionsmiljö. Var och en av dessa procedurer beskrivs i den här artikeln.

 ![enheter blad](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Använda Azure-portalen för att ändra lösenordet

Utför följande steg för att ändra enhetsadministratörens lösenord via Azure-portalen.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Så här ändrar du enhetsadministratörens lösenord via Azure-portalen

1. På tjänstens målsida väljer du din tjänst, dubbelklickar på tjänstnamnet och klickar sedan på **Enheter**i avsnittet **Hantering** . Då öppnas bladet **Enheter** som listar alla Dina StorSimple Virtual Array-enheter.

2. Dubbelklicka på den enhet som kräver ett lösenordsändring i bladet **Enheter.**

3. Klicka på **Säkerhet**i bladet **Inställningar** för enheten .

4. Gör följande i bladet **Säkerhetsinställningar:**
   
   1. Bläddra ned till avsnittet **Lösenord för enhetsadministratör.** Ange ett administratörslösenord som innehåller mellan 8 och 15 tecken.
   2. Bekräfta lösenordet.
   3. Klicka på **Spara** högst upp på bladet.

Lösenordet för enhetsadministratören har nu uppdaterats. Du kan använda det här ändrade lösenordet för att komma åt enheten lokalt.

![Blad för säkerhetsinställningar](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Använda det lokala webbgränssnittet för att ändra lösenordet

Gör följande för att ändra lösenordet för enhetsadministratören via det lokala webbgränssnittet.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Så här ändrar du lösenordet för enhetsadministratören via det lokala webbgränssnittet

1. Klicka på Ändring av **underhållslösenord** > **Password change** för enheten i det lokala webbgränssnittet.
   
    ![ändra lösenord1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Ange det **aktuella lösenordet**.
3. Ange ett **nytt lösenord**. Lösenordet måste vara minst 8 tecken långt. Den skall innehålla 3 av 4 av följande: versaler, gemener, numeriska och specialtecken.
   
    Observera att ditt lösenord inte kan vara detsamma som de senaste 24 lösenorden.
4. Ange lösenordet igen för att bekräfta det.
   
    ![ändra lösenord2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Klicka på **Använd**längst ned på sidan. Det nya lösenordet tillämpas nu. Om lösenordsändringen inte lyckas visas följande fel:
   
    ![lösenordsfel](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    När lösenordet har uppdaterats meddelas du. Du kan sedan använda det här ändrade lösenordet för att komma åt enheten lokalt.


## <a name="next-steps"></a>Nästa steg
Lär dig hur du [administrerar din Virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

