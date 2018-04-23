---
title: Azure Snabbstart – Säkerhetskopiera en virtuell dator med PowerShell | Microsoft Docs
description: Lär dig hur du säkerhetskopierar virtuella datorer med Azure PowerShell
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: ''
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 2/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 91303368f96b477f0756e4d3b9d19601797f0ba8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Säkerhetskopiera en virtuell dator med PowerShell
Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Med Azure Backup skapas återställningspunkter som kan lagras i geo-redundanta återställningsvalv. Den här artikeln beskriver hur du säkerhetskopierar en virtuell dator med Azure PowerShell-modulen. Du kan också utföra dessa steg med [Azure CLI](quick-backup-vm-cli.md) eller [Azure Portal](quick-backup-vm-portal.md).

I den här snabbstarten sker säkerhetskopieringen på en befintlig virtuell Azure-dator. Om du behöver skapa en virtuell dator kan du [skapa en virtuell dator med Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Den här snabbstarten kräver Azure PowerShell-modul version 4.4 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

Första gången du använder Azure Backup måste du registrera Azure Recovery Services-providern i din prenumeration med [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vaults"></a>Skapa ett Recovery Services-valv
Recovery Services-valvet är en logisk behållare som lagrar säkerhetskopierade data för varje skyddad resurs, till exempel virtuella Azure-datorer. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Skapa ett Recovery Services-valv med [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Ange samma resursgrupp och plats som den virtuella datorn som du vill skydda. Om du använde [exempelskriptet](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) för att skapa din virtuella dator heter resursgruppen *myResourceGroup*, den virtuella datorn *myVM* och resurserna finns på platsen *WestEurope*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

Valvet är som standard inställt på geo-redundant lagring. Den här lagringsredundansnivån ger ett extra skydd genom att dina säkerhetskopierade data replikeras till en sekundär Azure-region som ligger hundratals kilometer från den primära regionen.

Om du vill använda det här valvet i de återstående stegen anger du valvkontexten med [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext)

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Aktivera säkerhetskopiering för en virtuell Azure-dator
Du skapar och använder principer för att definiera när ett säkerhetskopieringsjobb ska köras och hur länge återställningspunkterna ska sparas. Med standardskyddsprincipen körs ett säkerhetskopieringsjobb varje dag och återställningspunkterna sparas i 30 dagar. Du kan använda standardvärdena för att snabbt skydda din virtuella dator. Börja med att ange standardprincipen med [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy):

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Du använder [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection) för att aktivera säkerhetskopiering för en virtuell dator. Ange vilken princip som ska användas och sedan vilken resursgrupp och virtuell dator som ska skyddas:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb
Du kan starta en säkerhetskopiering nu, i stället för att vänta tills jobbet körs vid standardprincipens schemalagda tidpunkt, med hjälp av [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). När det första säkerhetskopieringsjobbet körs skapas en fullständig återställningspunkt. Vid varje säkerhetskopiering, efter den första säkerhetskopieringen, skapas inkrementella återställningspunkter. Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

I följande uppsättning kommandon anger du en behållare i Recovery Services-valvet som ska förvara dina säkerhetskopieringsdata med [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Varje virtuell dator som säkerhetskopieras hanteras som ett objekt. För att kunna starta ett säkerhetskopieringsjobb behöver du hämta information om ditt virtuella datorobjekt med [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Eftersom en fullständig återställningspunkt skapas vid den första säkerhetskopieringen kan den här processen ta upp till 20 minuter.


## <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen
Du kan övervaka status för säkerhetskopieringsjobb med [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob):

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

De utdata som returneras liknar dem i följande exempel, som visar att säkerhetskopieringsjobbet har status **Pågår**:

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

När *status* för säkerhetskopieringsjobbet är *Slutförd* skyddas din virtuella dator med Recovery Services och en fullständig återställningspunkt har sparats.


## <a name="clean-up-deployment"></a>Rensa distribution
När det inte längre behövs kan du inaktivera skyddet av den virtuella datorn, ta bort återställningspunkterna och Recovery Services-valvet och sedan ta bort resursgruppen och de relaterade virtuella datorresurserna. Om du använde en befintlig virtuell dator kan du hoppa över den sista [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)-cmdleten och ha kvar resursgruppen och den virtuella datorn.

Om du tänker gå vidare till en säkerhetskopieringskurs som förklarar hur du återställer data för din virtuella dator kan du hoppa över stegen i det här avsnittet och gå till [Nästa steg](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skapat ett Recovery Services-valv, aktiverat skydd på en virtuell dator och skapat den första återställningspunkten. Om du vill lära dig mer om Azure Backup och Recovery Services kan du gå vidare till självstudiekurserna.

> [!div class="nextstepaction"]
> [Säkerhetskopiera flera virtuella Azure-datorer](./tutorial-backup-vm-at-scale.md)
