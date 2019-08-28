---
title: Starten av virtuella Azure-datorer har fastnat på Windows Update | Microsoft Docs
description: Lär dig hur du felsöker problemet när en Azure VM-start fastnar på Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: b9a93448e084a42a935a8281ccd069e3604b1f18
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089607"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Starten av virtuella Azure-datorer fastnar på Windows Update

Den här artikeln hjälper till att lösa problemet när den virtuella datorn (VM) fastnar i Windows Update fasen under start. 

> [!NOTE] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder distributions modellen för Resource Manager. Vi rekommenderar att du använder den här modellen för nya distributioner istället för att använda den klassiska distributions modellen.

## <a name="symptom"></a>Symtom

 En virtuell Windows-dator startar inte. När du kontrollerar skärm bilderna i fönstret [Boot Diagnostics](../troubleshooting/boot-diagnostics.md) ser du att Start programmet fastnar i uppdaterings processen. Följande är exempel på meddelanden som du kan få:

- Om du installerar Windows # #% stängs inte datorn av. Det tar en stund att starta om datorn flera gånger
- Håll datorn kvar tills det är färdigt. Installerar uppdatering # av #... 
- Det gick inte att slutföra uppdateringar som ångrar ändringar Stäng inte av datorn
- Det gick inte att konfigurera Windows-uppdateringar återställnings ändringar Stäng inte av datorn
- Fel < felkod > tillämpa uppdaterings åtgärder # # # # # av # # (\Regist...)
- Allvarligt fel < felkod > att tillämpa uppdaterings åtgärder # # # # # av # #...)


## <a name="solution"></a>Lösning

Uppdaterings processen kan ta en stund beroende på hur många uppdateringar som installeras eller återställs. Lämna den virtuella datorn i det här läget i 8 timmar. Om den virtuella datorn fortfarande är i det här läget efter den perioden startar du om den virtuella datorn från Azure Portal och ser om den kan starta normalt. Om det här steget inte fungerar kan du prova följande lösning.

### <a name="remove-the-update-that-causes-the-problem"></a>Ta bort uppdateringen som orsakar problemet

1. Ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md). 
2. [Koppla OS-disk till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md).
3. När OS-disken är ansluten till den virtuella återställnings datorn kör du **diskmgmt. msc** för att öppna disk hantering och kontrollerar att den anslutna disken är **online**. Anteckna enhets beteckningen som är kopplad till den anslutna OS-disken som innehåller mappen \Windows. Om disken är krypterad dekrypterar du disken innan du fortsätter med nästa steg i det här dokumentet.

4. Öppna en upphöjd kommando tolks instans (kör som administratör). Kör följande kommando för att hämta en lista över de uppdaterings paket som finns på den anslutna OS-disken:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Om den anslutna OS-disken till exempel är enhet F kör du följande kommando:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Öppna filen C:\temp\Patch_level.txt och Läs den sedan längst ned. Leta upp uppdateringen som **väntar** eller väntar på att avinstalleras.  Följande är ett exempel på uppdaterings status:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Ta bort uppdateringen som orsakade problemet:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Exempel: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Beroende på paketets storlek tar DISM-verktyget en stund att bearbeta avinstallationen. Normalt utförs processen inom 16 minuter.

7. [Koppla från den OS-disken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Kontrol lera sedan om problemet är löst.
