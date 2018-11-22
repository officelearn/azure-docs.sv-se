---
title: Felsöka ett netvsc.sys problem när du fjärransluter till en Windows 10 eller Windows Server 2016-dator i Azure | Microsoft Docs
description: Lär dig att felsöka en RDP netsvc.sys-relaterade problem när du ansluter till en Windows 10 eller Windows Server 2016-dator i Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 777d5cb9449bcf9424e2514b2b8f90a9ca6c479c
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52285452"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Det går inte att fjärransluta till en Windows 10 eller Windows Server 2016-dator i Azure på grund av netvsc.sys

Den här artikeln förklarar hur du felsöker ett problem där det inte finns någon nätverksanslutning när du ansluter till en Windows 10 eller Windows Server 2016 Datacenter virtuell dator (VM) på en Hyper-V Server 2016-värd.

## <a name="symptoms"></a>Symtom

Du kan inte ansluta till en Azure Windows 10 eller Windows Server 2016-dator med hjälp av Rmote Desktop Protocol (RDP). I [Startdiagnostik](boot-diagnostics.md), skärmen visas ett rött kryss över nätverkskort (NIC). Detta anger att den virtuella datorn inte har någon anslutning efter att operativsystemet har lästs in helt.

Vanligtvis det här problemet uppstår i Windows [skapa 14393](http://support.microsoft.com/help/4093120/) och [skapa 15063](http://support.microsoft.com/help/4015583/). Om versionen av operativsystemet är senare än dessa versioner, gäller inte den här artikeln för ditt scenario. Om du vill kontrollera vilken version av systemet, öppna en CMD-session i [funktionen åtkomst Seriekonsolen](serial-console-windows.md), och kör sedan **Ver**.

## <a name="cause"></a>Orsak

Det här problemet kan inträffa om versionen av filen installerade netvsc.sys är **10.0.14393.594** eller **10.0.15063.0**. De här versionerna av netvsc.sys kanske systemet inte interagera med Azure-plattformen.


## <a name="solution"></a>Lösning

Innan du följer dessa steg [ta en ögonblicksbild av systemdisken](../windows/snapshot-copy-managed-disk.md) på den berörda virtuella datorn som en säkerhetskopia. Felsök problemet genom att använda Seriekonsolen eller [reparera den virtuella datorn offline](#repair-the-vm-offline) genom att koppla systemdisken på den virtuella datorn till en virtuell dator för återställning.


### <a name="use-the-serial-console"></a>Använd Seriekonsolen

Ansluta till [Seriekonsolen, öppna en PowerShell-instans](serial-console-windows.md), och Följ dessa steg.

> [!NOTE]
> Om Seriekonsolen inte är aktiverad på den virtuella datorn går du till den [reparera den virtuella datorn offline](#repair-the-vm-offline) avsnittet.

1. Kör följande kommando i en PowerShell-instans för att hämta versionen av filen (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Hämta lämplig uppdatering till en ny eller befintlig datadisk som är kopplad till en aktiv virtuell dator från samma region:

   - **10.0.14393.594**: [KB4073562](http://support.microsoft.com/help/4073562) eller en senare uppdatering
   - **10.0.15063.0**: [KB4016240](http://support.microsoft.com/help/4016240) eller en senare uppdatering

3. Koppla från verktygsdisk fungerande virtuell dator och sedan ansluta den till den brutna virtuella datorn.

4. Kör följande kommando för att installera uppdateringen på den virtuella datorn:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Starta om den virtuella datorn.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn Offline

1. [Koppla systemdisken till virtuell återställningsdator](../windows/troubleshoot-recovery-disks-portal.md).

2. Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

3. Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna systemdisken.

4. Skapa en kopia av den **\Windows\System32\config** mappen i fall en återställning om ändringarna är nödvändigt.

5. Starta Registereditorn (regedit.exe) på Räddade VM.

6. Välj den **HKEY_LOCAL_MACHINE** nyckel och välj sedan **filen** > **Läs in registreringsdatafil** på menyn.

7. Leta upp filen i den **\Windows\System32\config** mapp.

8. Välj **öppna**, typ **BROKENSYSTEM** namn, expandera den **HKEY_LOCAL_MACHINE** nyckel och leta sedan upp den ytterligare nyckel som heter **BROKENSYSTEM** .

9. Gå till följande plats:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. För varje nyckel (t.ex 0000) granska de **DriverDesc** värdet som visas som **Microsoft HYPER-V-nätverkskortet**.

11. I undernyckeln undersöka den **DriverVersion** värde som är drivrutinsversion för nätverkskortet på den virtuella datorn.

12. Hämta lämplig uppdatering:

   - **10.0.14393.594**: [KB4073562](http://support.microsoft.com/help/4073562) eller en senare uppdatering
   - **10.0.15063.0**: [KB4016240](http://support.microsoft.com/help/4016240) eller en senare uppdatering

13. Koppla systemdisken som en datadisk på en Räddade virtuell dator där du kan ladda ned uppdateringen.

14. Kör följande kommando för att installera uppdateringen på den virtuella datorn:

   ```
   dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
   ```

15. Kör följande kommando för att demontera registreringsdatafilerna:

   ```
   reg unload HKLM\BROKENSYSTEM
   ```

16. [Koppla från systemdisken och skapa den virtuella datorn igen](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp, [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
