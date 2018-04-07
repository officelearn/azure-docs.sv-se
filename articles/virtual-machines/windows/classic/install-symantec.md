---
title: Installera Symantec Endpoint Protection på en Windows-dator i Azure | Microsoft Docs
description: Lär dig mer om att installera och konfigurera tillägg för Symantec Endpoint Protection-säkerhet på en ny eller befintlig Azure VM som skapats med den klassiska distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: iainfou
ms.openlocfilehash: bd96cb975bfc30b2561a98a950a9dd3fc060fa54
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Så installerar och konfigurerar du Symantec Endpoint Protection på virtuella Windows-datorer
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Den här artikeln visar hur du installerar och konfigurerar Symantec Endpoint Protection-klienten på en befintlig virtuell dator (VM) med Windows Server. Fullständig klienten inkluderar tjänster, till exempel virus och spionprogram skydd, brandvägg och intrångsskydd. Klienten installeras som ett tillägg för säkerhet med hjälp av den Virtuella Datoragenten.

Om du har en befintlig prenumeration från Symantec för en lokal lösning kan använda du den för att skydda din virtuella Azure-datorer. Om du inte är en kund ännu kan registrera du dig för en utvärderingsprenumeration. Mer information om den här lösningen finns [Symantec Endpoint Protection på Microsoft Azure-plattformen][Symantec]. Sidan innehåller också länkar till licensinformation och anvisningar för att installera klienten om du redan en Symantec-kund.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installera Symantec Endpoint Protection på en befintlig virtuell dator
Innan du börjar behöver du följande:

* Azure PowerShell-modulen, version 0.8.2 eller senare på datorn fungerar. Du kan kontrollera versionen av Azure PowerShell som du har installerat med den **Get-Module azure | format-table version** kommando. Instruktioner och en länk till den senaste versionen finns [installera och konfigurera Azure PowerShell][PS]. Logga in på Azure-prenumeration med hjälp av `Add-AzureAccount`.
* VM-agenten som körs på Azure-dator.

Kontrollera först att den Virtuella Datoragenten är installerad på den virtuella datorn. Fyll i molntjänstnamnet och namn på virtuell dator och kör följande kommandon i en administratörsnivå Azure PowerShell-kommandotolk. Ersätt allt inom citattecken, inklusive den < och > tecken.

> [!TIP]
> Om du inte vet Molntjänsten och namn på virtuella datorer, kör **Get-AzureVM** till lista med namn för alla virtuella datorer i din aktuella prenumeration.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Om den **write-host** kommando visar **SANT**, VM-agenten är installerad. Om det visar **FALSKT**, finns i anvisningarna och en länk för hämtning i Azure blogginlägget [Agent för VM-tillägg - del 2][Agent].

Om den Virtuella Datoragenten är installerad, kör du kommandona installera Symantec Endpoint Protection-agenten.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Kontrollera att tillägget Symantec säkerhet har installerats och är uppdaterad:

1. Logga in på den virtuella datorn. Instruktioner finns i [så att logga in på en virtuell dator kör Windows Server][Logon].
2. Windows Server 2008 R2, klickar du på **Start > Symantec Endpoint Protection**. Windows Server 2012 eller Windows Server 2012 R2 från startskärmen skriver **Symantec**, och klicka sedan på **Symantec Endpoint Protection**.
3. Från den **Status** för den **Status Symantec Endpoint Protection** fönster, tillämpa uppdateringar eller starta om vid behov.

## <a name="additional-resources"></a>Ytterligare resurser
[Hur du logga in på en virtuell dator som kör Windows Server][Logon]

[Azure VM-tillägg och funktioner][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
