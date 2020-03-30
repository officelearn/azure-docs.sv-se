---
title: Installera Symantec-slutpunktsskydd på en Windows-virtuell dator i Azure
description: Lär dig hur du installerar och konfigurerar Symantec Endpoint Protection-säkerhetstillägget på en ny eller befintlig Azure-virtuell dator som skapats med den klassiska distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: akjosh
ms.openlocfilehash: 63f9441d4df9551405c2ab2bf8c0c67d7de5753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919914"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Installera och konfigurera Symantec-slutpunktsskydd på en virtuell dator med Windows
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

I den här artikeln visas hur du installerar och konfigurerar Symantec Endpoint Protection-klienten på en befintlig virtuell dator (VM) som kör Windows Server. Den här fullständiga klienten innehåller tjänster som virus- och spionprogramsskydd, brandvägg och intrångsskydd. Klienten installeras som ett säkerhetstillägg med hjälp av VM-agenten.

Om du har en befintlig prenumeration från Symantec för en lokal lösning kan du använda den för att skydda dina virtuella Azure-datorer. Om du inte är kund ännu kan du registrera dig för en utvärderingsprenumeration. Mer information om den här lösningen finns i [Symantec Endpoint Protection på Microsofts Azure-plattform][Symantec]. Den här sidan innehåller också länkar till licensieringsinformation och instruktioner för hur du installerar klienten om du redan är Symantec-kund.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installera Symantec-slutpunktsskydd på en befintlig virtuell dator
Innan du börjar behöver du följande:

* Azure PowerShell-modulen, version 0.8.2 eller senare, på din arbetsdator. Du kan kontrollera vilken version av Azure PowerShell som du har installerat med kommandot **Get-Module azure | format-table version.** Instruktioner och en länk till den senaste versionen finns i [Installera och konfigurera Azure PowerShell][PS]. Logga in på din `Add-AzureAccount`Azure-prenumeration med .
* VM-agenten som körs på den virtuella Azure-datorn.

Kontrollera först att VM-agenten redan är installerad på den virtuella datorn. Fyll i molntjänstnamnet och namnet på den virtuella datorn och kör sedan följande kommandon på en Azure PowerShell-kommandotolk på administratörsnivå. Ersätt allt inom citattecken, inklusive < och > tecken.

> [!TIP]
> Om du inte känner till molntjänsten och namn på virtuella datorer kör du **Get-AzureVM** för att lista namnen för alla virtuella datorer i din aktuella prenumeration.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Om kommandot skrivvärd visar **Sant**installeras **VM-agenten.** Om det visas **False,** se instruktionerna och en länk till nedladdningen i Azure blogginlägg [VM Agent och tillägg - Del 2][Agent].

Om VM-agenten är installerad kör du dessa kommandon för att installera Symantec Endpoint Protection-agenten.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Så här kontrollerar du att Symantecs säkerhetstillägg har installerats och är uppdaterat:

1. Logga in på den virtuella datorn. Instruktioner finns i [Så här loggar du in på en virtuell dator som kör Windows Server][Logon].
2. För Windows Server 2008 R2 klickar du på **Start > Symantec Slutpunktsskydd**. För Windows Server 2012 eller Windows Server 2012 R2 skriver du **Symantec**från startskärmen och klickar sedan på **Symantec-slutpunktsskydd**.
3. På fliken **Status** i fönstret **Status-Symantec slutpunktsskydd** kan du installera uppdateringar eller starta om om det behövs.

## <a name="additional-resources"></a>Ytterligare resurser
[Så här loggar du in på en virtuell dator som kör Windows Server][Logon]

[Azure VM-tillägg och -funktioner][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
