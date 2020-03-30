---
title: Aktivera eller inaktivera en brandväggsregel för ett gästoperativsystem i Azure VM | Microsoft-dokument
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
ms.openlocfilehash: 782240c51833fc841af9f4260860db4c03897c03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71086438"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Aktivera eller inaktivera en brandväggsregel i gästoperativsystemet på en virtuell Azure-dator

Den här artikeln innehåller en referens för felsökning av en situation där du misstänker att brandväggen för gästoperativsystemet filtrerar partiell trafik på en virtuell dator (VM). Detta kan vara användbart av följande skäl:

*   Om en ändring avsiktligt gjordes i brandväggen som orsakade att RDP-anslutningar misslyckades kan problemet lösa problemet med funktionen Anpassat skripttillägg.

*   Att inaktivera alla brandväggsprofiler är ett mer idiotsäkert sätt att felsöka än att ange den RDP-specifika brandväggsregeln.

## <a name="solution"></a>Lösning

Hur du konfigurerar brandväggsreglerna beror på vilken åtkomstnivå du har till den virtuella datorn som krävs. I följande exempel används RDP-regler. Samma metoder kan dock tillämpas på alla andra typer av trafik genom att peka på rätt registernyckel.

### <a name="online-troubleshooting"></a>Felsökning online 

#### <a name="mitigation-1-custom-script-extension"></a>Begränsning 1: Anpassat skripttillägg

1.  Skapa skriptet med hjälp av följande mall.

    *   Så här aktiverar du en regel:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Så här inaktiverar du en regel:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Ladda upp skriptet i Azure-portalen med hjälp av funktionen [Anpassat skripttillägg.](../extensions/custom-script-windows.md) 

#### <a name="mitigation-2-remote-powershell"></a>Begränsning 2: Remote PowerShell

Om den virtuella datorn är online och kan nås på en annan virtuell dator i samma virtuella nätverk kan du göra följande åtgärder med hjälp av den andra virtuella datorn.

1.  Öppna ett PowerShell-konsolfönster i den virtuella felsökningsdatorn.

2.  Kör följande kommandon, beroende på vad som är lämpligt.

    *   Så här aktiverar du en regel:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Så här inaktiverar du en regel:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Begränsning 3: PSTools-kommandon

Om den virtuella datorn är online och kan nås på en annan virtuell dator i samma virtuella nätverk kan du göra följande åtgärder med hjälp av den andra virtuella datorn.

1.  Ladda ned [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)på felsöka virtuella datorer .

2.  Öppna en CMD-instans och få åtkomst till den virtuella datorn via dess interna IP (DIP). 

    * Så här aktiverar du en regel:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Så här inaktiverar du en regel:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Begränsning 4: Fjärrregister

Om den virtuella datorn är online och kan nås på en annan virtuell dator i samma virtuella nätverk kan du använda [Fjärrregistret](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) på den andra virtuella datorn.

1.  Starta Registereditorn (regedit.exe) på felsöka den virtuella datorn och välj sedan > **Arkivanslutningsnätverksregister**. **File**

2.  Öppna grenen *TARGET MACHINE*\SYSTEM och ange sedan följande värden:

    * Om du vill aktivera en regel öppnar du följande registervärde:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlset\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-in-TCP
    
        Ändra sedan **Active=FALSE** till **Active=TRUE** i strängen:

        **v2.22| Åtgärd=Tillåt| Active=SANT| Dir=In| Protokoll=6| Profile=Domän| Profile=Privat| Profile=Offentlig| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Namn=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**
    
    * Om du vill inaktivera en regel öppnar du följande registervärde:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlset\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-in-TCP

        Ändra sedan **Aktiv =SANT** till **Active=FALSKT:**
        
        **v2.22| Åtgärd=Tillåt| Active=FALSKT| Dir=In| Protokoll=6| Profile=Domän| Profile=Privat| Profile=Offentlig| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Namn=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

3.  Starta om den virtuella datorn för att tillämpa ändringarna.

### <a name="offline-troubleshooting"></a>Felsökning offline 

Om du inte kan komma åt den virtuella datorn på någon metod misslyckas det med anpassad skripttillägg och du måste arbeta i offlineläge genom att arbeta direkt via systemdisken.

Innan du följer dessa steg ska du ta en ögonblicksbild av systemdisken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

1.  [Koppla systemdisken till en återställnings-VM](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en anslutning till återställningsdatorn till återställningsdatorn.

3.  Kontrollera att disken är flaggad som **online** i konsolen Diskhantering. Observera att enhetsbeteckningen som är tilldelad den bifogade systemdisken.

4.  Innan du gör några ändringar skapar du en kopia av mappen \windows\system32\config om det skulle bli nödvändigt med en återställning av ändringarna.

5.  Starta Registereditorn (regedit.exe) på felsöka den virtuella datorn).

6.  Markera **HKEY_LOCAL_MACHINE** och välj sedan > **Filinläsningsdata från** menyn. **File**

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Leta reda på och öppna filen \windows\system32\config\SYSTEM. 

    > [!Note]
    > Du uppmanas att ange ett namn. Ange **BROKENSYSTEM**och expandera sedan **HKEY_LOCAL_MACHINE**. Du kommer nu att se ytterligare en nyckel som heter **BROKENSYSTEM**. För den här felsökningen monterar vi dessa problembef hives som **BROKENSYSTEM**.

8.  Gör följande ändringar på BROKENSYSTEM-grenen:

    1.  Kontrollera vilken **ControlSet-registernyckel** som den virtuella datorn startar från. Du kommer att se dess nyckelnummer i HKLM\BROKENSYSTEM\Select\Current.

    2.  Om du vill aktivera en regel öppnar du följande registervärde:
    
        HKLM\BROKENSYSTEM\ControlSet00x\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-in-TCP
        
        Ändra sedan **Active=FALSE** till **Active=True**.
        
        **v2.22| Åtgärd=Tillåt| Active=SANT| Dir=In| Protokoll=6| Profile=Domän| Profile=Privat| Profile=Offentlig| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Namn=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

    3.  Om du vill inaktivera en regel öppnar du följande registernyckel:

        HKLM\BROKENSYSTEM\ControlSet00x\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-in-TCP

        Ändra sedan **Active=True** till **Active=FALSE**.
        
        **v2.22| Åtgärd=Tillåt| Active=FALSKT| Dir=In| Protokoll=6| Profile=Domän| Profile=Privat| Profile=Offentlig| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Namn=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

9.  Markera **BROKENSYSTEM**och välj sedan**Filavlastningsdata** **File** > från menyn.

10. [Koppla från systemdisken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md).

11. Kontrollera om problemet är löst.
