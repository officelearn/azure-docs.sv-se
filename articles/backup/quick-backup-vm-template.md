---
title: Snabbstart - Vm-säkerhetskopia för Resurshanteraren
description: Lär dig hur du säkerhetskopierar dina virtuella datorer med Azure Resource Manager-mall
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d6fb73801f0f460daf2ed70f8dc88187e41ea887
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458853"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Säkerhetskopiera en virtuell dator i Azure med Resource Manager-mall

[Azure Backup](backup-overview.md) säkerhetskopierar lokala datorer och appar och virtuella Azure-datorer. Den här artikeln visar hur du säkerhetskopierar en Azure VM med Resource Manager-mall och Azure PowerShell. Den här snabbstarten fokuserar på hur du distribuerar en Resource Manager-mall för att skapa ett Recover Services-valv. Mer information om hur du utvecklar Resource Manager-mallar finns i [Dokumentationen](/azure/azure-resource-manager/) till Resource Manager och [mallreferensen](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också säkerhetskopiera en virtuell dator med [Azure PowerShell,](./quick-backup-vm-powershell.md) [Azure CLI](quick-backup-vm-cli.md)eller i [Azure-portalen](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Skapa ett vm- och återställningstjänster-valv

Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhetskopieringsjobb körs skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten är från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Med den här mallen kan du distribuera enkla Windows VM och Recovery Services Vault som konfigurerats med DefaultPolicy for Protection.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

De resurser som definieras i mallen är:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAdresser**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/valv**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/valv/backupFabric/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>Distribuera mallen

Om du vill distribuera mallen väljer du **Prova** den för att öppna Azure Cloud Shell och klistra sedan in följande PowerShell-skript i skalfönstret. Om du vill klistra in koden högerklickar du på skalfönstret och väljer sedan **Klistra in**.

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

Azure PowerShell används för att distribuera Resource Manager-mallen i den här snabbstarten. [Azure-portalen,](../azure-resource-manager/templates/deploy-portal.md) [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)och [Rest API](../azure-resource-manager/templates/deploy-rest.md) kan också användas för att distribuera mallar.

## <a name="validate-the-deployment"></a>Verifiera distributionen

### <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb

Mallen skapar en virtuell dator och aktiverar den virtuella datorn igen. När du har distribuerat mallen måste du starta ett säkerhetskopieringsjobb. Mer information finns i [Starta ett säkerhetskopieringsjobb](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen

Om du vill övervaka säkerhetskopieringsjobbet finns i [Övervaka säkerhetskopieringsjobbet](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Rensa distributionen

Om du inte längre behöver säkerhetskopiera den virtuella datorn kan du rensa den.

- Om du vill prova att återställa den virtuella datorn hoppar du över rensningen.
- Om du har använt en befintlig virtuell dator kan du hoppa över den slutliga [Cmdleten Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att lämna resursgruppen och den virtuella datorn på plats.

Inaktivera skydd, ta bort återställningspunkterna och valvet. Ta sedan bort resursgruppen och associerade VM-resurser enligt följande:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett Recovery Services-valv, aktiverat skydd på en virtuell dator och skapat den första återställningspunkten.

- [Läs om hur du](tutorial-backup-vm-at-scale.md) säkerhetskopierar virtuella datorer i Azure-portalen.
- [Lär dig hur du](tutorial-restore-disk.md) snabbt återställer en virtuell dator
- [Lär dig hur du](../azure-resource-manager/templates/template-tutorial-create-first-template.md) skapar Resource Manager-mallar.
