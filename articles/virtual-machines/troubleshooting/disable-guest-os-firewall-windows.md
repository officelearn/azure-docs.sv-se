---
title: Inaktivera gästoperativ brandväggen för os i Azure VM | Microsoft-dokument
description: Lär dig en lösningsmetod för felsökningssituationer där en gästoperativsystembrandvägg filtrerar partiell eller fullständig trafik till en virtuell dator.
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
ms.openlocfilehash: e4cd1595d963330bd5decb366310bf5e97f59bc8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422366"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Inaktivera brandvägg för gästoperativsystem i en virtuell Azure-dator

Den här artikeln innehåller en referens för situationer där du misstänker att brandväggen för gästoperativsystemet filtrerar partiell eller fullständig trafik till en virtuell dator (VM). Detta kan inträffa om ändringar avsiktligt gjordes i brandväggen som orsakade RDP-anslutningar misslyckas.

## <a name="solution"></a>Lösning

Processen som beskrivs i den här artikeln är avsedd att användas som en lösning så att du kan fokusera på att åtgärda ditt verkliga problem, vilket är hur du ställer in brandväggsreglerna korrekt. Det är en Microsoft Best Practice att ha Komponenten Windows-brandväggen aktiverad. Hur du konfigurerar brandväggsreglerna beror på vilken åtkomstnivå du har till den virtuella datorn som krävs.

### <a name="online-solutions"></a>Online-lösningar 

Om den virtuella datorn är online och kan nås på en annan virtuell dator i samma virtuella nätverk kan du göra dessa åtgärder med hjälp av den andra virtuella datorn.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Begränsning 1: Funktionen Anpassat skripttillägg eller körkommando

Om du har en fungerande Azure-agent kan du använda [Anpassat skripttillägg](../extensions/custom-script-windows.md) eller funktionen [Kör kommandon](../windows/run-command.md) (endast resurshanterarens virtuella datorer) för att fjärrköra följande skript.

> [!Note]
> * Om brandväggen är inställd lokalt kör du följande skript:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Om brandväggen är inställd via en Active Directory-princip kan du använda köra följande skript för tillfällig åtkomst. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Men så fort principen tillämpas igen kommer du att sparkas ut från fjärrsessionen. Den permanenta korrigeringen för det här problemet är att ändra principen som tillämpas på den här datorn.

#### <a name="mitigation-2-remote-powershell"></a>Begränsning 2: Remote PowerShell

1.  Anslut till en virtuell dator som finns i samma virtuella nätverk som den virtuella datorn som du inte kan nå med hjälp av RDP-anslutning.

2.  Öppna ett PowerShell-konsolfönster.

3.  Kör följande kommandon:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Om brandväggen är inställd via ett grupprincipobjekt kanske den här metoden inte fungerar eftersom det här kommandot bara ändrar de lokala registerposterna. Om det finns en princip åsidosätts ändringen. 

#### <a name="mitigation-3-pstools-commands"></a>Begränsning 3: PSTools-kommandon

1.  Ladda ned [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)på felsöka virtuella datorer .

2.  Öppna en CMD-förekomst och sedan komma åt den virtuella datorn via dess DIP.

3.  Kör följande kommandon:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Begränsning 4: Fjärrregister 

Följ dessa steg för att använda [Fjärrregister](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  Starta registerredigeraren på felsöka den virtuella datorn och gå sedan till **File** > **Connect Network Registry**.

2.  Öppna grenen *TARGET MACHINE*\SYSTEM och ange följande värden:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Starta om tjänsten. Eftersom du inte kan göra det med fjärrregistret måste du använda Remote Service Console.

4.  Öppna en instans av **Services.msc**.

5.  Klicka på **Tjänster (lokalt)**.

6.  Välj **Anslut till en annan dator**.

7.  Ange den **privata IP-adressen (DIP)** för problemet VM.

8.  Starta om den lokala brandväggsprincipen.

9.  Försök att ansluta till den virtuella datorn via RDP igen från den lokala datorn.

### <a name="offline-solutions"></a>Offlinelösningar 

Om du har en situation där du inte kan nå den virtuella datorn på någon metod misslyckas anpassat skripttillägg och du måste arbeta i OFFLINE-läge genom att arbeta direkt via systemdisken. Det gör du genom att följa dessa steg:

1.  [Koppla systemdisken till en återställnings-VM](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en anslutning till återställningsdatorn till återställningsdatorn.

3.  Kontrollera att disken är flaggad som online i konsolen Diskhantering. Observera enhetsbeteckningen som har tilldelats den bifogade systemdisken.

4.  Innan du gör några ändringar skapar du en kopia av mappen \windows\system32\config om det skulle bli nödvändigt med en återställning av ändringarna.

5.  Starta registerredigeraren (regedit.exe) på felsöka den virtuella datorn). 

6.  För den här felsökningsproceduren monterar vi bikuporna som BROKENSYSTEM och BROKENSOFTWARE.

7.  Markera HKEY_LOCAL_MACHINE och välj sedan Arkiv > Läs in Hive på menyn.

8.  Leta reda på filen \windows\system32\config\SYSTEM på den bifogade systemdisken.

9.  Öppna en upphöjd PowerShell-förekomst och kör sedan följande kommandon:

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
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
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
