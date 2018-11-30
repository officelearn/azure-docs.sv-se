---
title: Inaktivera gäst-OS-brandvägg i Azure VM | Microsoft Docs
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
ms.openlocfilehash: b0cd20278287b41dd953c64044b705aa2dba7557
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319409"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Inaktivera gäst-OS-brandvägg i Azure VM

Den här artikeln innehåller en referens för situationer där du misstänker att operativsystemet gästbrandvägg filtrerar helt eller delvis trafik till en virtuell dator (VM). Detta kan inträffa om ändringar gjordes avsiktligt i brandväggen som orsakade RDP-anslutningar misslyckas.

## <a name="solution"></a>Lösning

Processen som beskrivs i den här artikeln är avsedd att användas som en tillfällig lösning så att du kan fokusera på att åtgärda verkliga problemet, vilket är hur du konfigurerar brandväggsregler på rätt sätt. It\rquote s Microsoft rekommenderar att komponenten Windows-brandväggen aktiverad. Hur du konfigurerar brandväggen regler \cf3 beror på vilken åtkomstnivå till den virtuella datorn that\rquote s som krävs.

### <a name="online-solutions"></a>Onlinelösningar 

Om den virtuella datorn är online och kan kommas åt på en annan virtuell dator på samma virtuella nätverk, kan du göra dessa åtgärder med hjälp av den andra virtuella datorn.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Lösning 1: Funktionen för tillägget för anpassat skript eller kör kommandot

Om du har ett aktivt Azure-agenten kan du använda [tillägget för anpassat skript](../extensions/custom-script-windows.md) eller [kör kommandon](../windows/run-command.md) funktionen (endast Resource Manager-VM) för att köra följande skript via fjärranslutning.

> [!Note]
> * Om brandväggen är lokalt, kör du följande skript:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Om brandväggen har angetts via en Grupprincip i Active Directory, kan du använda Kör följande skript för tillfällig åtkomst. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Men när principen tillämpas igen, kommer du att går ut från fjärrsessionen. Definitiv lösning för det här problemet är att ändra den princip som tillämpas på den här datorn.

#### <a name="mitigation-2-remote-powershell"></a>Lösning 2: Fjärr-PowerShell

1.  Ansluta till en virtuell dator som finns på samma virtuella nätverk som den virtuella datorn som du inte kan nå via RDP-anslutning.

2.  Öppna ett PowerShell-konsolfönster.

3.  Kör följande kommandon:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Om brandväggen har angetts via ett grupprincipobjekt, fungerar inte den här metoden eftersom det här kommandot ändrar endast lokala registerposterna. Om en princip är på plats kan åsidosätter det här ändringen. 

#### <a name="mitigation-3-pstools-commands"></a>Lösning 3: PSTools kommandon

1.  På Virtuellt felsökningsdatorn, ladda ned [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Öppna en CMD-instans och sedan komma åt den virtuella datorn med dess DIP.

3.  Kör följande kommandon:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Minskning 4: Remote Registry 

Följ dessa steg för att använda [Remote Registry](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  Starta Registereditorn på Virtuellt felsökningsdatorn, och gå sedan till **filen** > **ansluta register**.

2.  Öppna den *MÅLDATORN*\SYSTEM grenen och ange följande värden:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Starta om tjänsten. Eftersom du inte gör det genom att använda remote registry måste du använda ta bort Tjänstkonsolen.

4.  Öppna en instans av **Services.msc**.

5.  Klicka på **tjänster (lokala)**.

6.  Välj **Anslut till en annan dator**.

7.  Ange den **privata IP-adress (DIP)** problemets VM.

8.  Starta om den lokala principen.

9.  Försök att ansluta till den virtuella datorn via RDP igen från din lokala dator.

### <a name="offline-solutions"></a>Offline-lösningar 

Om du har en situation där du inte kan nå den virtuella datorn med en metod som tillägget för anpassat skript misslyckas och du måste arbeta i OFFLINE-läge genom att arbeta direkt med hjälp av systemdisken. Det gör du genom att följa dessa steg:

1.  [Koppla systemdisken till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

3.  Kontrollera att disken flaggas som Online i konsolen Diskhantering. Observera den enhetsbeteckning som är tilldelad till den anslutna systemdisken.

4.  Innan du gör några ändringar kan du skapa en kopia av mappen \windows\system32\config om en återställning av ändringarna är nödvändigt.

5.  Starta Registereditorn (regedit.exe) på Virtuella felsökningsdatorn. 

6.  För proceduren felsökning monterar vi registreringsdatafilerna som BROKENSYSTEM och BROKENSOFTWARE.

7.  Markera nyckeln HKEY_LOCAL_MACHINE och Välj fil > Läs in registreringsdatafil på menyn.

8.  Leta upp filen \windows\system32\config\SYSTEM på anslutna systemdisken.

9.  Öppna en upphöjd PowerShell-instans och kör sedan följande kommandon:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Koppla från systemdisken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md).

11. Kontrollera om problemet är löst.
