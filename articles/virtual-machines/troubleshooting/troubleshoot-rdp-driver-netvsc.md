---
title: Felsöka ett netvsc. sys-problem när du ansluter via fjärr anslutning till en virtuell Windows 10-eller Windows Server 2016-dator i Azure | Microsoft Docs
description: Lär dig hur du felsöker ett netsvc. sys-relaterat RDP-problem när du ansluter till en virtuell Windows 10-eller Windows Server 2016-dator i Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057991"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Det går inte att fjärrans luta till en virtuell Windows 10-eller Windows Server 2016-dator i Azure på grund av netvsc. sys

Den här artikeln förklarar hur du felsöker ett problem där det inte finns någon nätverks anslutning när du ansluter till en virtuell dator med Windows 10 eller Windows Server 2016 Data Center (VM) på en Hyper-V Server 2016-värd.

## <a name="symptoms"></a>Symtom

Det går inte att ansluta till en virtuell Azure Windows 10-eller Windows Server 2016-dator med hjälp av Remote Desktop Protocol (RDP). I [startdiagnostik](boot-diagnostics.md)visar skärmen ett rött kors över nätverkskortet (NIC). Detta anger att den virtuella datorn inte har någon anslutning efter att operativ systemet har lästs in helt.

Det här problemet uppstår vanligt vis i Windows [version 14393](https://support.microsoft.com/help/4093120/) och [build 15063](https://support.microsoft.com/help/4015583/). Om versionen av operativ systemet är senare än de här versionerna gäller inte den här artikeln för ditt scenario. Om du vill kontrol lera systemets version öppnar du en CMD-session i [funktionen för seriell åtkomst konsol](serial-console-windows.md)och kör sedan **ver**.

## <a name="cause"></a>Orsak

Det här problemet kan inträffa om versionen av den installerade netvsc. sys-systemfilen är **10.0.14393.594** eller **10.0.15063.0**. Dessa versioner av netvsc. sys kan hindra systemet från att interagera med Azure-plattformen.


## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du [ta en ögonblicks bild av system disken](../windows/snapshot-copy-managed-disk.md) för den berörda virtuella datorn som en säkerhets kopia. Felsök problemet genom att använda Seriekonsolen eller [reparera den virtuella datorn offline](#repair-the-vm-offline) genom att koppla systemdisken på den virtuella datorn till en virtuell dator för återställning.


### <a name="use-the-serial-console"></a>Använda serie konsolen

Anslut till [serie konsolen, öppna en PowerShell-instans](serial-console-windows.md)och följ sedan de här stegen.

> [!NOTE]
> Om Seriekonsolen inte är aktiverad på den virtuella datorn går du till den [reparera den virtuella datorn offline](#repair-the-vm-offline) avsnittet.

1. Kör följande kommando i en PowerShell-instans för att hämta versionen av filen (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Hämta lämplig uppdatering till en ny eller befintlig datadisk som är ansluten till en fungerande virtuell dator från samma region:

   - **10.0.14393.594**: [KB4073562 eller en senare](https://support.microsoft.com/help/4073562) uppdatering
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) eller en senare uppdatering

3. Koppla bort verktygs disken från den virtuella datorn och koppla den sedan till den brutna virtuella datorn.

4. Kör följande kommando för att installera uppdateringen på den virtuella datorn:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Starta om den virtuella datorn.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn Offline

1. [Koppla systemdisken till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).

2. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

3. Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna systemdisken.

4. Skapa en kopia av mappen **\Windows\System32\config** om en återställning av ändringarna är nödvändig.

5. Starta Registereditorn (regedit. exe) på den rädda virtuella datorn.

6. Välj nyckeln **HKEY_LOCAL_MACHINE** och välj sedan **fil** > **läsnings registrerings data** fil på menyn.

7. Leta upp SYSTEM filen i mappen **\Windows\System32\config**

8. Välj **Öppna**, Skriv **BROKENSYSTEM** som namn, expandera nyckeln **HKEY_LOCAL_MACHINE** och leta sedan upp den ytterligare nyckel som heter **BROKENSYSTEM**.

9. Gå till följande plats:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. I varje under nyckel (till exempel 0000) undersöker du det **DriverDesc** -värde som visas som **Microsoft Hyper-V-nätverkskort**.

11. I under nyckeln undersöker du **DriverVersion** -värdet som är driv rutins versionen av nätverkskortet för den virtuella datorn.

12. Hämta lämplig uppdatering:

    - **10.0.14393.594**: [KB4073562 eller en senare](https://support.microsoft.com/help/4073562) uppdatering
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) eller en senare uppdatering

13. Anslut system disken som en datadisk på en räddnings dator där du kan hämta uppdateringen.

14. Kör följande kommando för att installera uppdateringen på den virtuella datorn:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Kör följande kommando för att demontera Hive:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Koppla från system disken och skapa den virtuella datorn igen](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.
