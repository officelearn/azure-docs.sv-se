---
title: Felsöka ett netvsc.sys-problem när du fjärransluter till en virtuell dator med Windows 10 eller Windows Server 2016 i Azure | Microsoft-dokument
description: Lär dig hur du felsöker ett netsvc.sys-relaterat RDP-problem när du ansluter till en virtuell dator med Windows 10 eller Windows Server 2016 i Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057991"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Det går inte att fjärransluta till en virtuell dator med Windows 10 eller Windows Server 2016 i Azure på grund av netvsc.sys

I den här artikeln beskrivs hur du felsöker ett problem där det inte finns någon nätverksanslutning när du ansluter till en virtuell dator med Windows 10 eller Windows Server 2016 Datacenter (VM) på en Hyper-V Server 2016-värd.

## <a name="symptoms"></a>Symtom

Du kan inte ansluta till en virtuell azure Windows 10- eller Windows Server 2016-dator med hjälp av RDP (Remote Desktop Protocol). I [Boot diagnostics](boot-diagnostics.md)visar skärmen ett rött kors över nätverkskortet (NIC). Detta indikerar att den virtuella datorn inte har någon anslutning när operativsystemet är fullständigt inläst.

Det här problemet uppstår vanligtvis i Windows [build 14393](https://support.microsoft.com/help/4093120/) och [build 15063](https://support.microsoft.com/help/4015583/). Om operativsystemets version är senare än dessa versioner gäller inte den här artikeln för ditt scenario. Om du vill kontrollera versionen av systemet öppnar du en CMD-session i [funktionen Serial Access Console](serial-console-windows.md)och kör sedan **Ver**.

## <a name="cause"></a>Orsak

Det hÃ¤r problemet kan uppstÃ¥ om den installerade systemfilen netvsc.sys **10.0.14393.594** eller **10.0.15063.0**. Dessa versioner av netvsc.sys kan hindra systemet från att interagera med Azure-plattformen.


## <a name="solution"></a>Lösning

Innan du följer dessa steg [ska du ta en ögonblicksbild av systemdisken](../windows/snapshot-copy-managed-disk.md) för den berörda virtuella datorn som en säkerhetskopia. Om du vill felsöka problemet använder du seriekonsolen eller [reparerar den virtuella datorn offline](#repair-the-vm-offline) genom att koppla systemdisken på den virtuella datorn till en återställnings-VM.


### <a name="use-the-serial-console"></a>Använda seriekonsolen

Anslut till [seriekonsolen, öppna en PowerShell-instans](serial-console-windows.md)och följ sedan dessa steg.

> [!NOTE]
> Om seriekonsolen inte är aktiverad på den virtuella datorn går du till [avsnittet reparera den virtuella datorn offline.](#repair-the-vm-offline)

1. Kör följande kommando i en PowerShell-instans för att hämta versionen av filen **(c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Hämta lämplig uppdatering till en ny eller befintlig datadisk som är kopplad till en fungerande virtuell dator från samma region:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) eller en senare uppdatering
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) eller en senare uppdatering

3. Koppla bort verktygsdisken från den fungerande virtuella datorn och anslut den sedan till den trasiga virtuella datorn.

4. Kör följande kommando för att installera uppdateringen på den virtuella datorn:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Starta om den virtuella datorn.

### <a name="repair-the-vm-offline"></a>Reparera den virtuella datorn offline

1. [Koppla systemdisken till en återställnings-VM](../windows/troubleshoot-recovery-disks-portal.md).

2. Starta en anslutning till återställningsdatorn till återställningsdatorn.

3. Kontrollera att disken är flaggad som **online** i konsolen Diskhantering. Observera enhetsbeteckningen som har tilldelats den bifogade systemdisken.

4. Skapa en kopia av mappen **\Windows\System32\config** om det behövs en återställning av ändringarna.

5. Starta Registereditorn (regedit.exe) på den virtuella räddningsdatorn).

6. Markera **HKEY_LOCAL_MACHINE** och välj sedan > **Filinläsningsdata från** menyn. **File**

7. Leta upp SYSTEM-filen i mappen **\Windows\System32\config.**

8. Välj **Öppna,** skriv **BROKENSYSTEM** för namnet, expandera **HKEY_LOCAL_MACHINE** och leta sedan upp den ytterligare nyckeln med namnet **BROKENSYSTEM**.

9. Gå till följande plats:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. I varje undernyckel (till exempel 0000) undersöker du det **DriverDesc-värde** som visas som **Microsoft HYPER-V-nätverkskort**.

11. I undernyckeln undersöker du **drivrutinsversionen** som är drivrutinsversionen av nätverkskortet för den virtuella datorn.

12. Ladda ner lämplig uppdatering:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) eller en senare uppdatering
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) eller en senare uppdatering

13. Bifoga systemdisken som en datadisk på en räddnings-VM där du kan hämta uppdateringen.

14. Kör följande kommando för att installera uppdateringen på den virtuella datorn:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Kör följande kommando för att avmontera bikuporna:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Koppla från systemdisken och skapa den virtuella datorn igen](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp [kontaktar du Azure Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.
