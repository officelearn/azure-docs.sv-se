---
title: Azure VM-brandväggen för gäst operativ system blockerar inkommande trafik | Microsoft Docs
description: Lär dig hur du åtgärdar anslutnings problemet för fjärr skrivbords portal (RDP) som gäst operativ systemets brand vägg blockerar inkommande trafik.
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
ms.openlocfilehash: 1b80fc997a4b3d2b472717b1ec2f379a4e958d8c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422560"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM-brandväggen för gäst operativ system blockerar inkommande trafik

Den här artikeln beskriver hur du korrigerar RDP-problemet (Remote Desktop Portal) som inträffar om gäst operativ systemets brand vägg blockerar inkommande trafik.

## <a name="symptoms"></a>Symtom

Du kan inte använda en RDP-anslutning för att ansluta till en virtuell Azure-dator (VM). Från startdiagnostik – > skärm bild, ser du att operativ systemet är fullständigt inläst på Välkomst skärmen (Ctrl + Alt + del).

## <a name="cause"></a>Orsak

### <a name="cause-1"></a>Orsak 1

RDP-regeln har inte kon figurer ATS för att tillåta RDP-trafik.

### <a name="cause-2"></a>Orsak 2

Brand Väggs profilerna för gäst systemet är inställda på att blockera alla inkommande anslutningar, inklusive RDP-trafiken.

![Brand Väggs inställning](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicks bild av system disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

Åtgärda problemet genom att använda någon av metoderna i [hur du använder fjärrverktyg för att felsöka problem med virtuella Azure-datorer](remote-tools-troubleshoot-azure-vm-issues.md) för att ansluta till den virtuella datorn via fjärr anslutning och sedan redigera brand Väggs reglerna för gäst operativ systemet så att RDP-trafik **tillåts** .

### <a name="online-troubleshooting"></a>Online-felsökning

Anslut till [serie konsolen och öppna sedan en PowerShell-instans](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Om serie konsolen inte är aktive rad på den virtuella datorn går du till "[reparera den virtuella datorn offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Minskning 1

1.  Om Azure-agenten är installerad och fungerar korrekt på den virtuella datorn kan du använda alternativet "Återställ endast konfigurationen" under **support + fel sökning** > **Återställ lösen ord** på VM-menyn.

2.  Att köra detta återställnings alternativ gör följande:

    *   Aktiverar en RDP-komponent om den är inaktive rad.

    *   Aktiverar alla profiler för Windows-brandväggen.

    *   Kontrol lera att RDP-regeln är aktive rad i Windows-brandväggen.

    *   Om föregående steg inte fungerar återställer du brand Väggs regeln manuellt. Det gör du genom att fråga alla regler som innehåller namnet "fjärr skrivbord" genom att köra följande kommando:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Om RDP-porten har angetts till någon annan port än 3389 måste du hitta en anpassad regel som kan ha skapats och angetts till den här porten. Kör följande kommando för att fråga efter alla regler för inkommande trafik som har en anpassad port:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Aktivera den om du ser att regeln är inaktive rad. Om du vill öppna en hel grupp, till exempel den inbyggda fjärr skrivbords gruppen, kör du följande kommando:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Annars kör du följande kommando för att öppna den aktuella regeln för fjärr skrivbord (TCP-in):

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  För fel sökning kan du aktivera brand Väggs profilerna:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    När du har slutfört fel sökningen och angett brand väggen korrekt aktiverar du brand väggen igen.

    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa ändringarna.

5.  Försök att upprätta en RDP-anslutning för att få åtkomst till den virtuella datorn.

#### <a name="mitigation-2"></a>Minskning 2

1.  Fråga brand Väggs profilerna för att avgöra om den inkommande brand Väggs principen är inställd på *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Följande rikt linjer gäller för brand Väggs principen, beroende på hur den har kon figurer ATS:
    >    * *BlockInbound*: all inkommande trafik kommer att blockeras om du inte har en regel som tillåter att trafiken används.
    >    * *BlockInboundAlways*: alla brand Väggs regler kommer att ignoreras och all trafik kommer att blockeras.

2.  Redigera *DefaultInboundAction* för att ställa in dessa profiler för att **tillåta** trafik. Gör detta genom att köra följande kommando:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Fråga profilerna igen för att se till att din ändring har gjorts. Gör detta genom att köra följande kommando:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa ändringarna.

4.  Försök igen för att få åtkomst till den virtuella datorn via RDP.

### <a name="offline-mitigations"></a>Offline-åtgärder

1.  [Anslut system disken till en virtuell återställnings dator](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en fjärr skrivbords anslutning till den virtuella återställnings datorn.

3.  Kontrol lera att disken är flaggad som **online** i disk hanterings konsolen. Anteckna enhets beteckningen som är kopplad till den anslutna system disken.

#### <a name="mitigation-1"></a>Minskning 1

Se [Aktivera-inaktivera en brand Väggs regel på ett gäst operativ system](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Minskning 2

1.  [Anslut system disken till en virtuell återställnings dator](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en fjärr skrivbords anslutning till den virtuella återställnings datorn.

3.  När system disken är ansluten till den virtuella återställnings datorn kontrollerar du att disken är flaggad som **online** i disk hanterings konsolen. Anteckna enhets beteckningen som tilldelas till den anslutna OS-disken.

4.  Öppna en upphöjd kommando instans och kör sedan följande skript:

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

5.  [Koppla från system disken och skapa den virtuella datorn på nytt](troubleshoot-recovery-disks-portal-windows.md).

6.  Kontrol lera om problemet är löst.
