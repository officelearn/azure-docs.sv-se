---
title: Ändra läge för StorSimple-enhet | Microsoft Docs
description: Beskriver lägen för StorSimple-enhet och förklarar hur du använder Windows PowerShell för StorSimple för att ändra läget för enheten.
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
ms.openlocfilehash: dd160ede1189b0de544c8cf5db3b13228d212419
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875020"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Ändra läget för enheten på din StorSimple-enhet

Den här artikeln innehåller en kort beskrivning av de olika lägena för där din StorSimple-enhet fungerar. Din StorSimple-enhet kan fungera i tre lägen: normal, underhåll och återställning.

När du har läst den här artikeln, vet du:

* Vilka StorSimple enheten läget är
* Hur du ta reda på vilket läge StorSimple-enheten är i
* Så här ändrar du från normal till underhållsläge och *tvärtom*

Ovanstående hanteringsuppgifter kan endast utföras via Windows PowerShell-gränssnittet för din StorSimple-enhet.

## <a name="about-storsimple-device-modes"></a>Om lägen för StorSimple-enhet

Din StorSimple-enhet kan köras i läget normal, underhåll eller återställning. Var och en av dessa lägen kortfattat beskrivs nedan.

### <a name="normal-mode"></a>Normalt läge

Detta definieras som operativa normalläge för en helt konfigurerade StorSimple-enhet. Som standard måste enheten vara i normalläge.

### <a name="maintenance-mode"></a>Underhållsläge

StorSimple-enhet kan ibland behöva placeras i underhållsläge. Det här läget kan du utföra underhåll på enheten och installera störande uppdateringar, till exempel de som är relaterade till disk inbyggda programvaran.

Du kan försätta datorn i underhållsläge endast via Windows PowerShell för StorSimple. Alla i/o-begäranden är pausade i det här läget. Tjänster, till exempel beständiga RAM-minne (NVRAM) eller klustertjänsten stoppas. Båda domänkontrollanterna startas om när du anger eller avsluta det här läget. När du avslutar underhållsläge återupptas alla tjänster och bör vara felfritt. Det kan ta några minuter.

> [!NOTE]
> **Underhållsläge stöds bara på en korrekt fungerande enhet. Den stöds inte på en enhet där en eller båda av domänkontrollanter inte fungerar.**


### <a name="recovery-mode"></a>Återställningsläge

Återställningsläge kan beskrivas som ”Windows felsäkert läge med nätverksstöd”. Återställningsläge aktiverar Microsoft Support-teamet och gör att de kan utföra diagnostik i systemet. Det främsta målet med återställningsläge är att hämta systemloggarna.

Om datorn försätts i återställningsläge, bör du kontakta Microsoft Support för nästa steg. Mer information finns på [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Du kan inte placera enheten i återställningsläget. Om enheten är i ett dåligt tillstånd, försöker återställningsläge hämta enheten i ett tillstånd där Microsoft Support-personal undersöka den.**

## <a name="determine-storsimple-device-mode"></a>Identifiera läge för StorSimple-enhet

#### <a name="to-determine-the-current-device-mode"></a>Att fastställa det aktuella läget för enhet

1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Titta på Banderollmeddelandet i menyn för seriekonsolen för enheten. Det här meddelandet anger uttryckligen om enheten är i läget underhåll eller återställning. Enheten är i normalläge om meddelandet inte innehåller någon särskild information som rör system-läge.

## <a name="change-the-storsimple-device-mode"></a>Ändra läge för StorSimple-enhet

Du kan placera StorSimple-enhet i underhållsläge (från normalt läge) för att utföra underhåll eller installera uppdateringar för underhåll läge. Utför följande procedurer för att ange eller avsluta underhållsläget.

> [!IMPORTANT]
> Innan du anger underhållsläge, kontrollera att båda styrenheter är felfri genom att öppna den **Enhetsinställningar > maskinvara hälsa** för din enhet i Azure-portalen. Om en eller båda domänkontrollanterna inte är felfri, kan du kontakta Microsofts Support för nästa steg. Mer information finns på [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Ange underhållsläge

1. Logga in på enhetens seriekonsol genom att följa stegen i [Använd PuTTY för att ansluta till enhetens seriekonsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas, ange den **enhetens administratörslösenord**. Standardlösenordet är: `Password1`.
3. I Kommandotolken, skriver du: 
   
    `Enter-HcsMaintenanceMode`
4. Visas ett varningsmeddelande som talar om att underhållsläge ska störa alla i/o-begäranden och sever anslutningen till Azure portal och du uppmanas att bekräfta. Typen **Y** anger underhållsläge.
5. Både domänkontrollanter startas om. När omstarten är klar indikerar banderollen seriekonsolen att enheten är i underhållsläge. Ett exempel på utdata visas nedan.

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

#### <a name="to-exit-maintenance-mode"></a>Avsluta underhållsläge

1. Logga in på enhetens seriekonsol. Kontrollera från Banderollmeddelandet som enheten är i underhållsläge.
2. Skriv följande vid kommandotolken:
   
    `Exit-HcsMaintenanceMode`
3. Ett varningsmeddelande och ett meddelande visas. Typen **Y** du avsluta underhållsläget.
4. Både domänkontrollanter startas om. När omstarten är klar, anger seriekonsolen popup-meddelandet att enheten är i normalläge. Ett exempel på utdata visas nedan.

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

Lär dig hur du [tillämpa uppdateringar för normal och underhåll läge](storsimple-update-device.md) på StorSimple-enheten.

