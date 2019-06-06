---
title: Azure Snabbstart – säkerhetskopiera en virtuell dator med Resource Manager-mall
description: Lär dig hur du säkerhetskopierar virtuella datorer med Azure Resource Manager-mall
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b144d7509562b8ca0bca6299caee4a7ce292f4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481371"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Säkerhetskopiera en virtuell dator i Azure med Resource Manager-mall

[Azure Backup](backup-overview.md) säkerhetskopierar lokala datorer och appar och virtuella Azure-datorer. Den här artikeln visar hur du säkerhetskopierar en virtuell Azure-dator med Resource Manager-mall och Azure PowerShell. Den här snabbstarten fokuserar på hur du distribuerar en Resource Manager-mall för att skapa ett återställa Services-valv. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](/azure/azure-resource-manager/) och [mallreferensen](/azure/templates/microsoft.recoveryservices/allversions).

Du kan också säkerhetskopiera en virtuell dator med [Azure PowerShell](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md), eller i den [Azure-portalen](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Skapa en virtuell dator och Recovery Services-valv

En [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhetskopieringsjobb körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Den mall som användes i den här snabbstarten är från [Azures snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Den här mallen kan du distribuera enkel Windows-VM och Recovery Services-valv som konfigurerats med DefaultPolicy för skydd.

Om du vill distribuera mallen, Välj **prova** att öppna Azure Cloud shell och klistra in följande PowerShell-skript i fönstret shell. Om du vill klistra in koden, högerklicka på shell-fönstret och välj sedan **klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Azure PowerShell används för att distribuera Resource Manager-mallen i den här snabbstarten. Den [Azure-portalen](../azure-resource-manager/resource-group-template-deploy-portal.md), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md), och [Rest API](../azure-resource-manager/resource-group-template-deploy-rest.md) kan också användas för att distribuera mallar.

## <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb

Mallen skapar en virtuell dator och aktiverar tillbaka på den virtuella datorn. När du distribuerar mallen kan behöva du starta ett säkerhetskopieringsjobb. Mer information finns i [starta ett säkerhetskopieringsjobb](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen

Om du vill övervaka säkerhetskopieringen, se [övervaka säkerhetskopieringen](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Rensa upp distributionen

Om du behöver inte längre att säkerhetskopiera den virtuella datorn kan rensa du den.

- Hoppa över den Rensa upp om du vill prova återställer den virtuella datorn.
- Om du använde en befintlig virtuell dator kan du hoppa över sista [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdleten och ha kvar resursgruppen och den Virtuella datorn.

Inaktivera skyddet, ta bort återställningspunkterna och valv. Ta sedan bort resursgruppen och de relaterade Virtuella datorresurserna på följande sätt:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett Recovery Services-valv, aktiverat skydd på en virtuell dator och skapat den första återställningspunkten.

- [Lär dig hur](tutorial-backup-vm-at-scale.md) att säkerhetskopiera virtuella datorer i Azure-portalen.
- [Lär dig hur](tutorial-restore-disk.md) snabbt återställa en virtuell dator
