---
title: Ändra dina StorSimple-lösenord | Microsoft-dokument
description: Beskriver hur du använder Tjänsten StorSimple Device Manager för att ändra lösenorden för StorSimple Snapshot Manager och enhetsadministratör.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ab874bbdcd47a4bfa9abfba721afa46d0f23a338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268033"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Använda Tjänsten StorSimple Device Manager för att ändra dina StorSimple-lösenord

## <a name="overview"></a>Översikt
Alternativet Azure portal **Device-inställningar** innehåller alla enhetsparametrar som du kan konfigurera om på en StorSimple-enhet som hanteras av en StorSimple Enhetshanteraren-tjänst. I den här självstudien beskrivs hur du kan använda alternativet **Säkerhet** under **Enhetsinställningar för** att ändra enhetsadministratören eller Lösenordet för StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Ändra enhetens administratörslösenord
När du använder Windows PowerShell-gränssnittet för att komma åt StorSimple-enheten måste du ange ett lösenord för enhetsadministratören. När den första StorSimple-enheten är registrerad med en tjänst är standardlösenordet för det här gränssnittet *Password1*. För att dina data ska bli säkerhetsskäl måste du ändra lösenordet i slutet av registreringsprocessen. Du kan inte avsluta registreringsprocessen utan att ändra lösenordet. Mer information finns i [Steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Lösenordet som först angavs via Windows PowerShell-gränssnittet under registreringen kan ändras senare via Azure-portalen. Gör följande för att ändra lösenordet för enhetsadministratören.

#### <a name="to-change-the-device-administrator-password"></a>Så här ändrar du lösenordet för enhetsadministratören
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**.

2. Välj och klicka på den enhet vars lösenord du tänker ändra i tabelllistan för enheter.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Gå till **Enhetsinställningar > säkerhet i**bladet **Inställningar** .

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. I bladet **Säkerhetsinställningar** klickar du på **Lösenord** för att ändra lösenordet för enhetsadministratören.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Ange ett administratörslösenord som innehåller mellan 8 och 15 tecken i bladet **Lösenord.** Lösenordet måste vara en kombination av 3 eller fler versaler, gemener, numeriska och specialtecken.

6. Bekräfta lösenordet.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klicka på **Spara** och klicka på **Ja**när du uppmanas att bekräfta.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Lösenordet för enhetsadministratören bör nu uppdateras. Du kan använda det här ändrade lösenordet för att komma åt Windows PowerShell-gränssnittet.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Ange lösenordet för StorSimple Snapshot Manager
Programvaran StorSimple Manager finns på din Windows-värd och ger administratörer möjlighet att hantera säkerhetskopiering av din StorSimple-enhet i form av lokala och molnbaserade ögonblicksbilder.

När du konfigurerar en enhet i StorSimple Snapshot Manager uppmanas du att ange enhetens IP-adress och lösenord för att autentisera lagringsenheten.

Du kan ange eller ändra lösenordet för StorSimple Snapshot Manager via Azure-portalen. Utför följande steg för att ange eller ändra StorSimple Snapshot Manager-lösenordet.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Så här anger du storsimple-snapshothanterarens lösenord
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**.

2. Välj och klicka på den enhet vars StorSimple Snapshot Manager-lösenord som du tänker ange eller ändra i tabelllistan för enheter.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Gå till **Enhetsinställningar > säkerhet i**bladet **Inställningar** .

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. I bladet **Säkerhetsinställningar** klickar du på **Lösenord** för att ange eller ändra StorSimple Snapshot Manager-lösenordet.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Ange ett lösenord som är 14 eller 15 tecken i bladet **Lösenord.** Kontrollera att lösenordet innehåller en kombination av 3 eller fler versaler, gemener, numeriska och specialtecken.

6. Bekräfta lösenordet.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klicka på **Spara** och klicka på **Ja**när du uppmanas att bekräfta.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

StorSimple Snapshot Manager-lösenordet bör nu uppdateras.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Läs mer om [hur du ändrar enhetskonfigurationen](storsimple-8000-modify-device-config.md).
* Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

