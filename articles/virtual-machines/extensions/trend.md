---
title: Installera Trend Micro Deep Security på en virtuell dator
description: I den här artikeln beskrivs hur du installerar och konfigurerar Trend Micro-säkerhet på en virtuell dator som skapats med den klassiska distributionsmodellen i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: cffd2eab3a616b4d16d847d0f2e1a26655f40459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919931"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Så här installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en Virtuell Windows-dator

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Den här artikeln visar hur du installerar och konfigurerar Trend Micro Deep Security som en tjänst på en ny eller befintlig virtuell dator (VM) som kör Windows Server. Deep Security as a Service omfattar skydd mot skadlig kod, en brandvägg, ett intrångsförebyggande system och integritetsövervakning.

Klienten installeras som ett säkerhetstillägg via VM-agenten. På en ny virtuell dator installerar du Deep Security Agent, eftersom VM-agenten skapas automatiskt av Azure-portalen.

En befintlig virtuell dator som skapats med Azure-portalen, Azure CLI eller PowerShell kanske inte har en VM-agent. För en befintlig virtuell dator som inte har VM-agenten måste du hämta och installera den först. Den här artikeln täcker båda situationerna.

Om du har en aktuell prenumeration från Trend Micro för en lokal lösning kan du använda den för att skydda dina virtuella Azure-datorer. Om du inte är kund ännu kan du registrera dig för en utvärderingsprenumeration. Mer information om den här lösningen finns i Trend Micro-blogginlägget [Microsoft Azure VM Agent Extension For Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installera Deep Security Agent på en ny virtuell dator

[Med Azure-portalen](https://portal.azure.com) kan du installera säkerhetstillägget Trend Micro när du använder en avbildning från **Marketplace** för att skapa den virtuella datorn. Om du skapar en enda virtuell dator är det enkelt att använda portalen för att lägga till skydd från Trend Micro.

Om du använder en post från **Marketplace** öppnas en guide som hjälper dig att konfigurera den virtuella datorn. Du använder **bladet Inställningar,** den tredje panelen i guiden, för att installera säkerhetstillägget Trend Micro.  Allmänna instruktioner finns i [Skapa en virtuell dator som kör Windows i Azure-portalen](../windows/classic/tutorial.md).

Gör följande när du kommer till guiden **Inställningar:**

1. Klicka på **Tillägg**och sedan **på Lägg till tillägg** i nästa fönster.

   ![Börja lägga till tillägget][1]

2. Välj **Deep Security Agent** i fönstret Ny **resurs.** Klicka på **Skapa**i fönstret Djupsäkerhetsagent.

   ![Identifiera deep security agent][2]

3. Ange **klientidentifieraren** och **klientaktiveringslösenordet** för tillägget. Du kan också ange en **säkerhetsprincipidentifierare**. Klicka sedan på **OK** för att lägga till klienten.

   ![Ange tilläggsinformation][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installera Deep Security Agent på en befintlig virtuell dator
Om du vill installera agenten på en befintlig virtuell dator behöver du följande objekt:

* Azure PowerShell-modulen, version 0.8.2 eller senare, installerad på din lokala dator. Du kan kontrollera vilken version av Azure PowerShell som du har installerat med kommandot **Get-Module azure | format-table version.** Instruktioner och en länk till den senaste versionen finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). Logga in på din `Add-AzureAccount`Azure-prenumeration med .
* VM-agenten som är installerad på den virtuella måldatorn.

Kontrollera först att VM-agenten redan är installerad. Fyll i molntjänstnamnet och namnet på den virtuella datorn och kör sedan följande kommandon på en Azure PowerShell-kommandotolk på administratörsnivå. Ersätt allt inom citattecken, inklusive < och > tecken.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Om du inte känner till molntjänsten och namnet på den virtuella datorn kör **du Get-AzureVM** för att visa den informationen för alla virtuella datorer i din aktuella prenumeration.

Om **kommandot skrivvärd** returnerar **True**installeras VM-agenten. Om den returnerar **False**läser du instruktionerna och en länk till hämtningen i Azure-blogginlägget [VM Agent and Extensions - Part 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Om VM-agenten är installerad kör du dessa kommandon.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
Det tar några minuter för agenten att börja köras när den är installerad. Därefter måste du aktivera Deep Security på den virtuella datorn så att den kan hanteras av en Deep Security Manager. Mer information finns i följande artiklar:

* Trends artikel om den här lösningen, [Instant-On Cloud Security för Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Ett [exempel på Windows PowerShell-skript](https://go.microsoft.com/fwlink/?LinkId=404100) för att konfigurera den virtuella datorn
* [Instruktioner](https://go.microsoft.com/fwlink/?LinkId=404099) för provet

## <a name="additional-resources"></a>Ytterligare resurser
[Så här loggar du in på en virtuell dator med Windows Server]

[Azure VM-tillägg och -funktioner]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Så här loggar du in på en virtuell dator med Windows Server]:../windows/classic/connect-logon.md
[Azure VM-tillägg och -funktioner]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
