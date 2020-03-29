---
title: Start av Azure VM har fastnat i Windows Update| Microsoft-dokument
description: Lär dig hur du felsöker problemet när en Start för Azure VM har fastnat i Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 8a47131cb4f19cce1664eafa50c67ab1a1171e67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919438"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Start för Azure VM har fastnat i Windows Update

Den här artikeln hjälper dig att lösa problemet när din virtuella dator (VM) har fastnat i Windows Update-skedet under start. 


## <a name="symptom"></a>Symptom

 En Virtuell Windows-dator startar inte. När du kontrollerar skärmbilderna i fönstret [Starta diagnostik](../troubleshooting/boot-diagnostics.md) ser du att starten har fastnat i uppdateringsprocessen. Följande är exempel på meddelanden som du kan få:

- Installera Windows ##% Stäng inte av datorn. Detta tar ett tag datorn startar om flera gånger
- Håll datorn på tills detta är gjort. Installera uppdatering # av #... 
- Det gick inte att slutföra uppdateringarna Ångra ändringar Stäng inte av datorn
- Det gick inte att konfigurera Windows-uppdateringar Återställa ändringar Stäng inte av datorn
- Fel < felkod > att uppdatera åtgärder ##### av ##### (\Regist...)
- Fatal Error < felkod > att tillämpa uppdateringsåtgärder ##### av ##### ($$...)


## <a name="solution"></a>Lösning

Beroende på hur många uppdateringar som installeras eller rullas upp kan uppdateringsprocessen ta en stund. Lämna den virtuella datorn i det här tillståndet i 8 timmar. Om den virtuella datorn fortfarande är i det här tillståndet efter den perioden startar du om den virtuella datorn från Azure-portalen och ser om den kan starta normalt. Om det här steget inte fungerar provar du följande lösning.

### <a name="remove-the-update-that-causes-the-problem"></a>Ta bort uppdateringen som orsakar problemet

1. Ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md). 
2. [Koppla OS-disken till en återställnings-VM](troubleshoot-recovery-disks-portal-windows.md).
3. När OS-disken är ansluten på återställningsdatorn kör **du diskmgmt.msc** för att öppna Diskhantering och se till att den anslutna disken är **ONLINE**. Anteckna enhetsbeteckningen som är tilldelad den bifogade OS-disken som innehåller mappen \windows. Om disken är krypterad dekrypterar du disken innan du fortsätter med nästa steg i det här dokumentet.

4. Öppna en upphöjd kommandotolksinstans (Kör som administratör). Kör följande kommando för att hämta listan över uppdateringspaket som finns på den bifogade OS-disken:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Om den anslutna OS-disken till exempel är enhet F kör du följande kommando:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Öppna filen C:\temp\Patch_level.txt och läs den sedan nedifrån och upp. Leta reda på uppdateringen som finns i **läget Installera väntande** eller **Avinstallera väntande** tillstånd.  Följande är ett exempel på uppdateringsstatus:

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
    > Beroende på paketets storlek tar det ett tag innan DISM-verktyget bearbetar installationen. Normalt kommer processen att slutföras inom 16 minuter.

7. [Koppla från OS-disken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Kontrollera sedan om problemet är löst.
