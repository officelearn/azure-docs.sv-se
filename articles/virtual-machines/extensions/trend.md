---
title: Installera Trend Micro Deep Security på en virtuell dator | Microsoft Docs
description: Den här artikeln beskriver hur du installerar och konfigurerar du Trend Micro security på en virtuell dator som skapats med den klassiska distributionsmodellen i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: roiyz
ms.openlocfilehash: ddb999c252f0802ef57942de694340ae29f2d8ba
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240366"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Så installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en virtuell Windows-dator
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Den här artikeln visar hur du installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en ny eller befintlig virtuell dator (VM) som kör Windows Server. Deep Security som en tjänst innehåller skydd mot skadlig kod, en brandvägg, ett intrångsskyddssystem och övervakning av filintegritet.

Klienten installeras som en säkerhetstillägg via VM-agenten. På en ny virtuell dator installerar du den Deep Security Agent när VM-agenten skapas automatiskt av Azure-portalen.

En befintlig virtuell dator som skapats med hjälp av Azure-portalen, Azure CLI eller PowerShell kanske inte har en VM-agenten. För en befintlig virtuell dator som inte har VM-agenten måste du ladda ned och installera den först. Den här artikeln täcker båda situationer.

Om du har någon aktuell prenumeration från Trend Micro för en lokal lösning kan använda du den för att skydda virtuella datorer i Azure. Om du inte är en kund ännu kan registrera du dig för en utvärderingsprenumeration. Mer information om den här lösningen finns i blogginlägget Trend Micro [Microsoft Azure VM Agent-tillägget för Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installera Deep Security Agent på en ny virtuell dator

Den [Azure-portalen](http://portal.azure.com) kan du installera tillägget Trend Micro säkerhet när du använder en bild från den **Marketplace** att skapa den virtuella datorn. Om du skapar en virtuell dator, är med hjälp av portalen ett enkelt sätt att lägga till skydd från Trend Micro.

Med hjälp av en transaktion från den **Marketplace** öppnar en guide som hjälper dig att ställa in den virtuella datorn. Du använder den **inställningar** bladet, panelen tredje i guiden om du vill installera tillägget Trend Micro säkerhet.  Mer information, se [skapa en virtuell dator som kör Windows i Azure-portalen](../windows/classic/tutorial.md).

När du kommer till den **inställningar** bladet i guiden och gör följande:

1. Klicka på **tillägg**, klicka sedan på **lägga till tillägget** i nästa ruta.

   ![Börja lägga till tillägget][1]

2. Välj **Deep Security Agent** i den **ny resurs** fönstret. Klicka på i fönstret Deep Security Agent **skapa**.

   ![Identifiera Djupsäkerhetsagent][2]

3. Ange den **klientidentifierare** och **lösenord för aktivering av Klientorganisation** för tillägget. Du kan alternativt kan du ange en **Security Principidentifierare**. Klicka sedan på **OK** att lägga till klienten.

   ![Ange information om webbprogramtillägg][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installera Deep Security Agent på en befintlig virtuell dator
Om du vill installera agenten på en befintlig virtuell dator, behöver du följande objekt:

* Azure PowerShell-modulen, version 0.8.2 eller senare, installerad på den lokala datorn. Du kan kontrollera vilken version av Azure PowerShell som du har installerat med hjälp av den **Get-Module azure | format-table version** kommando. Instruktioner och en länk till den senaste versionen finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). Logga in på Azure-prenumerationen med `Add-AzureAccount`.
* VM-agenten installerad på den virtuella måldatorn.

Kontrollera först att VM-agenten är redan installerad. Fyll i molntjänstens namn och namn på virtuell dator och kör sedan följande kommandon i en behörighet på Azure PowerShell-kommandotolk. Ersätt allt inom citattecken, inklusive den < och > tecken.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Om du inte vet molntjänst och namn på virtuell dator, kör **Get-AzureVM** visa informationen för alla virtuella datorer i din aktuella prenumeration.

Om den **write-host** kommandot **SANT**, VM-agenten är installerad. Om den returnerar **FALSKT**, se instruktionerna och en länk för nedladdning i Azure-blogginlägget [VM-agenten och tillägg – del 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Om den Virtuella Datoragenten är installerad kan du köra dessa kommandon.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
Det tar några minuter för att agenten ska börja köra när den är installerad. Därefter måste du aktivera Deep Security på den virtuella datorn så att den kan hanteras av en Deep Security Manager. Se följande artiklar för ytterligare information:

* Trends artikeln om den här lösningen [Instant-On Molnsäkerhet för Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* En [exempel på Windows PowerShell-skript](https://go.microsoft.com/fwlink/?LinkId=404100) att konfigurera den virtuella datorn
* [Instruktioner](https://go.microsoft.com/fwlink/?LinkId=404099) för exemplet

## <a name="additional-resources"></a>Ytterligare resurser
[Hur du ansluter till en virtuell dator som kör Windows Server]

[Azure VM-tillägg och funktioner]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Hur du ansluter till en virtuell dator som kör Windows Server]:../windows/classic/connect-logon.md
[Azure VM-tillägg och funktioner]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
