---
title: Azure VM Start har fastnat på Windows Update | Microsoft Docs
description: Lär dig att felsöka problemet när en virtuell dator i Azure-Start har fastnat på Windows update.
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 7fbf157152bd5c2c35c496b1fd8e707884d0f3da
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731471"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Azure VM Start har fastnat på Windows update

Den här artikeln hjälper dig att lösa problemet när dina virtuella datorer (VM) har fastnat i Windows Update-skedet under starten. 

> [!NOTE] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver Resource Manager-distributionsmodellen. Vi rekommenderar att du använder den här modellen för nya distributioner istället för att använda den klassiska distributionsmodellen.

## <a name="symptom"></a>Symtom

 En virtuell Windows-dator startar inte. När du checkar skärmbilderna den [Startdiagnostik](../troubleshooting/boot-diagnostics.md) fönstret ser du att Start har fastnat under uppdateringen. Här följer några exempel på meddelanden som kan visas:

- Installera Windows ##% stänga inte av datorn. Detta kan ta ett tag datorn startas om flera gånger
- Håll din dator på tills detta görs. Installera uppdatering antal #... 
- Det gick inte att slutföra de uppdateringar som ångrar ändringarna inte stänga av datorn
- Fel konfigurera Windows-uppdateringar Reverting ändringar stänger inte av datorn
- Fel < error code > tillämpa uppdateringsåtgärder ### av ### (\Regist...)
- Allvarligt fel < felkod > tillämpa uppdateringsåtgärder ### av ### ($...)


## <a name="solution"></a>Lösning

Beroende på antalet uppdateringar som får installerat eller återställas säkerhetskopiering, update-processen kan ta en stund. Lämna den virtuella datorn i det här tillståndet i 8 timmar. Om den virtuella datorn är fortfarande i det här tillståndet efter denna period, starta om den virtuella datorn från Azure portal och se om den kan starta normalt. Om det här steget inte fungerar kan du prova följande lösning.

### <a name="remove-the-update-that-causes-the-problem"></a>Ta bort den uppdatering som orsakar problemet

1. Ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md). 
2. [Koppla OS-disk till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md).
3. När OS-disken är ansluten på den Virtuella återställningsdatorn, köra **diskmgmt.msc** att öppna Diskhantering och kontrollera ansluten disk är **ONLINE**. Anteckna den enhetsbeteckning som är tilldelad till den anslutna OS-disken som innehåller mappen \windows. Om disken är krypterad dekryptera disken innan du fortsätter med nästa steg i det här dokumentet.

4. Öppna en upphöjd kommandotolk-instans (Kör som administratör). Kör följande kommando för att hämta listan över uppdateringspaket som finns på den anslutna OS-disken:

        dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt

    Till exempel om den anslutna OS-disken är enhet F, kör du följande kommando:

        dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
5. Öppna filen C:\temp\Patch_level.txt och läsa det längst ned. Hitta den uppdatering som finns i **installation väntar** eller **avinstallera väntande** tillstånd.  Följande är ett exempel på uppdateringsstatus:

     ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Ta bort den uppdatering som orsakade problemet:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Exempel: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Beroende på storleken på paketet kan DISM-verktyget ta ett tag att bearbeta un-installationen. Processen kommer normalt slutföras inom 16 minuter.

7. [Koppla från den OS-disken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk). Kontrollera sedan om problemet är löst.