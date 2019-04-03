---
title: Skapa en virtuell Windows-dator från en mall i Azure | Microsoft Docs
description: Använd Resource Manager-mall och PowerShell för att enkelt skapa en ny virtuell Windows-dator.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1ba96c76c51abcfe5bb5ef9bd66eb8a50afdfda
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846618"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Skapa en Windows-dator från en Resource Manager-mall

Lär dig hur du skapar en virtuell Windows-dator med en Azure Resource Manager-mall och Azure PowerShell från Azure Cloud shell. Den mall som användes i den här artikeln distribuerar en virtuell dator som kör Windows Server i ett nytt virtuellt nätverk med ett enda undernät. För att skapa en Linux-dator, se [hur du skapar en Linux-dator med Azure Resource Manager-mallar](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en Azure virtuell dator vanligtvis innehåller två steg:

- Skapa en resursgrupp. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn.
- Skapa en virtuell dator.

I följande exempel skapas en virtuell dator från en [Azure-snabbstartsmall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Här är en kopia av mallen:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Om du vill köra PowerShell-skriptet, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Om du väljer att installera och använda PowerShell lokalt i stället för från Azure Cloud shell kräver den här självstudien Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Du har angett en mall som lagras i GitHub i exemplet ovan. Du kan också hämta eller skapa en mall och ange den lokala sökvägen med den `--template-file` parametern.

Här följer några ytterligare resurser:

- Läs hur du utvecklar Resource Manager-mallar i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/).
- Scheman för Azure-dator finns i [Azure mallreferensen](/azure/templates/microsoft.compute/allversions).
- Flera VM-mallexempel finns i [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Det sista PowerShell-kommandot från föregående skript visar namnet på virtuella datorn. För att ansluta till den virtuella datorn, se [hur du ansluter och logga in på Azure-datorer som kör Windows](./connect-logon.md).

## <a name="next-steps"></a>Nästa steg

- Om det finns problem med distributionen, kan du ta en titt på [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Lär dig att skapa och hantera en virtuell dator i [skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om hur du skapar mallar, visa JSON-syntax och egenskaper för resurstyper som du har distribuerat:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
