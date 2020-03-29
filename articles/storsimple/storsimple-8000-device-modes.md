---
title: Ändra Läge för StorSimple-enhet | Microsoft-dokument
description: Beskriver lägena för StorSimple-enheten och förklarar hur du använder Windows PowerShell för StorSimple för att ändra enhetsläget.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: e55964beff48df6ce24d99c01975d39b662f1612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60576099"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Ändra enhetsläget på StorSimple-enheten

Den här artikeln innehåller en kort beskrivning av de olika lägen där Din StorSimple-enhet kan fungera. Din StorSimple-enhet kan fungera i tre lägen: normalt, underhåll och återställning.

Efter att ha läst den här artikeln, vet du:

* Vad StorSimple-enhetslägena är
* Ta reda på vilket läge StorSimple-enheten är i
* Hur man ändrar från normalt till underhållsläge och *vice versa*

Ovanstående hanteringsuppgifter kan endast utföras via Windows PowerShell-gränssnittet på din StorSimple-enhet.

## <a name="about-storsimple-device-modes"></a>Om StorSimple-enhetslägen

Din StorSimple-enhet kan fungera i normalt, underhålls- eller återställningsläge. Vart och ett av dessa lägen beskrivs kortfattat nedan.

### <a name="normal-mode"></a>Normalt läge

Detta definieras som det normala driftläget för en fullständigt konfigurerad StorSimple-enhet. Som standard bör enheten vara i normalt läge.

### <a name="maintenance-mode"></a>Underhållsläge

Ibland kan StorSimple-enheten behöva placeras i underhållsläge. I det här läget kan du utföra underhåll på enheten och installera störande uppdateringar, till exempel de som är relaterade till den inbyggda programvaran för disk.

Du kan endast placera systemet i underhållsläge via Windows PowerShell för StorSimple. Alla I/O-begäranden pausas i det här läget. Tjänster som icke-flyktigt slumpmässigt åtkomstminne (NVRAM) eller klustertjänsten stoppas också. Båda styrenheterna startas om när du går in i eller avslutar det här läget. När du avslutar underhållsläget återupptas alla tjänster och bör vara felfria. Det kan ta några minuter.

> [!NOTE]
> **Underhållsläge stöds endast på en väl fungerande enhet. Det stöds inte på en enhet där en eller båda av styrenheterna inte fungerar.**


### <a name="recovery-mode"></a>Återställningsläge

Återställningsläge kan beskrivas som "Felsäkert läge i Windows med nätverksstöd". Återställningsläget engagerar Microsoft Support-teamet och gör det möjligt för dem att utföra diagnostik på systemet. Det primära målet med återställningsläget är att hämta systemloggarna.

Om systemet går in i återställningsläge bör du kontakta Microsoft Support för nästa steg. Mer information finns i [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Du kan inte placera enheten i återställningsläge. Om enheten är i dåligt tillstånd försöker återställningsläget att få enheten i ett tillstånd där Microsoft Support-personal kan undersöka den.**

## <a name="determine-storsimple-device-mode"></a>Identifiera StorSimple-enhetsläget

#### <a name="to-determine-the-current-device-mode"></a>Så här tar du reda på det aktuella enhetsläget

1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Titta på banderollmeddelandet i enhetens seriella konsolmeny. Det här meddelandet anger uttryckligen om enheten är i underhålls- eller återställningsläge. Om meddelandet inte innehåller någon specifik information om systemläget är enheten i normalt läge.

## <a name="change-the-storsimple-device-mode"></a>Ändra StorSimple-enhetsläge

Du kan placera StorSimple-enheten i underhållsläge (från normalt läge) för att utföra uppdateringar av underhålls- eller installationsunderhållsläge. Utför följande procedurer för att gå in i eller avsluta underhållsläge.

> [!IMPORTANT]
> Innan du går in i underhållsläge kontrollerar du att båda enhetskontrollanterna är felfria genom att komma åt **enhetsinställningarna > maskinvaruhälsan** för enheten i Azure-portalen. Om en eller båda styrenheterna inte är felfria kontaktar du Microsoft Support för nästa steg. Mer information finns i [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Så här går du in i underhållsläge

1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I den seriella konsolmenyn väljer du alternativ 1, **Logga in med full åtkomst**. Ange **lösenordet för enhetsadministratören**när du uppmanas att göra det. Standardlösenordet `Password1`är: .
3. Skriv i kommandotolken 
   
    `Enter-HcsMaintenanceMode`
4. Du kommer att se ett varningsmeddelande som talar om att underhållsläget kommer att störa alla I/O-begäranden och bryta anslutningen till Azure-portalen, och du kommer att uppmanas att bekräfta. Skriv **Y** för att gå in i underhållsläge.
5. Båda styrenheterna startas om. När omstarten är klar anger banderollen för seriell konsol att enheten är i underhållsläge. Ett exempel på utdata visas nedan.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Så här avslutar du underhållsläget

1. Logga in på enhetens seriekonsol. Kontrollera från bannermeddelandet att enheten är i underhållsläge.
2. Skriv följande i kommandotolken:
   
    `Exit-HcsMaintenanceMode`
3. Ett varningsmeddelande och ett bekräftelsemeddelande visas. Skriv **Y** för att avsluta underhållsläget.
4. Båda styrenheterna startas om. När omstarten är klar anger banderollen för seriell konsol att enheten är i normalt läge. Ett exempel på utdata visas nedan.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [installerar uppdateringar i normalt och underhållsläge](storsimple-update-device.md) på Din StorSimple-enhet.

