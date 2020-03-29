---
title: Installera Azure VM-agenten i offlineläge | Microsoft-dokument
description: Lär dig hur du installerar Azure VM-agenten i offlineläge.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8ea85b560f35c79b3d5066d794f587345810b5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920866"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Installera Azure Virtual Machine Agent i offlineläge 

Azure Virtual Machine Agent (VM Agent) innehåller användbara funktioner, till exempel lokal återställning av administratörslösenord och skriptspårning. Den här artikeln visar hur du installerar VM-agenten för en virtuell dator (VM) (offline). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>När vm-agenten ska användas i offlineläge

Installera VM-agenten i offlineläge i följande scenarier:

- Den distribuerade Azure VM-datorn har inte VM-agenten installerad eller så fungerar inte agenten.
- Du har glömt administratörslösenordet för den virtuella datorn eller så kan du inte komma åt den virtuella datorn.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Så här installerar du VM-agenten i offlineläge

Använd följande steg för att installera VM-agenten i offlineläge.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Steg 1: Koppla os-disken för den virtuella datorn till en annan virtuell dator som en datadisk

1. Ta en ögonblicksbild för OS-disken för den berörda virtuella datorn, skapa en disk från ögonblicksbilden och bifoga sedan disken till en felsöka virtuell dator. Mer information finns i [Felsöka en Virtuell Windows-dator genom att koppla OS-disken till en återställnings-VM med Azure-portalen](troubleshoot-recovery-disks-portal-windows.md). För den klassiska virtuella datorn tar du bort den virtuella datorn och behåller OS-disken och ansluter sedan OS-disken till felsöka virtuella datorer.

2.  Anslut till felsökaren VM. Öppna > **Diskhantering för** **datorhantering**. Bekräfta att OS-disken är online och att enhetsbeteckningar tilldelas diskpartitionerna.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Steg 2: Ändra OS-disken för att installera Azure VM-agenten

1.  Upprätta en fjärrskrivbordsanslutning till felsökarens virtuella dator.

2.  Öppna mappen \windows\system32\config i felsökaren. Kopiera alla filer i den här mappen som en säkerhetskopia, om en återställning krävs.

3.  Starta **Registereditorn** (regedit.exe).

4.  Välj **HKEY_LOCAL_MACHINE** HKEY_LOCAL_MACHINE-tangenten. Välj > **Filinläsningsdatafil**på menyn: **File**

    ![Ladda bikupan](./media/install-vm-agent-offline/load-hive.png)

5.  Bläddra till mappen \windows\system32\config\SYSTEM på os-disken som du har bifogat. För namnet på registreringsdatafilen anger du **BROKENSYSTEM**. Den nya registreringsdatafilen visas under **HKEY_LOCAL_MACHINE** HKEY_LOCAL_MACHINE-tangenten.

6.  Bläddra till mappen \windows\system32\config\SOFTWARE på os-disken som du har bifogat. För namnet på hive-programvaran, ange **BROKENSOFTWARE**.

7. Om den anslutna OS-disken har VM-agenten installerad utför du en säkerhetskopia av den aktuella konfigurationen. Om den inte har VM-agent installerad går du vidare till nästa steg.
      
    1. Byt namn på mappen \windowsazure till \windowsazure.old.

    2. Exportera följande register:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Tjänster\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Tjänster\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Använd de befintliga filerna på felsökaren VM som en lagringsplats för vm-agentinstallationen. Utför följande steg:

    1. Från felsökarens virtuella dator exporterar du följande undernycklar i registerformat (.reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Tjänster\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Tjänster\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Tjänster\RdAgent

          ![Exportera registerundernycklarna](./media/install-vm-agent-offline/backup-reg.png)

    2. Redigera registerfilerna. I varje fil ändrar du **inmatningsvärdet SYSTEM** till **BROKENSYSTEM** (som visas i följande bilder) och sparar filen. Kom ihåg **ImagePath** för den aktuella VM-agenten. Vi kommer att behöva kopiera motsvarande mapp till den bifogade OS-disken. 

        ![Ändra registerundernyckelvärdena](./media/install-vm-agent-offline/change-reg.png)

    3. Importera registerfilerna till databasen genom att dubbelklicka på varje registerfil.

    4. Bekräfta att följande tre undernycklar har importerats till **HIVE-registreringsfilen BROKENSYSTEM:**
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent (9)9

    5. Kopiera installationsmappen för den aktuella VM-agenten till den anslutna OS-disken: 

        1.  Skapa en mapp med namnet WindowsAzure i rotsökvägen på os-disken som du har bifogat.

        2.  Gå till C:\WindowsAzure på felsökaren VM, leta efter en mapp med namnet C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Kopiera mappen GuestAgent som har det senaste versionsnumret från C:\WindowsAzure till WindowsAzure-mappen i den bifogade OS-disken. Om du inte är säker på vilken mapp som ska kopieras kopierar du alla GuestAgent-mappar. Följande bild visar ett exempel på mappen GuestAgent som kopieras till den anslutna OS-disken.

             ![Kopiera GuestAgent-mappen](./media/install-vm-agent-offline/copy-files.png)

9.  Välj **BROKENSYSTEM**. Välj > **Filavlastningsdatafil**på menyn . **File**

10.  Välj **BROKENSOFTWARE**. Välj > **Filavlastningsdatafil**på menyn . **File**

11.  Koppla från OS-disken och ändra sedan [OS-disken för den berörda virtuella datorn](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Skapa en ny virtuell dator med hjälp av den reparerade OS-disken för den klassiska virtuella datorn.

12.  Öppna den virtuella datorn. Observera att RdAgent körs och loggarna genereras.

Om du har skapat den virtuella datorn med hjälp av resurshanterarens distributionsmodell är du klar.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Använd egenskapen ProvisionGuestAgent för klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Om du skapade den virtuella datorn med den klassiska modellen använder du Azure PowerShell-modulen för att uppdatera egenskapen **ProvisionGuestAgent.** Egenskapen informerar Azure om att den virtuella datorn har VM-agenten installerad.

Om du vill ange egenskapen **ProvisionGuestAgent** kör du följande kommandon i Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Kör sedan `Get-AzureVM` kommandot. Observera att egenskapen **GuestAgentStatus** nu är fylld med data:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Virtual Machine Agent](../extensions/agent-windows.md)
- [Tillägg och funktioner för virtuella datorer för Windows](../extensions/features-windows.md)
