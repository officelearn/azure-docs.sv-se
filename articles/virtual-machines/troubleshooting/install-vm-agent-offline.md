---
title: Installera Azure VM-agenten i offline-läge | Microsoft Docs
description: Lär dig hur du installerar Azure VM-agenten i offline-läge.
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
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002620"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Installera Azure Virtual Machine-agenten i offline-läge 

Agenten för virtuell Azure-dator (VM-agent) tillhandahåller användbara funktioner, till exempel återställning av lokalt administratörs lösen ord och skript. Den här artikeln visar hur du installerar VM-agenten för en virtuell Windows-dator (VM) offline. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>När du ska använda VM-agenten i offline-läge

Installera VM-agenten i offline-läge i följande scenarier:

- Den distribuerade virtuella Azure-datorn har inte den virtuella dator agenten installerad eller också fungerar inte agenten.
- Du har glömt administratörs lösen ordet för den virtuella datorn eller så har du inte åtkomst till den virtuella datorn.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Så här installerar du VM-agenten i offline-läge

Använd följande steg för att installera VM-agenten i offline-läge.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Steg 1: koppla den virtuella datorns OS-disk till en annan virtuell dator som en datadisk

1. Ta en ögonblicks bild för OS-disken för den berörda virtuella datorn, skapa en disk från ögonblicks bilden och Anslut sedan disken till en Felsök virtuell dator. Mer information finns i [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure Portal](troubleshoot-recovery-disks-portal-windows.md). För den klassiska virtuella datorn tar du bort den virtuella datorn och behåller OS-disken och ansluter sedan OS-disken till fel söknings datorn.

2.  Anslut till fel söknings datorn. Öppna **Computer management**  >  **disk hantering** för dator hantering. Bekräfta att OS-disken är online och att enhets beteckningarna är kopplade till diskpartitionerna.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Steg 2: ändra OS-disken för att installera Azure VM-agenten

1.  Upprätta en fjärr skrivbords anslutning till fel söknings datorn.

2.  I den virtuella datorns fel sökare bläddrar du till den OS-disk som du har bifogat, öppnar mappen \Windows\System32\Config. Kopiera alla filer i den här mappen som en säkerhets kopia, om det krävs en återställning.

3.  Starta **Registereditorn** (regedit.exe).

4.  Välj den **HKEY_LOCAL_MACHINE** nyckeln. På menyn väljer du **fil**  >  **läsnings registrerings data** fil:

    ![Läs in registrerings data filen](./media/install-vm-agent-offline/load-hive.png)

5.  Bläddra till mappen \windows\system32\config\SYSTEM på den OS-disk som du har anslutit. Ange **BROKENSYSTEM** som namn på Hive. Den nya registrerings data filen visas under **HKEY_LOCAL_MACHINE** nyckeln.

6.  Bläddra till mappen \windows\system32\config\SOFTWARE på den OS-disk som du har anslutit. Skriv **BROKENSOFTWARE** som namn på Hive-programvaran.

7. Om den anslutna OS-disken har VM-agenten installerad utför du en säkerhets kopia av den aktuella konfigurationen. Om den inte har någon VM-agent installerad går du vidare till nästa steg.
      
    1. Byt namn på mappen \windowsazure till \windowsazure.old.

    2. Exportera följande register:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Använd de befintliga filerna på den virtuella fel söknings datorn som en lagrings plats för den virtuella dator Agent installationen. Slutför följande steg:

    1. Exportera följande under nycklar i register format (. reg) från fel söknings datorn: 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportera register under nycklarna](./media/install-vm-agent-offline/backup-reg.png)

    2. Redigera registerfilerna. I varje fil **ändrar du värdet för post värde** till **BROKENSYSTEM** (se följande bilder) och sparar filen. Kom ihåg **ImagePath** för den aktuella VM-agenten. Vi kommer att behöva kopiera motsvarande mapp till den anslutna OS-disken. 

        ![Ändra värden för register under nyckeln](./media/install-vm-agent-offline/change-reg.png)

    3. Importera registerfilerna till lagrings platsen genom att dubbelklicka på varje register fil.

    4. Bekräfta att följande två under nycklar har importer ATS till Hive- **BROKENSYSTEM** :
        - WindowsAzureGuestAgent
        - RdAgent

    5. Kopiera installationsmappen för den aktuella VM-agenten till den anslutna OS-disken: 

        1.  På den OS-disk som du har bifogat skapar du en mapp med namnet WindowsAzure i rot Sök vägen.

        2.  Gå till C:\WindowsAzure på den virtuella fel sökaren och leta efter en mapp med namnet C:\WindowsAzure\ GuestAgent_X. X. XXXX. XXX. Kopiera mappen GuestAgent som har det senaste versions numret från C:\WindowsAzure till mappen WindowsAzure på den anslutna OS-disken. Kopiera alla GuestAgent-mappar om du inte är säker på vilken mapp som ska kopieras. Följande bild visar ett exempel på GuestAgent-mappen som kopieras till den anslutna OS-disken.

             ![Kopiera mappen GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Välj **BROKENSYSTEM**. Från menyn väljer du **Arkiv**  >  **ta bort Hive**.

10.  Välj **BROKENSOFTWARE**. Från menyn väljer du **Arkiv**  >  **ta bort Hive**.

11.  Koppla från operativ system disken och [ändra sedan OS-disken för den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). För den klassiska virtuella datorn skapar du en ny virtuell dator med hjälp av den reparerade OS-disken.

12.  Åtkomst till den virtuella datorn. Observera att RdAgent körs och loggarna skapas.

Om du har skapat den virtuella datorn med hjälp av distributions modellen Resource Manager är du klar.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Använd egenskapen ProvisionGuestAgent för klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Om du har skapat den virtuella datorn med hjälp av den klassiska modellen använder du Azure PowerShell-modulen för att uppdatera egenskapen **ProvisionGuestAgent** . Egenskapen informerar Azure att VM-agenten är installerad på den virtuella datorn.

Om du vill ange egenskapen **ProvisionGuestAgent** kör du följande kommandon i Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Kör sedan `Get-AzureVM` kommandot. Observera att egenskapen **GuestAgentStatus** nu fylls med data:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Virtual Machine agent](../extensions/agent-windows.md)
- [Tillägg och funktioner för virtuella datorer för Windows](../extensions/features-windows.md)
