---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166424"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera uppdateringar av underhållsläge via Windows PowerShell för StorSimple

När du använder uppdateringar av Underhållsläge för StorSimple-enhet, har alla i/o-förfrågningar pausats. Tjänster, till exempel beständigt minne (NVRAM) eller klustertjänsten stoppas. Båda styrenheterna startas om när du anger eller avsluta det här läget. När du avslutar det här läget återuppta alla tjänster och är felfria. (Detta kan ta några minuter.)

> [!IMPORTANT]
> * Kontrollera att båda styrenheterna är felfria i Azure-portalen innan du anger underhållsläge. Om kontrollanten inte är felfri, [kontakta Microsoft Support](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) i nästa steg.
> * När du är i underhållsläge, måste du först uppdatera en domänkontrollant och sedan den andra styrenheten.

1. Använd PuTTY för att ansluta till seriekonsol. Följ de detaljerade instruktionerna i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Tryck på **Retur** i kommandotolken. Välj alternativ 1 **logga in med fullständig åtkomst**.

2. Om du vill placera kontrollanten i underhållsläge, skriver du:
    
    `Enter-HcsMaintenanceMode`

    Båda styrenheterna startas om i underhållsläge.

3. Installera uppdateringar av din underhållsläge. Ange:

    `Start-HcsUpdate`

    Du uppmanas att bekräfta. När du har bekräftat uppdateringarna är installerade på den styrenhet som du använder för närvarande. När uppdateringarna har installerats startar om kontrollanten.

4. Övervaka statusen för uppdateringar. Logga in till peer-styrenhet eftersom den aktuella kontrollanten uppdaterar och går inte att bearbeta andra kommandon. Ange:

    `Get-HcsUpdateStatus`

    Om den `RunInProgress` är `True`, uppdateringen pågår fortfarande. Om `RunInProgress` är `False`, betyder det att uppdateringen har slutförts.

5. När disk-uppdateringar av inbyggd programvara har tillämpats och den uppdaterade styrenheten har startats om, kontrollerar du versionen för inbyggd programvara för disk. Skriv på kontrollanten uppdaterats:

    `Get-HcsFirmwareVersion`
   
    Förväntade disk-versioner av inbyggd programvara är:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Avsluta underhållsläget. Skriv följande kommando för varje enhetsstyrenhet:

    `Exit-HcsMaintenanceMode`

    Styrenheterna startas om när du avslutar underhållsläget.

7. Gå tillbaka till Azure-portalen. Portalen kanske inte visar att du har installerat uppdateringarna i underhållsläge i 24 timmar.