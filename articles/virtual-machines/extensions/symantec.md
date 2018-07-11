---
title: Installera Symantec Endpoint Protection på en Windows-dator i Azure | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar säkerhetstillägg Symantec Endpoint Protection på en ny eller befintlig Azure VM som skapats med den klassiska distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 3102a183b5ce124182f770545a75deb9b128574f
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930570"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Så installerar och konfigurerar du Symantec Endpoint Protection på virtuella Windows-datorer
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Den här artikeln visar hur du installerar och konfigurerar du Symantec Endpoint Protection-klienten på en befintlig virtuell dator (VM) som kör Windows Server. Det här fullständiga klienten innehåller tjänster, till exempel virus och spionprogram skydd, brandvägg och intrångsskydd. Klienten installeras som en utökning av säkerhet med hjälp av VM-agenten.

Om du har en befintlig prenumeration från Symantec för en lokal lösning kan använda du det för att skydda virtuella datorer i Azure. Om du inte är en kund ännu kan registrera du dig för en utvärderingsprenumeration. Mer information om den här lösningen finns i [Symantec Endpoint Protection på Microsofts Azure-plattformen][Symantec]. Den här sidan finns också länkar till mer information om licenser och instruktioner för att installera klienten om du redan är en Symantec-kund.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installera Symantec Endpoint Protection på en befintlig virtuell dator
Innan du börjar behöver du följande:

* Azure PowerShell-modulen, version 0.8.2 eller senare på din arbetsdator. Du kan kontrollera vilken version av Azure PowerShell som du har installerat med den **Get-Module azure | format-table version** kommando. Instruktioner och en länk till den senaste versionen finns i [installera och konfigurera Azure PowerShell][PS]. Logga in på Azure-prenumerationen med `Add-AzureAccount`.
* VM-agenten som körs på Azure-dator.

Kontrollera först att VM-agenten är redan installerad på den virtuella datorn. Fyll i molntjänstens namn och namn på virtuell dator och kör sedan följande kommandon i en behörighet på Azure PowerShell-kommandotolk. Ersätt allt inom citattecken, inklusive den < och > tecken.

> [!TIP]
> Om du inte känner till tjänst i molnet och namn på virtuella datorer kan köra **Get-AzureVM** att lista namnen för alla virtuella datorer i din aktuella prenumeration.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Om den **write-host** kommandot visar **SANT**, VM-agenten är installerad. Om den visar **FALSKT**, se instruktionerna och en länk för nedladdning i Azure-blogginlägget [VM-agenten och tillägg – del 2][Agent].

Om VM-agenten är installerad, kör du följande kommandon för att installera Symantec Endpoint Protection-agenten.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Kontrollera att tillägget Symantec security har installerats och är uppdaterad:

1. Logga in på den virtuella datorn. Anvisningar finns i [så här loggar du in på en virtuell dator som kör Windows Server][Logon].
2. För Windows Server 2008 R2, klickar du på **starta > Symantec Endpoint Protection**. Windows Server 2012 eller Windows Server 2012 R2, från startskärmen skriver **Symantec**, och klicka sedan på **Symantec Endpoint Protection**.
3. Från den **Status** fliken den **Status Symantec Endpoint Protection** fönstret tillämpa uppdateringar eller starta om vid behov.

## <a name="additional-resources"></a>Ytterligare resurser
[Så här loggar du in på en virtuell dator som kör Windows Server][Logon]

[Azure VM-tillägg och funktioner][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
