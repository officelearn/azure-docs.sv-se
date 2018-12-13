---
title: Installera Azure VM-agenten i offlineläge | Microsoft Docs
description: Lär dig hur du installerar Azure VM-agenten i offlineläge.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 14b108a836424e92a251f50b42ed93963038a333
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192064"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Installera Azure VM-agenten i offlineläge 

Azure VM-agenten (VM-agenten) ger användbara funktioner, till exempel återställning av lösenord för lokal administratör och skript som push-överföring. Den här artikeln visar hur du installerar VM-agenten för en offline Windows-dator (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>När du ska använda VM-agenten i offlineläge

Installera VM-agenten i offlineläge i följande scenarier:

- Den distribuerade virtuella Azure-datorn har inte VM-agenten installerad eller agenten fungerar inte.
- Du har glömt administratörslösenordet för den virtuella datorn eller du kan inte komma åt den virtuella datorn.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Så här installerar du VM-agenten i offlineläge

Använd följande steg för att installera VM-agenten i offlineläge.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Steg 1: Koppla OS-disken för den virtuella datorn till en annan virtuell dator som en datadisk

1.  Ta bort den virtuella datorn. Se till att välja den **behålla diskarna** när du tar bort den virtuella datorn.

2.  Koppla OS-disken som en datadisk till en annan virtuell dator (kallas en _felsökare_ VM). Mer information finns i [ansluter en datadisk till en virtuell Windows-dator i Azure-portalen](../windows/attach-managed-disk-portal.md).

3.  Anslut till felsökaren för virtuell dator. Öppna **Datorhantering** > **Diskhantering**. Kontrollera att OS-disken är online och att enhetsbeteckningar tilldelas diskpartitionerna.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Steg 2: Ändra OS-disken för att installera Azure VM-agenten

1.  Se en fjärrskrivbordsanslutning till felsökaren för virtuell dator.

2.  Bläddra till mappen \windows\system32\config på OS-disken bifogade. Kopiera alla filer i den här mappen som en säkerhetskopia, om en återställning.

3.  Starta den **Registereditorn** (regedit.exe).

4.  Välj den **HKEY_LOCAL_MACHINE** nyckel. På menyn, Välj **filen** > **Läs in registreringsdatafil**:

    ![Läsa in registreringsdata](./media/install-vm-agent-offline/load-hive.png)

5.  Bläddra till mappen \windows\system32\config\SYSTEM på OS-disken bifogade. Namnet på hive, ange **BROKENSYSTEM**. Den nya registreringsdatafilen visas under den **HKEY_LOCAL_MACHINE** nyckel.

6.  Bläddra till mappen \windows\system32\config\SOFTWARE på OS-disken bifogade. Namnet på hive-programvara, ange **BROKENSOFTWARE**.

7. Om den anslutna OS-disken har VM-agenten installerad, utför du en säkerhetskopia av den aktuella konfigurationen. Om den inte har VM-agenten installerad, flytta till nästa steg.
      
    1. Byt namn på mappen \windowsazure till \windowsazure.old.

    2. Exportera följande register:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Använda de befintliga filerna på felsökaren för virtuell dator som en lagringsplats för VM-agentinstallation. Utför följande steg:

    1. Exportera följande undernycklar i registret format (.reg) från felsökaren för virtuell dator: 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportera registernycklarna](./media/install-vm-agent-offline/backup-reg.png)

    2. Redigera registret. I varje fil, ändrar du värdet för posten **SYSTEM** till **BROKENSYSTEM** (som visas i följande avbildningar) och spara filen. Kom ihåg den **ImagePath** av den befintliga VM-agenten. Vi behöver du kopiera mappen motsvarande till den anslutna OS-disken. 

        ![Ändra undernycklar registervärden](./media/install-vm-agent-offline/change-reg.png)

    3. Importera registerfilerna till databasen genom att dubbelklicka på varje fil i registret.

    4. Bekräfta att följande tre undernycklar har importerats till den **BROKENSYSTEM** hive:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. Kopiera installationsmappen för den befintliga VM-agenten till den anslutna OS-disken: 

        1.  Skapa en mapp med namnet Azure visade sig vara i rotsökvägen på OS-disken bifogade.

        2.  Gå till C:\WindowsAzure på felsökaren för virtuell dator ska du leta efter en mapp med namnet C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Kopiera mappen GuestAgent med senaste versionsnumret från C:\WindowsAzure till mappen Azure visade sig vara i den anslutna OS-disken. Om du inte är säker på vilken mapp som ska kopieras kopiera alla GuestAgent mappar. Följande bild visar ett exempel på mappen GuestAgent som kopieras till den anslutna OS-disken.

             ![Kopiera GuestAgent mapp](./media/install-vm-agent-offline/copy-files.png)

9.  Välj **BROKENSYSTEM**. På menyn, väljer **filen** > **ta bort registreringsdata**.

10.  Välj **BROKENSOFTWARE**. På menyn, väljer **filen** > **ta bort registreringsdata**.

11.  Koppla från OS-disken och sedan återskapa den virtuella datorn med hjälp av OS-disken.

12.  Åtkomst till den virtuella datorn. Observera att RdAgent körs och loggarna genereras.

Om du har skapat den virtuella datorn med hjälp av distributionsmodellen i Resource Manager kan är du klar.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Använd egenskapen ProvisionGuestAgent för klassiska virtuella datorer

Om du har skapat den virtuella datorn med hjälp av den klassiska modellen kan använda Azure PowerShell-modulen för att uppdatera den **ProvisionGuestAgent** egenskapen. Egenskapen meddelar Azure att den virtuella datorn har VM-agenten installerad.

Ange den **ProvisionGuestAgent** -egenskapen i Azure PowerShell kör du följande kommandon:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Kör sedan den `Get-AzureVM` kommando. Observera att den **GuestAgentStatus** egenskapen nu har fyllts med data:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure VM-agenten](../extensions/agent-windows.md)
- [Virtuella datorer, tillägg och funktioner för Windows](../extensions/features-windows.md)
