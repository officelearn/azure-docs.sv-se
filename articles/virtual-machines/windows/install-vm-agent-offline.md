---
title: "Installera Azure VM-agenten i offlineläge | Microsoft Docs"
description: "Lär dig installera Azure VM-agenten i Offline-läge."
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
ms.openlocfilehash: b38bfaffad3e214f3b854715e4d8030cde432bae
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="install-the-vm-agent-in-offline-mode-in-an-azure-windows-vm"></a>Installera den Virtuella datoragenten i offlineläge på en Windows Azure-dator 

VM-agenten innehåller användbara funktioner som lokal administratör för lösenordsåterställning och skript push. Den här artikeln visar hur du installerar den Virtuella datoragenten för en virtuell dator som är offline. 

## <a name="when-to-use-offline-mode"></a>När du ska använda offline-läge

Du måste du installera den Virtuella datoragenten i offline-läge i följande scenario:

- Du distribuerar en virtuell Azure-dator som inte har VM-agenten installeras eller inte fungerar som den Virtuella datoragenten.
- Du kommer ihåg inte lösenordet för administratören eller du har inte åtkomst till den virtuella datorn.

I det här scenariot skulle du behöva installera den Virtuella datoragenten i offline-läge. 



## <a name="detailed-steps"></a>Detaljerade steg

**Steg 1: Koppla OS-disken på den virtuella datorn till en annan virtuell dator som en datadisk**

1.  Ta bort den virtuella datorn. Kontrollera att du väljer den **hålla diskarna** alternativet när du gör detta.

2.  Koppla OS-disk som en datadisk till en annan virtuell dator (en felsökare VM). Mer information finns i [How to attach a data disk to a Windows VM in the Azure portal](attach-managed-disk-portal.md). (Så här kopplar du en datadisk till en virtuell Windows-dator i Azure Portal)

3.  Anslut till den virtuella felsökningsdatorn. Öppna **Datorhantering** > **Diskhantering**. Kontrollera att OS-disken är online och att dess partitioner har enhetsbeteckningar.

**Steg 2: Ändra OS-disk om du vill installera VM-agenten**

1.  Gör en fjärrskrivbordsanslutning till Felsöka VM.

2.  OS-disk som du har kopplat, gå till **\windows\system32\config**. Kopiera alla filer som en säkerhetskopia om det krävs en återställning.

3.  Öppna Registereditorn (regedit.exe).

4.  Klicka på den **HKEY_LOCAL_MACHINE** nyckeln och välj sedan **filen** > **Läs in registreringsdata** på menyn.

    ![Läs in registreringsdata.](./media/install-vm-agent-offline/load-hive.png)

5.  Gå till **\windows\system32\config\SYSTEM** disken som du har kopplat på Operativsystemet och skriv sedan BROKENSYSTEM som namnet på hive. När du gör det visas registret hive under **HKEY_LOCAL_MACHINE**.

6.  Gå till **\windows\system32\config\SOFTWARE** på OS-disken bifogade, Skriv BROKENSOFTWARE som namn för strukturen.

7.  Om du har en aktuell version av agenten som inte fungerar kan du utföra en säkerhetskopia av den aktuella konfigurationen. Om den virtuella datorn inte har VM-agenten installerad, går du till nästa steg.  
      
    1. Byt namn på mappen \windowsazure till \windowsazure.old

    2. Exportera följande register
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Använd de befintliga filerna på felsökning VM som en lagringsplats för agentinstallation VM: 

    1. Exportera följande nycklar i registret format (.reg) från felsökaren för VM: 

        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Bild för export registernycklar](./media/install-vm-agent-offline/backup-reg.png)

    2. Redigera filerna tre registret, ändra den **SYSTEM** nyckeln post som **BROKENSYSTEM**, och spara filerna.
        ![Bilden om ändra registernycklar](./media/install-vm-agent-offline/change-reg.png)
    3. Dubbelklicka på registerfiler för att importera dem.
    4. Kontrollera att följande nycklar är har importerats till BROKENSYSTEM hive: WindowsAzureGuestAgent, WindowsAzureTelemetryService och RdAgent.

9.  Kopiera mappen VM-agenten från C:\windowsazure\packages till den &lt;OS-disk som ska&gt;: \windowsazure\packages.
    ![Bilden om kopiera filer](./media/install-vm-agent-offline/copy-package.png)
      
    **Obs** inte kopiera mappen loggar. När tjänsten startar kommer nya loggar att skapas.

10.  Klicka på BROKENSYSTEM och välj **filen** > **ta bort registreringsdata** på menyn.

11.  Klicka på BROKENSOFTWARE och välj **filen** > **ta bort registreringsdata** på menyn.

12.  Koppla bort disken och sedan skapa den virtuella datorn med hjälp av OS-disk.

13.  Om du har åtkomst till den virtuella datorn visas nu RDAgent körs och loggar komma skapas.

14. Om det här är en virtuell dator som skapats med hjälp av den klassiska distributionsmodellen är du klar. Om detta är en virtuell dator som skapats med hjälp av Resource Manager-distributionsmodellen kan använda du dock också Azure PowerShell för att uppdatera egenskapen ProvisionGuestAgent så att Azure vet att den virtuella datorn har agenten installerad. Kör följande kommandon i Azure PowerShell för att åstadkomma detta:

        $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
        $vm.VM.ProvisionGuestAgent = $true
        Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>

    Om du kör **Get-AzureVM** när du har följt de här stegen på **GuestAgentStatus** egenskapen bör fyllas i stället för en tom sida som ska visas:

        Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
        GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure virtuella datorns Agent](agent-user-guide.md)
- [Tillägg för virtuell dator och funktioner i Windows](extensions-features.md)