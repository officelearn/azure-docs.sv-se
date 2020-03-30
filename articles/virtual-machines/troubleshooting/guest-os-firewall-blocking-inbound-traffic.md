---
title: Azure VM Guest OS-brandväggen blockerar inkommande trafik | Microsoft-dokument
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 0cbd1a24f5c460e248d55777735da6809befba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72028790"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM Guest OS-brandvägg blockerar inkommande trafik

I den hÃ¤r artikeln beskrivs hur du åtgärdar problemet med rdp (Remote Desktop Portal) som uppstÃ¥r om den anvÃ¤nder operativsystemets brandvÃ¤rrã¥tkomst blockerar inkommande trafik.

## <a name="symptoms"></a>Symtom

Du kan inte använda en RDP-anslutning för att ansluta till en virtuell Azure-dator (VM). Från Boot diagnostics -> Screenshot visar det att operativsystemet är fullständigt laddat på välkomstskärmen (Ctrl+Alt+Del).

## <a name="cause"></a>Orsak

### <a name="cause-1"></a>Orsak 1

RDP-regeln har inte konfigurerats för att tillåta RDP-trafik.

### <a name="cause-2"></a>Orsak 2

Gästsystembrandväggsprofilerna är inställda för att blockera alla inkommande anslutningar, inklusive RDP-trafiken.

![Brandväggsinställning](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicksbild av systemdisken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

Du åtgärdar problemet genom att använda någon av metoderna i [Hur du använder fjärrverktyg för att felsöka Azure VM-problem](remote-tools-troubleshoot-azure-vm-issues.md) för att fjärransluta till den virtuella datorn på distans och redigera sedan brandväggsreglerna för gästoperativsystemet för att **tillåta** FJÄRRSKRIVBORDStrafik.

### <a name="online-troubleshooting"></a>Felsökning online

Anslut till [seriekonsolen och öppna sedan en PowerShell-instans](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Om seriekonsolen inte är aktiverad på den virtuella datorn går du till "[Reparera den virtuella datorn offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Begränsning 1

1.  Om Azure Agent är installerat och fungerar korrekt på den virtuella datorn kan du använda alternativet "Endast återställningskonfiguration" under **Support + felsökning** > **Återställ lösenord** på vm-menyn.

2.  Om du kör det här återställningsalternativet gäller följande:

    *   Aktiverar en RDP-komponent om den är inaktiverad.

    *   Aktiverar alla Windows-brandväggsprofiler.

    *   Kontrollera att RDP-regeln är aktiverad i Windows-brandväggen.

    *   Om de föregående stegen inte fungerar återställer du brandväggsregeln manuellt. Det gör du genom att fråga alla regler som innehåller namnet "Fjärrskrivbord" genom att köra följande kommando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Om RDP-porten har angetts till någon annan port än 3389 måste du hitta alla anpassade regler som kan ha skapats och ställts in på den här porten. Om du vill fråga efter alla inkommande regler som har en anpassad port kör du följande kommando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Om du ser att regeln är inaktiverad aktiverar du den. Om du vill öppna en hel grupp, till exempel den inbyggda gruppen Fjärrskrivbord, kör du följande kommando:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Om du vill öppna den specifika TCP-In-regeln (Remote Desktop) kör du följande kommando:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  För felsökning kan du aktivera brandväggsprofilerna till AV:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    När du har felsökt och ställt in brandväggen på rätt sätt aktiverar du brandväggen igen.

    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa dessa ändringar.

5.  Försök att göra en RDP-anslutning för att komma åt den virtuella datorn.

#### <a name="mitigation-2"></a>Begränsning 2

1.  Fråga brandväggsprofilerna för att avgöra om principen för inkommande brandvägg är inställd *på BlockInboundAlways:*

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allaprofiler](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Följande riktlinjer gäller för brandväggsprincipen, beroende på hur den har konfigurerats:
    >    * *Blockera inkommande*: All inkommande trafik blockeras om du inte har en regel som gäller för att tillåta den trafiken.
    >    * *BlockInboundAlways*: Alla brandväggsregler ignoreras och all trafik blockeras.

2.  Redigera *DefaultInboundAction* för att ange dessa profiler så att **trafik tillåts.** Gör detta genom att köra följande kommando:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Fråga profilerna igen för att se till att ändringen har gjorts. Gör detta genom att köra följande kommando:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa ändringarna.

4.  Försök igen för att komma åt din virtuella dator via RDP.

### <a name="offline-mitigations"></a>Offline mildrande åtgärder

1.  [Koppla systemdisken till en återställnings-VM](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en anslutning till återställningsdatorn till återställningsdatorn.

3.  Kontrollera att disken är flaggad som **online** i konsolen Diskhantering. Observera enhetsbeteckningen som har tilldelats den bifogade systemdisken.

#### <a name="mitigation-1"></a>Begränsning 1

Se [Aktivera-inaktivera en brandväggsregel på ett gäst-OS](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Begränsning 2

1.  [Koppla systemdisken till en återställnings-VM](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en anslutning till återställningsdatorn till återställningsdatorn.

3.  När systemdisken har anslutits till återställningsdatorn kontrollerar du att disken är flaggad som **online** i diskhanteringskonsolen. Observera enhetsbeteckningen som har tilldelats den bifogade OS-disken.

4.  Öppna en förhöjd CMD-förekomst och kör sedan följande skript:

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
