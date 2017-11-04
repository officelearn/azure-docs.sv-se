---
title: "Installera Trend Micro djup Security på en virtuell dator | Microsoft Docs"
description: "Den här artikeln beskriver hur du installerar och konfigurerar Trend Micro säkerhet på en virtuell dator som skapats med den klassiska distributionsmodellen i Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: iainfou
ms.openlocfilehash: 911b8f12472dcbda3e6bfeb8c97bf1d04a63e1dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Så installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en virtuell Windows-dator
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Den här artikeln visar hur du installerar och konfigurerar Trend Micro djup säkerhet som en tjänst på en ny eller befintlig virtuell dator (VM) med Windows Server. Djupgående säkerhet som en tjänst innehåller skydd mot skadlig kod, en brandvägg, ett intrång förebyggande system och integritet övervakning.

Klienten installeras som ett tillägg för säkerhet via den Virtuella Datoragenten. På en ny virtuell dator installerar du agenten djup säkerhet när den Virtuella Datoragenten skapas automatiskt av Azure-portalen.

En befintlig virtuell dator skapas med den klassiska portalen, Azure CLI eller PowerShell kanske inte har en VM-agent. För en befintlig virtuell dator som inte har den Virtuella Datoragenten, måste du hämta och installera det först. Den här artikeln täcker båda situationer.

Om du har någon aktuell prenumeration från Trend Micro för en lokal lösning kan använda du den för att skydda din virtuella Azure-datorer. Om du inte är en kund ännu kan registrera du dig för en utvärderingsprenumeration. Mer information om den här lösningen finns i bloggposten Trend Micro [Microsoft Azure VM-tillägget för djup säkerhetsgrupper](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installera djup Security-agenten på en ny virtuell dator

Den [Azure-portalen](http://portal.azure.com) kan du installera tillägget Trend Micro säkerhet när du använder en bild från den **Marketplace** att skapa den virtuella datorn. Om du skapar en virtuell dator är med hjälp av portalen ett enkelt sätt att lägga till skydd från Trend Micro.

Med hjälp av en transaktion från den **Marketplace** öppnar en guide som hjälper dig att ställa in den virtuella datorn. Du använder den **inställningar** bladet, panelen tredje i guiden för att installera tillägget Trend Micro säkerhet.  Allmänna instruktioner finns i [skapa en virtuell dator som kör Windows i Azure portal](tutorial.md).

När du kommer till den **inställningar** bladet i guiden, gör du följande:

1. Klicka på **tillägg**, klicka på **lägga till tillägget** i nästa ruta.

   ![Börja lägga till tillägget][1]

2. Välj **djup säkerhet Agent** i den **ny resurs** fönstret. Klicka på i fönstret djup säkerhet Agent **skapa**.

   ![Identifiera Agent djup säkerhet][2]

3. Ange den **klient-ID** och **klient aktivering lösenord** för tillägget. Alternativt kan du ange en **princip säkerhetsidentifieraren**. Klicka på **OK** att lägga till klienten.

   ![Ange information om webbprogramtillägg][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installera djup Security-agenten på en befintlig virtuell dator
Om du vill installera agenten på en befintlig virtuell dator, behöver du följande:

* Azure PowerShell-modulen, version 0.8.2 eller senare, installeras på den lokala datorn. Du kan kontrollera versionen av Azure PowerShell som du har installerat med hjälp av den **Get-Module azure | format-table version** kommando. Instruktioner och en länk till den senaste versionen finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). Logga in på Azure-prenumeration med hjälp av `Add-AzureAccount`.
* VM-agenten installeras på den virtuella måldatorn.

Kontrollera först att den Virtuella Datoragenten är installerad. Fyll i molntjänstnamnet och namn på virtuell dator och kör följande kommandon i en administratörsnivå Azure PowerShell-kommandotolk. Ersätt allt inom citattecken, inklusive den < och > tecken.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Om du inte vet Molntjänsten och namn på virtuell dator, kör **Get-AzureVM** visa informationen för alla virtuella datorer i din nuvarande prenumeration.

Om den **write-host** kommandot returnerar **SANT**, VM-agenten är installerad. Om den returnerar **FALSKT**, finns i anvisningarna och en länk för hämtning i Azure blogginlägget [Agent för VM-tillägg - del 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Om den Virtuella Datoragenten är installerad kan du köra dessa kommandon.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
Det tar några minuter för att agenten ska börja köras när den är installerad. Därefter måste du aktivera djup säkerhet på den virtuella datorn så att den kan hanteras av en djup Security Manager. Se följande artiklar för mer information:

* Trends artikel om den här lösningen [Instant-On Cloud Security för Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* En [exempel på Windows PowerShell-skript](http://go.microsoft.com/fwlink/?LinkId=404100) att konfigurera den virtuella datorn
* [Instruktioner](http://go.microsoft.com/fwlink/?LinkId=404099) för

## <a name="additional-resources"></a>Ytterligare resurser
[Logga in till en virtuell dator som kör Windows Server]

[Azure VM-tillägg och funktioner]

<!-- Image references -->
[1]: ./media/install-trend/new_vm_Blade3.png
[2]: ./media/install-trend/find_SecurityAgent.png
[3]: ./media/install-trend/SecurityAgentDetails.png

<!-- Link references -->
[Logga in till en virtuell dator som kör Windows Server]:connect-logon.md
[Azure VM-tillägg och funktioner]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
