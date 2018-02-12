---
title: "Installera Azure VM-agenten i offlineläge | Microsoft Docs"
description: "Lär dig hur du installerar Azure VM-agenten i offline-läge."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 3770cc3338c89a9bf88e2cf9ec3faa37e2ff109b
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Installera Azure Virtual Machine-agenten i offline-läge 

Virtuella Azure-agenten (VM-Agent) ger användbara funktioner, till exempel återställning av lösenord för lokal administratör och skript som push-överföring. Den här artikeln visar hur du installerar VM-Agent för en frånkopplad Windows virtuell dator (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>När du ska använda den Virtuella Datoragenten i offline-läge

Installera den Virtuella Datoragenten i offline-läge i följande scenarier:

- Den distribuerade virtuella Azure-datorn har inte VM-agenten installerad eller agenten fungerar inte.
- Du har glömt administratörslösenordet för den virtuella datorn eller du har inte åtkomst till den virtuella datorn.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Så här installerar du den Virtuella Datoragenten i offline-läge

Använd följande steg för att installera den Virtuella Datoragenten i offline-läge.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Steg 1: Koppla OS-disken på den virtuella datorn till en annan virtuell dator som en datadisk

1.  Ta bort den virtuella datorn. Se till att välja den **hålla diskarna** när du tar bort den virtuella datorn.

2.  Koppla OS-disk som en datadisk till en annan virtuell dator (kallas även en _felsökaren_ VM). Mer information finns i [ansluta en datadisk till en virtuell Windows-dator i Azure portal](attach-managed-disk-portal.md).

3.  Ansluta till felsökaren VM. Öppna **Datorhantering** > **Diskhantering**. Kontrollera att OS-disken är online och att enhetsbeteckningar tilldelas diskpartitioner.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Steg 2: Ändra OS-disk om du vill installera Azure VM-agenten

1.  Se en fjärrskrivbordsanslutning till felsökaren VM.

2.  Bläddra till mappen \windows\system32\config på OS-disken bifogade. Kopiera alla filer i den här mappen som en säkerhetskopia, om det krävs en återställning.

3.  Starta den **Registereditorn** (regedit.exe).

4.  Välj den **HKEY_LOCAL_MACHINE** nyckel. Välj på menyn **filen** > **Läs in registreringsdata**:

    ![Läsa in registreringsdata](./media/install-vm-agent-offline/load-hive.png)

5.  Bläddra till mappen \windows\system32\config\SYSTEM på OS-disk som du har kopplat. Namnet på hive, ange **BROKENSYSTEM**. Den nya registreringsdatafilen visas under den **HKEY_LOCAL_MACHINE** nyckel.

6.  Bläddra till mappen \windows\system32\config\SOFTWARE på OS-disk som du har kopplat. Namnet på programmet, hive, ange **BROKENSOFTWARE**.

7.  Om den Virtuella Datoragenten inte fungerar säkerhetskopiera den aktuella konfigurationen.

    >[!NOTE]
    >Om den virtuella datorn inte har agenten installerad kan du gå vidare till steg 8. 
      
    1. Byt namn på mappen \windowsazure \windowsazure.old.

    2. Exportera följande register:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Använda de befintliga filerna på felsökaren VM som databas för VM-Agent-installation. Utför följande steg:

    1. Exportera följande nycklar i registret format (.reg) från felsökaren för VM: 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Exportera registernycklarna](./media/install-vm-agent-offline/backup-reg.png)

    2. Redigera registret. Ändra värdet för posten i varje fil **SYSTEM** till **BROKENSYSTEM** (som visas i följande bilder) och spara filen.

        ![Ändra undernycklar registervärden](./media/install-vm-agent-offline/change-reg.png)

    3. Importera registerfilerna i databasen genom att dubbelklicka på varje fil i registret.

    4. Bekräfta att följande tre undernycklar har importerats till den **BROKENSYSTEM** hive:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  Kopiera mappen VM-agenten från C:\windowsazure\packages till den &lt;OS-disk som ska&gt;: \windowsazure\packages.

    ![Kopiera filerna VM-agenten till OS-disk](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >Kopiera inte den **loggar** mapp. När tjänsten startar skapas nya loggar.

10.  Välj **BROKENSYSTEM**. Välj på menyn **filen** > **ta bort registreringsdata**.

11.  Välj **BROKENSOFTWARE**. Välj på menyn **filen** > **ta bort registreringsdata**.

12.  Koppla från OS-disken och sedan skapa den virtuella datorn med hjälp av OS-disk.

13.  Åtkomst till den virtuella datorn. Observera att RdAgent körs och loggar som genereras.

Om du har skapat den virtuella datorn med hjälp av den klassiska distributionsmodellen är du klar.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Använd egenskapen ProvisionGuestAgent för virtuella datorer som skapats med Azure Resource Manager

Om du har skapat den virtuella datorn med hjälp av Resource Manager-distributionsmodellen använder du Azure PowerShell-modulen för att uppdatera den **ProvisionGuestAgent** egenskapen. Egenskapen informerar Azure att den virtuella datorn VM-agenten har installerats.

Ange den **ProvisionGuestAgent** egenskap, kör följande kommandon i Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Kör sedan den `Get-AzureVM` kommando. Observera att den **GuestAgentStatus** egenskapen nu fylls i med data:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure virtuella datorns Agent](agent-user-guide.md)
- [Tillägg för virtuell dator och funktioner i Windows](extensions-features.md)
