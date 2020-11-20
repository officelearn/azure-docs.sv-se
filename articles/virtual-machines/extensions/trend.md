---
title: Installera Trend Micro djupgående säkerhet på en virtuell dator
description: Den här artikeln beskriver hur du installerar och konfigurerar Trend Micro Security på en virtuell dator som har skapats med den klassiska distributions modellen i Azure.
author: axayjo
tags: azure-service-management
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: 9d6d80287d05517933f066d5e49fa31e78a48943
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966387"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Så här installerar och konfigurerar du Trend Micro djupgående säkerhet som en tjänst på en virtuell Windows-dator

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Den här artikeln visar hur du installerar och konfigurerar Trend Micro djupgående säkerhet som en tjänst på en ny eller befintlig virtuell dator (VM) som kör Windows Server. Djupgående säkerhet som en tjänst innehåller skydd mot skadlig kod, en brand vägg, ett system för skydd mot intrång och integritets övervakning.

Klienten installeras som ett säkerhets tillägg via VM-agenten. På en ny virtuell dator installerar du en djup säkerhets agent eftersom den virtuella dator agenten skapas automatiskt av Azure Portal.

En befintlig virtuell dator som skapats med Azure Portal, Azure CLI eller PowerShell kanske inte har någon VM-agent. För en befintlig virtuell dator som inte har VM-agenten måste du ladda ned och installera den först. I den här artikeln beskrivs båda situationerna.

Om du har en aktuell prenumeration från Trend Micro för en lokal lösning kan du använda den för att skydda dina virtuella Azure-datorer. Om du inte är en kund än kan du registrera dig för en utvärderings prenumeration. Mer information om den här lösningen finns i Trend Micro blogg inlägg [Microsoft Azure VM agent Extension för djup säkerhet](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installera djup säkerhets agenten på en ny virtuell dator

Med [Azure Portal](https://portal.azure.com) kan du installera Trend Micro Security-tillägget när du använder en avbildning från **Marketplace** för att skapa den virtuella datorn. Om du skapar en enda virtuell dator är det ett enkelt sätt att lägga till skydd från Trend Micro i portalen.

Om du använder en post från **Marketplace** öppnas en guide som hjälper dig att konfigurera den virtuella datorn. Du använder bladet **Inställningar** , den tredje panelen i guiden för att installera Trend Micro Security-tillägget.  Allmänna anvisningar finns i [skapa en virtuell dator som kör Windows i Azure Portal](../windows/quick-create-portal.md).

Gör så här när du kommer till **inställnings** bladet i guiden:

1. Klicka på **tillägg** och sedan på **Lägg till tillägg** i nästa fönster.

   ![Börja lägga till tillägget][1]

2. Välj **djup säkerhets agent** i fönstret **Ny resurs** . I fönstret djup säkerhets Agent klickar du på **skapa**.

   ![Identifiera djup säkerhets agent][2]

3. Ange **klient-ID** och **klient aktiverings lösen ord** för tillägget. Alternativt kan du ange en **säkerhets princip identifierare**. Klicka sedan på **OK** för att lägga till klienten.

   ![Ange tilläggs information][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installera djup säkerhets agenten på en befintlig virtuell dator
Om du vill installera agenten på en befintlig virtuell dator behöver du följande objekt:

* Azure PowerShell-modulen, version 0.8.2 eller senare, är installerad på den lokala datorn. Du kan kontrol lera vilken version av Azure PowerShell som du har installerat med hjälp av kommandot **Get-module Azure | Format-Table version** . Instruktioner och en länk till den senaste versionen finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/). Logga in på din Azure-prenumeration med `Add-AzureAccount` .
* VM-agenten som är installerad på den virtuella mål datorn.

Kontrol lera först att VM-agenten redan är installerad. Fyll i namnet på moln tjänsten och namnet på den virtuella datorn och kör sedan följande kommandon på administratörs nivå Azure PowerShell kommando tolken. Ersätt allt inom citat tecken, inklusive < och > tecken.

```azurepowershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Om du inte känner till moln tjänsten och namnet på den virtuella datorn kör du **Get-AzureVM** för att visa informationen för alla virtuella datorer i din aktuella prenumeration.

Om kommandot **Skriv-Host** returnerar **True** installeras VM-agenten. Om den returnerar **false** kan du läsa anvisningarna och en länk till hämtningen i Azure-bloggen publicera [VM-agenten och tilläggen – del 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Om den virtuella dator agenten är installerad kör du dessa kommandon.

```azurepowershell
$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM
```

## <a name="next-steps"></a>Nästa steg
Det tar några minuter innan agenten börjar köras när den är installerad. Därefter måste du aktivera djupgående säkerhet på den virtuella datorn så att den kan hanteras av en djup säkerhets hanterare. Mer information finns i följande artiklar:

* Trends artikeln om den här lösningen, [snabb moln säkerhet för Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Ett [exempel på Windows PowerShell-skript](https://go.microsoft.com/fwlink/?LinkId=404100) för att konfigurera den virtuella datorn
* [Instruktioner](https://go.microsoft.com/fwlink/?LinkId=404099) för exemplet

## <a name="additional-resources"></a>Ytterligare resurser
[Så här loggar du in på en virtuell dator som kör Windows Server]

[Azure VM-tillägg och-funktioner]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Så här loggar du in på en virtuell dator som kör Windows Server]:../windows/classic/connect-logon.md
[Azure VM-tillägg och-funktioner]: features-windows.md
