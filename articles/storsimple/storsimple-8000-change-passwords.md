---
title: "Ändra din StorSimple-lösenord | Microsoft Docs"
description: "Beskriver hur du använder tjänsten StorSimple Enhetshanteraren ändra administratörslösenordet StorSimple Snapshot Manager och enhet."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att ändra din StorSimple-lösenord

## <a name="overview"></a>Översikt
Azure-portalen **Enhetsinställningar** alternativet innehåller alla enheter-parametrar som du kan konfigurera om på en StorSimple-enhet som hanteras av en tjänst för StorSimple Enhetshanteraren. Den här självstudiekursen beskrivs hur du kan använda den **säkerhet** alternativ **Enhetsinställningar** ändra enhetsadministratören eller lösenordet för StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Ändra enhetens administratörslösenord
När du använder Windows PowerShell-gränssnittet för att komma åt StorSimple-enhet, måste du ange ett administratörslösenord för enheten. När den första StorSimple-enheten har registrerats med en tjänst standardlösenordet för det här gränssnittet är *Password1*. För att skydda dina data måste du ändra lösenordet i slutet av registreringsprocessen. Du kan inte avsluta registreringen utan att ändra lösenordet. Mer information finns i [steg3: konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Lösenordet som angavs först via Windows PowerShell-gränssnittet under registreringen kan ändras senare via Azure portal. Utför följande steg om du vill ändra enhetens administratörslösenord.

#### <a name="to-change-the-device-administrator-password"></a>Ändra enhetens administratörslösenord
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**.

2. Välj tabular lista över enheter, och klicka på den enhet vars lösenord du vill ändra.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. I den **inställningar** gå till bladet **Enhetsinställningar > säkerhet**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. I den **säkerhetsinställningar** bladet, klickar du på **lösenord** ändra enhetens administratörslösenord.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. I den **lösenord** bladet, ange ett administratörslösenord som innehåller mellan 8 och 15 tecken. Lösenordet måste vara en kombination av 3 eller flera av versaler, gemener, siffror och särskilda tecken.

6. Bekräfta lösenordet.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klicka på **spara** och när du uppmanas att bekräfta på **Ja**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Administratörslösenord för enheten ska nu uppdateras. Du kan använda den här ändrade lösenord för att komma åt Windows PowerShell-gränssnittet.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Ange lösenordet för StorSimple Snapshot Manager
Programvaran StorSimple Manager finns på din Windows-värd och ger administratörer möjlighet att hantera säkerhetskopiering av din StorSimple-enhet i form av lokala och molnbaserade ögonblicksbilder.

När du konfigurerar en enhet i StorSimple Snapshot Manager, uppmanas du att ange enhetens IP-adress och lösenord för att autentisera lagringsenheten.

Du kan ange eller ändra lösenordet för StorSimple Snapshot Manager via Azure portal. Utför följande steg om du vill ange eller ändra lösenordet för StorSimple Snapshot Manager.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Ange lösenordet för StorSimple Snapshot Manager
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**.

2. Välj tabular lista över enheter, och klicka på den enhet vars lösenord för StorSimple Snapshot Manager som du vill ange eller ändra.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. I den **inställningar** gå till bladet **Enhetsinställningar > säkerhet**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. I den **säkerhetsinställningar** bladet, klickar du på **lösenord** du anger eller ändrar lösenordet för StorSimple Snapshot Manager.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. I den **lösenord** bladet, ange ett lösenord som är 14 eller 15 tecken. Kontrollera att lösenordet innehåller en kombination av 3 eller flera av versaler, gemener, siffror och särskilda tecken.

6. Bekräfta lösenordet.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klicka på **spara** och när du uppmanas att bekräfta på **Ja**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Lösenordet för StorSimple Snapshot Manager ska nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Lär dig mer om [ändra din enhetskonfiguration](storsimple-8000-modify-device-config.md).
* Lär dig mer om [använder Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

