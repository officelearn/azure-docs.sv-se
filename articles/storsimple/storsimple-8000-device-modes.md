---
title: Ändra StorSimple-enhetsläget | Microsoft Docs
description: Beskrivs lägen för StorSimple-enheten och hur du använder Windows PowerShell för StorSimple för att ändra Enhetsläge.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576099"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Ändra Enhetsläge på StorSimple-enheten

Den här artikeln innehåller en kort beskrivning av de olika lägena för där din StorSimple-enhet kan köras. Din StorSimple-enhet kan fungera i tre lägen: normal, underhåll och återställning.

När du har läst den här artikeln, vet du:

* Vilka de StorSimple-enhet-lägena är
* Så här att ta reda på vilket läge StorSimple-enheten är i
* Så här ändrar du från normal till underhållsläge och *tvärtom*

Ovanstående hanteringsuppgifter kan endast utföras via Windows PowerShell-gränssnittet för StorSimple-enheten.

## <a name="about-storsimple-device-modes"></a>Om lägen för StorSimple-enhet

Din StorSimple-enhet kan fungera i läget för normal, underhåll eller återställning. Var och en av dessa lägen beskrivs kortfattat nedan.

### <a name="normal-mode"></a>Normalt läge

Detta definieras som det normala arbetsläget för en fullständigt konfigurerad StorSimple-enhet. Som standard ska enheten vara i normalläge.

### <a name="maintenance-mode"></a>Underhållsläge

StorSimple-enhet kan ibland behöva placeras i underhållsläge. Det här läget kan du utföra underhåll på enheten och installera störande uppdateringar, till exempel de som är relaterade till inbyggda programvaran för disken.

Du kan placera systemet i underhållsläge endast via Windows PowerShell för StorSimple. Alla i/o-förfrågningar har pausats i det här läget. Tjänster, till exempel beständigt minne (NVRAM) eller klustertjänsten stoppas. Båda styrenheterna startas om när du anger eller avsluta det här läget. När du avslutar underhållsläget alla tjänster kommer att återupptas och bör vara felfritt. Det kan ta några minuter.

> [!NOTE]
> **Underhållsläge stöds bara på en korrekt fungerande enhet. Det stöds inte på en enhet där en eller båda kontrollenheterna inte fungerar.**


### <a name="recovery-mode"></a>Återställningsläge

Återställningsläge kan beskrivas som ”Windows felsäkert läge med nätverksstöd”. Återställningsläge talar med Microsoft Support-teamet och låter dem att utföra diagnostik på systemet. Huvudsyftet med återställningsläge är att hämta systemloggar.

Om datorn hamnar i återställningsläge, kontaktar du Microsoft Support angående nästa steg. Mer information går du till [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Du kan inte placera enheten i återställningsläge. Om enheten är i ett felaktigt tillstånd, försöker återställningsläge få enheten i ett tillstånd där Microsoft Support personal undersöka den.**

## <a name="determine-storsimple-device-mode"></a>Fastställa StorSimple-enhetsläget

#### <a name="to-determine-the-current-device-mode"></a>Att fastställa det aktuella läget för enhet

1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Titta på Banderollmeddelandet i menyn för seriekonsolen för enheten. Det här meddelandet anger uttryckligen om enheten är i läget för underhåll eller återställning. Om meddelandet inte innehåller någon särskild information som rör system-läge, är enheten i normalläge.

## <a name="change-the-storsimple-device-mode"></a>Ändra StorSimple-enhetsläget

Du kan placera StorSimple-enheten i underhållsläge (från normalt läge) för att utföra underhåll eller installera uppdateringar av underhållsläge. Utför följande procedurer för att ange eller avsluta underhållsläget.

> [!IMPORTANT]
> Innan du anger underhållsläge, kontrollera att båda styrenheterna är felfria genom att öppna den **Enhetsinställningar > hälsotillstånd för maskinvara** för din enhet i Azure-portalen. Kontakta Microsoft Support för nästa steg om en eller båda styrenheterna inte är felfria. Mer information går du till [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Ange underhållsläge

1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas, anger den **enhetens administratörslösenord**. Standardlösenordet är: `Password1`.
3. I Kommandotolken, skriver du: 
   
    `Enter-HcsMaintenanceMode`
4. Du ser ett varningsmeddelande om att underhållsläge ska störa alla i/o-begäranden och Server anslutningen till Azure-portalen och du uppmanas att bekräfta. Typ **Y** att ange underhållsläget.
5. Båda styrenheterna startas om. När omstarten är klar, anger seriekonsolen popup-meddelandet att enheten är i underhållsläge. Ett exempel på utdata visas nedan.

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

#### <a name="to-exit-maintenance-mode"></a>Avsluta underhållsläget

1. Logga in på enhetens seriekonsol. Kontrollera från Banderollmeddelandet som din enhet är i underhållsläge.
2. Skriv följande i kommandotolken:
   
    `Exit-HcsMaintenanceMode`
3. Ett varningsmeddelande och ett bekräftelsemeddelande visas. Typ **Y** avsluta underhållsläget.
4. Båda styrenheterna startas om. När omstarten är klar, anger seriekonsolen popup-meddelandet att enheten är i normalläge. Ett exempel på utdata visas nedan.

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

Lär dig hur du [tillämpa uppdateringar av normal och underhåll](storsimple-update-device.md) på StorSimple-enheten.

