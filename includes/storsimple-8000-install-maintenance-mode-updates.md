---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187539"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera uppdateringar av underhållsläge via Windows PowerShell för StorSimple

När du installerar uppdateringar för underhållsläge på StorSimple-enheten pausas alla I/O-begäranden. Tjänster som icke-flyktigt slumpmässigt åtkomstminne (NVRAM) eller klustertjänsten stoppas. Båda handkontrollerna startas om när du går in i eller avslutar det här läget. När du avslutar det här läget återupptas alla tjänster och är felfria. (Detta kan ta några minuter.)

> [!IMPORTANT]
> * Innan du går in i underhållsläge kontrollerar du att båda enhetskontrollanterna är felfria i Azure-portalen. Om styrenheten inte är felfri [kontaktar du Microsoft Support](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) för nästa steg.
> * När du är i underhållsläge måste du först uppdatera den ena styrenheten och sedan den andra styrenheten.

1. Använd PuTTY för att ansluta till seriekonsolen. Följ de detaljerade instruktionerna i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Tryck på **Retur** i kommandotolken. Välj Alternativ 1, **Logga in med full åtkomst**.

2. Om du vill placera styrenheten i underhållsläge skriver du:
    
    `Enter-HcsMaintenanceMode`

    Båda styrenheterna startas om i underhållsläge.

3. Installera uppdateringar av underhållsläget. Ange:

    `Start-HcsUpdate`

    Du uppmanas att bekräfta. När du har bekräftat uppdateringarna installeras de på den styrenhet som du för närvarande använder. När uppdateringarna har installerats startas handkontrollen om.

4. Övervaka status för uppdateringar. Logga in på peer-styrenheten när den aktuella styrenheten uppdateras och kan inte bearbeta några andra kommandon. Ange:

    `Get-HcsUpdateStatus`

    Om `RunInProgress` är `True`, uppdateringen pågår fortfarande. Om `RunInProgress` `False`är , anger det att uppdateringen har slutförts.

5. När uppdateringarna av den fasta disken har installerats och den uppdaterade styrenheten har startats om kontrollerar du versionen av den inbyggda programvaran för disken. Skriv:

    `Get-HcsFirmwareVersion`
   
    De förväntade versionerna av den fasta hårddisken är:`XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Avsluta underhållsläget. Skriv följande kommando för varje enhetsstyrenhet:

    `Exit-HcsMaintenanceMode`

    Styrenheterna startas om när du avslutar underhållsläget.

7. Gå tillbaka till Azure-portalen. Portalen kanske inte visar att du har installerat uppdateringarna för underhållsläge på 24 timmar.