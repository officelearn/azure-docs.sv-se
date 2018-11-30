---
title: Azure VM gäst-OS-brandväggen blockerar inkommande trafik | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 3ddd2f122de832654be295c5978a88bec702558c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319437"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM gäst-OS-brandväggen blockerar inkommande trafik

Den här artikeln beskriver hur du löser problemet Portal RDP (Remote Desktop) som uppstår om guest operating system brandväggen blockerar inkommande trafik.

## <a name="symptoms"></a>Symtom

Du kan inte använda en RDP-anslutning för att ansluta till en Azure-dator (VM). Från Boot diagnostics -> skärmbild, den visar att operativsystemet har lästs in helt på välkomstskärmen (Ctrl + Alt + Del).

## <a name="cause"></a>Orsak

### <a name="cause-1"></a>Orsak 1 

RDP-regeln är inte ställts in för att tillåta RDP-trafik.

### <a name="cause-2"></a>Orsak 2 

Brandväggsprofiler för gäst-system är inställda att blockera alla inkommande anslutningar, inklusive RDP-trafik.

![Brandväggsinställning](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Lösning 

Innan du följer dessa steg kan du ta en ögonblicksbild av systemdisken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

Åtgärda problemet, kan du använda en av metoderna i [så Använd verktyg för att felsöka problem med Azure Virtuella](remote-tools-troubleshoot-azure-vm-issues.md) att fjärransluta till den virtuella datorn och sedan redigera brandväggsregler för gäst operativsystemet att **Tillåt** RDP-trafik .

### <a name="online-troubleshooting"></a>Felsökning av online

Ansluta till den [Seriekonsolen och öppna en PowerShell-instans](serial-console-windows.md#open-cmd-or-powershell-in-serial-console). Om Seriekonsolen inte är aktiverad på den virtuella datorn går du till ”[reparera den virtuella datorn Offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Lösning 1

1.  Om Azure-agenten har installerats och fungerar korrekt på den virtuella datorn måste du använda alternativet ”endast återställning configuration” under **Support och felsökning** > **Återställ lösenord** på VM-menyn.

2.  Kör det här alternativet gör följande:

    *   Aktiverar en RDP-komponenter om det har inaktiverats.

    *   Gör det möjligt för alla profiler för Windows-brandväggen.

    *   Se till att RDP-regeln är aktiverad i Windows-brandväggen.

    *   Om föregående steg inte fungerar kan du manuellt återställa brandväggsregeln. Gör detta genom att fråga efter alla regler som innehåller namnet ”Remote Desktop” genom att köra följande kommando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Du måste hitta alla anpassade regler som kanske har skapats och angetts till den här porten om RDP-porten har angetts till någon annan port än 3389. Om du vill fråga efter alla inkommande regler som har en anpassad port, kör du följande kommando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Om du ser att regeln är inaktiverad, aktivera den. Om du vill öppna en hel grupp, till exempel gruppen inbyggda fjärrskrivbord, kör du följande kommando:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```
    
    I annat fall öppna specifika fjärrskrivbord (TCP-In) regeln genom att köra följande kommando:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Du kan aktivera brandväggsprofiler till OFF för felsökning:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    När du har slutfört felsökning och ställa in brandväggen korrekt återaktivera brandväggen.
    
    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa ändringarna.

5.  Om du försök göra en RDP-anslutning för att få åtkomst till den virtuella datorn.

#### <a name="mitigation-2"></a>Lösning 2

1.  Fråga brandväggsprofiler för att avgöra om den inkommande principen har angetts för *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![AllProfiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Följande riktlinjer gäller för brandväggsprincipen, beroende på hur den är konfigurerad:
    >    * *BlockInbound*: All inkommande trafik blockeras om du inte har en regel som gäller för att tillåta den trafiken.
    >    * *BlockInboundAlways*: alla brandväggsregler ignoreras och all trafik blockeras.

2.  Redigera den *DefaultInboundAction* att ställa in de här profilerna på **Tillåt** trafik. Gör detta genom att köra följande kommando:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Fråga efter profiler igen för att se till att ändringen är klar. Gör detta genom att köra följande kommando:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa ändringarna. 

4.  Försök igen att få åtkomst till den virtuella datorn via RDP.

### <a name="offline-mitigations"></a>Offline-åtgärder 

1.  [Koppla systemdisken till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

3.  Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna systemdisken.

#### <a name="mitigation-1"></a>Lösning 1

Se [hur att aktivera / inaktivera en brandvägg-regel för ett dokument för gäst-OS]().

#### <a name="mitigation-2"></a>Lösning 2

1.  [Koppla systemdisken till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

3.  När de är kopplade till den Virtuella återställningsdatorn, se till att disken flaggas som **Online** i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna OS-disken.

4.  Öppna en upphöjd CMD-instans och kör följande skript:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM 

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Koppla från systemdisken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md).

6.  Kontrollera om problemet är löst.
