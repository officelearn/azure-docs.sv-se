---
title: Aktivera eller inaktivera en brand Väggs regel på ett gäst operativ system på en virtuell Azure-dator | Microsoft Docs
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
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086438"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Aktivera eller inaktivera en brand Väggs regel på en Azure VM gäst operativ system

Den här artikeln innehåller en referens för fel sökning av en situation där du misstänker att gäst operativ systemets brand vägg filtrerar delvis trafik på en virtuell dator (VM). Detta kan vara användbart av följande anledningar:

*   Om en ändring avsiktligt har gjorts till den brand vägg som orsakade att RDP-anslutningarna misslyckades kan du lösa problemet med hjälp av tillägget för anpassad skript funktion.

*   Att inaktivera alla brand Väggs profiler är ett mer foolprooft sätt att felsöka än att ställa in den RDP-specificerade brand Väggs regeln.

## <a name="solution"></a>Lösning

Hur du konfigurerar brand Väggs reglerna beror på åtkomst nivån för den virtuella datorn som krävs. I följande exempel används RDP-regler. Samma metoder kan dock tillämpas på alla typer av trafik genom att peka på rätt register nyckel.

### <a name="online-troubleshooting"></a>Felsökning av online 

#### <a name="mitigation-1-custom-script-extension"></a>Minskning 1: Anpassat skripttillägg

1.  Skapa ditt skript med hjälp av följande mall.

    *   Så här aktiverar du en regel:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Så här inaktiverar du en regel:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Överför skriptet i Azure Portal med hjälp av [tillägget för anpassat skript](../extensions/custom-script-windows.md) . 

#### <a name="mitigation-2-remote-powershell"></a>Minskning 2: Fjärr-PowerShell

Om den virtuella datorn är online och kan nås på en annan virtuell dator i samma virtuella nätverk kan du utföra följande åtgärder med hjälp av den andra virtuella datorn.

1.  Öppna en PowerShell-konsol i fönstret Felsök virtuell dator.

2.  Kör följande kommandon, efter vad som är tillämpligt.

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

#### <a name="mitigation-3-pstools-commands"></a>Minskning 3: PSTools-kommandon

Om den virtuella datorn är online och kan nås på en annan virtuell dator i samma virtuella nätverk kan du utföra följande åtgärder med hjälp av den andra virtuella datorn.

1.  Hämta [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)på den virtuella datorn för fel sökning.

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

#### <a name="mitigation-4-remote-registry"></a>Minskning 4: Fjär register

Om den virtuella datorn är online och kan nås på en annan virtuell dator i samma virtuella nätverk, kan du använda [fjär registret](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) på den andra virtuella datorn.

1.  Starta Registereditorn (regedit. exe) på den virtuella datorn för fel sökning och välj sedan **fil** > **Anslut nätverks register**.

2.  Öppna *mål datorn*\System-gren och ange följande värden:

    * Om du vill aktivera en regel öppnar du följande register värde:
    
        *Mål datorn*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-usermode-in-TCP
    
        Ändra sedan **aktiv = falskt** till **aktiv = sant** i strängen:

        **v-2.22 | Åtgärd = Tillåt | Active = TRUE | Dir = in | Protokoll = 6 | Profil = domän | Profil = privat | Profil = offentlig | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Namn =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**
    
    * Om du vill inaktivera en regel öppnar du följande register värde:
    
        *Mål datorn*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-usermode-in-TCP

        Ändra sedan **aktiv = sant** till **aktiv = falskt**:
        
        **v-2.22 | Åtgärd = Tillåt | Aktiv = falskt | Dir = in | Protokoll = 6 | Profil = domän | Profil = privat | Profil = offentlig | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Namn =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

3.  Starta om den virtuella datorn för att tillämpa ändringarna.

### <a name="offline-troubleshooting"></a>Offline-felsökning 

Om du inte kan komma åt den virtuella datorn med någon metod kommer det inte att gå att använda det anpassade skript tillägget och du måste arbeta i OFFLINE-läge genom att gå direkt genom system disken.

Innan du följer dessa steg kan du ta en ögonblicksbild av systemdisken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

1.  [Koppla systemdisken till virtuell återställningsdator](troubleshoot-recovery-disks-portal-windows.md).

2.  Starta en fjärrskrivbordsanslutning till den Virtuella återställningsdatorn.

3.  Kontrollera att disken flaggas som **Online** i konsolen Diskhantering. Observera att den enhets beteckning som är kopplad till den anslutna system disken.

4.  Innan du gör några ändringar skapar du en kopia av mappen \Windows\System32\Config om du behöver återställa ändringarna.

5.  Starta Registereditorn (regedit. exe) på den virtuella datorn för fel sökning.

6.  Markera nyckeln **HKEY_LOCAL_MACHINE** och välj sedan **fil** > **läsnings registrerings data** fil på menyn.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Leta upp och öppna filen \windows\system32\config\SYSTEM. 

    > [!Note]
    > Du uppmanas att ange ett namn. Ange **BROKENSYSTEM**och expandera sedan **HKEY_LOCAL_MACHINE**. Nu visas en ytterligare nyckel som heter **BROKENSYSTEM**. För den här fel sökningen monterar vi dessa problem strukturer som **BROKENSYSTEM**.

8.  Gör följande ändringar på BROKENSYSTEM-grenen:

    1.  Kontrol lera vilken register nyckel för **ControlSet** som den virtuella datorn startar från. Nyckel numret visas i HKLM\BROKENSYSTEM\Select\Current.

    2.  Om du vill aktivera en regel öppnar du följande register värde:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Ändra sedan **aktiv = falskt** till **aktiv = sant**.
        
        **v-2.22 | Åtgärd = Tillåt | Active = TRUE | Dir = in | Protokoll = 6 | Profil = domän | Profil = privat | Profil = offentlig | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Namn =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

    3.  Om du vill inaktivera en regel öppnar du följande register nyckel:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Ändra sedan **aktiv = sant** till **aktiv = falskt**.
        
        **v-2.22 | Åtgärd = Tillåt | Aktiv = falskt | Dir = in | Protokoll = 6 | Profil = domän | Profil = privat | Profil = offentlig | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Namn =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

9.  Markera **BROKENSYSTEM**och välj sedan **Arkiv** > **ta bort Hive** från menyn.

10. [Koppla från systemdisken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md).

11. Kontrollera om problemet är löst.
