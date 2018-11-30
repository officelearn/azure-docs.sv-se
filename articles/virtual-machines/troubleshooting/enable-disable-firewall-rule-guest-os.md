---
title: Aktivera eller inaktivera en brandväggsregel på ett gäst-OS i Azure VM | Microsoft Docs
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
ms.openlocfilehash: cb2c548a94a91fe9126f684e382e9626adb93dd6
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319465"
---
# <a name="enable-or-disable-a-firewall-rule-on-a-azure-vm-guest-os"></a>Aktivera eller inaktivera en brandväggsregel på ett Gästoperativsystem för Azure VM

Den här artikeln innehåller en referens för att felsöka en situation där du misstänker att operativsystemet gästbrandvägg filtrerar delvis trafik på en virtuell dator (VM). Det kan vara praktiskt av följande skäl:

*   Om en ändring gjordes avsiktligt i brandväggen som orsakade RDP-anslutningar misslyckas, kan med hjälp av funktionen för tillägget för anpassat skript lösa problemet.

*   Inaktivera alla brandväggsprofiler är ett mer felsäker sätt att felsöka än att ange brandväggsregeln RDP-specifika.

## <a name="solution"></a>Lösning

Hur du konfigurerar brandväggsregler beror på vilken åtkomstnivå till den virtuella datorn som krävs. I följande exempel används RDP-regler. På samma sätt kan dock tillämpas till en annan typ av trafik genom att peka på rätt registernyckel.

### <a name="online-troubleshooting"></a>Felsökning av online 

#### <a name="mitigation-1-custom-script-extension"></a>Lösning 1: Tillägget för anpassat skript

1.  Skapa ditt skript med hjälp av följande mall.

    *   Så här aktiverar du en regel:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Inaktivera en regel:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Ladda upp skriptet i Azure portal med den [tillägget för anpassat skript](../extensions/custom-script-windows.md) funktionen. 

#### <a name="mitigation-2-remote-powershell"></a>Lösning 2: Fjärr-PowerShell

Om den virtuella datorn är online och kan kommas åt på en annan virtuell dator på samma virtuella nätverk kan göra du följande åtgärder med hjälp av den andra virtuella datorn.

1.  Öppna ett PowerShell-konsolfönster på Virtuella felsökningsdatorn.

2.  Kör följande kommandon och efter behov.

    *   Så här aktiverar du en regel:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Inaktivera en regel:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Lösning 3: PSTools kommandon

Om den virtuella datorn är online och kan kommas åt på en annan virtuell dator på samma virtuella nätverk kan göra du följande åtgärder med hjälp av den andra virtuella datorn.

1.  På Virtuellt felsökningsdatorn, ladda ned [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Öppna en CMD-instans och få åtkomst till den virtuella datorn via dess interna IP (DIP). 

    * Så här aktiverar du en regel:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Inaktivera en regel:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Minskning 4: Remote Registry

Om den virtuella datorn är online och kan kommas åt på en annan virtuell dator på samma virtuella nätverk, kan du använda [Remote Registry](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) på den andra virtuella datorn.

1.  Starta Registereditorn (regedit.exe) på Virtuellt felsökningsdatorn, och välj sedan **filen** > **ansluta register**.

2.  Öppna den *MÅLDATORN*\SYSTEM grenen och ange följande värden:

    * Öppna följande registervärde för att aktivera en regel:
    
        *MÅLDATORN*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Sedan kan ändra **Active = FALSE** till **Active = TRUE** i strängen:

        **v2.22 | Åtgärd = Tillåt | Aktiva = TRUE | Dir = In | Protocol = 6 | Profil = domän | Profil = privat | Profil = offentlig | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Namn =@FirewallAPI.dll,-28775 | Desc =@FirewallAPI.dll,-28756 | EmbedCtxt =@FirewallAPI.dll,-28752 |**
    
    * Öppna följande registervärde för att inaktivera en regel:
    
        *MÅLDATORN*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Sedan kan ändra **Active = TRUE** till **Active = FALSE**:
        
        **v2.22 | Åtgärd = Tillåt | Aktiva = FALSE | Dir = In | Protocol = 6 | Profil = domän | Profil = privat | Profil = offentlig | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Namn =@FirewallAPI.dll,-28775 | Desc =@FirewallAPI.dll,-28756 | EmbedCtxt =@FirewallAPI.dll,-28752 |**

3.  Starta om den virtuella datorn för att tillämpa ändringarna.

### <a name="offline-troubleshooting"></a>Felsökning offline 

Om du inte åtkomst till den virtuella datorn med varje metod, med hjälp av tillägget för anpassat skript kommer att misslyckas och du måste arbeta i OFFLINE-läge genom att arbeta direkt med hjälp av systemdisken.

Innan du följer dessa steg kan du ta en ögonblicksbild av systemdisken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

1.  [Koppla systemdisken till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

3.  Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera att enheten enhetsbokstaven som är tilldelad till den anslutna systemdisken.

4.  Innan du gör några ändringar kan du skapa en kopia av mappen \windows\system32\config om en återställning av ändringarna är nödvändigt.

5.  Starta Registereditorn (regedit.exe) på Virtuella felsökningsdatorn.

6.  Markera den **HKEY_LOCAL_MACHINE** nyckel och välj sedan **filen** > **Läs in registreringsdatafil** på menyn.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Leta upp och öppna sedan filen \windows\system32\config\SYSTEM. 

    > [!Note]
    > Du uppmanas att ange ett namn. Ange **BROKENSYSTEM**, och expandera sedan **HKEY_LOCAL_MACHINE**. Nu visas en ytterligare nyckel som heter **BROKENSYSTEM**. För den här felsökning monterar vi dessa problem registreringsdatafilerna som **BROKENSYSTEM**.

8.  Gör följande ändringar på BROKENSYSTEM gren:

    1.  Kontrollera vilka **ControlSet** registernyckel som den virtuella datorn startas från. Du kan se dess viktiga nummer i hklm\brokensystem\välj\aktuell.

    2.  Öppna följande registervärde för att aktivera en regel:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop UserMode i TCP
        
        Sedan kan ändra **Active = FALSE** till **Active = True**.
        
        **v2.22 | Åtgärd = Tillåt | Aktiva = TRUE | Dir = In | Protocol = 6 | Profil = domän | Profil = privat | Profil = offentlig | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Namn =@FirewallAPI.dll,-28775 | Desc =@FirewallAPI.dll,-28756 | EmbedCtxt =@FirewallAPI.dll,-28752 |**

    3.  Om du vill inaktivera en regel, öppnar du följande registernyckel:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop UserMode i TCP

        Sedan kan ändra **Active = True** till **Active = FALSE**.
        
        **v2.22 | Åtgärd = Tillåt | Aktiva = FALSE | Dir = In | Protocol = 6 | Profil = domän | Profil = privat | Profil = offentlig | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Namn =@FirewallAPI.dll,-28775 | Desc =@FirewallAPI.dll,-28756 | EmbedCtxt =@FirewallAPI.dll,-28752 |**

9.  Markera **BROKENSYSTEM**, och välj sedan **filen** > **ta bort registreringsdata** på menyn.

10. [Koppla från systemdisken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md).

11. Kontrollera om problemet är löst.