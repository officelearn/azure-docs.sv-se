---
title: Azure Snabbstart – Säkerhetskopiera en virtuell dator med PowerShell
description: Lär dig hur du säkerhetskopierar virtuella datorer med Azure PowerShell
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 05432f5a38c3d907afa95ac7b1b3adfe9c5515fe
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236333"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Säkerhetskopiera en virtuell dator med PowerShell

Den [Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) modulen används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. 

[Azure Backup](backup-overview.md) säkerhetskopierar lokala datorer och appar och virtuella Azure-datorer. Den här artikeln visar hur du säkerhetskopierar en virtuell Azure-dator med AZ-modulen. Du kan också säkerhetskopiera en virtuell dator med den [Azure CLI](quick-backup-vm-cli.md), eller i den [Azure-portalen](quick-backup-vm-portal.md).

I den här snabbstarten sker säkerhetskopieringen på en befintlig virtuell Azure-dator. Om du behöver skapa en virtuell dator kan du [skapa en virtuell dator med Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Den här snabbstarten kräver Azure PowerShell-AZ Modulversion 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Logga in och registrera

1. Logga in på Azure-prenumerationen med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

    ```powershell
    Connect-AzAccount
    ```
2. Första gången du använder Azure Backup, måste du registrera Azure Recovery Services-providern i din prenumeration med [registrera AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), enligt följande:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

En [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhetskopieringsjobb körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

När du skapar valvet:

- Ange resursgrupp och plats för den virtuella datorn som du vill säkerhetskopiera för resursgrupp och plats.
- Om du har använt detta [exempel på skript](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) för att skapa den virtuella datorn, resursgruppen är **myResourceGroup**, den virtuella datorn är ***myVM**, och resurserna finns på den **WestEurope**  region.
- Azure Backup hanterar automatiskt lagring för säkerhetskopierade data. Valvet använder som standard [Geo-Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). GEO-redundans garanterar att säkerhetskopierade data replikeras till en sekundär Azure region hundratals mil bort från den primära regionen.

Nu ska du skapa ett valv:


1. Använd den [New AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) att skapa valvet:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Anger du valvkontexten med [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), enligt följande:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Ändra storage redundans-konfiguration (LRS/GRS) valvet med [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty), enligt följande:
    
    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```
    > [!NOTE]
    > Lagringsredundans kan ändras om det finns inga objekt att säkerhetskopiera skyddas i valvet.

## <a name="enable-backup-for-an-azure-vm"></a>Aktivera säkerhetskopiering för en virtuell Azure-dator

Du aktiverar säkerhetskopiering för en Azure virtuell dator och ange en princip för säkerhetskopiering.

- Principen definierar när säkerhetskopieringar ska köras och hur länge återställningspunkter som skapats av säkerhetskopiorna ska behållas.
- Med standardskyddsprincipen körs en gång per dag för en säkerhetskopia för den virtuella datorn och behåller skapade återställningspunkter i 30 dagar. Du kan använda den här standardprincipen för att snabbt skydda den virtuella datorn. 

Aktivera säkerhetskopiering på följande sätt:

1. Först ange standardprincipen med [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Aktivera säkerhetskopiering av virtuella datorer med [aktivera AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Ange principen, resursgruppen och VM-namnet.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```


## <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb

Säkerhetskopiering köras i enlighet med det schema som angetts i säkerhetskopieringsprincipen. Du kan också köra en ad hoc-säkerhetskopiering:

- Det första första säkerhetskopieringsjobbet skapar en fullständig återställningspunkt.
- Efter den första säkerhetskopian skapas varje säkerhetskopiering inkrementella återställningspunkter.
- Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

Om du vill köra en ad hoc-säkerhetskopiering, som du använder den [Backup AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem). 
- Anger du en behållare i valvet som innehåller dina säkerhetskopierade data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Varje virtuell dator som säkerhetskopieras hanteras som ett objekt. Om du vill starta ett säkerhetskopieringsjobb du få information om den virtuella datorn med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Kör en ad hoc-säkerhetskopiering på följande sätt:

1. Anger behållaren, hämta information för virtuell dator och köra säkerhetskopieringen.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Du kan behöva vänta upp till 20 minuter, eftersom det första säkerhetskopieringsjobbet körs skapas en fullständig återställningspunkt. Övervaka jobbet enligt beskrivningen i nästa procedur.


## <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen

1. Kör [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) att övervaka jobbstatusen.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    Utdata liknar följande exempel som visar jobbet som **InProgress**:

    ```
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. När jobbet har statusen är **slutförd**, den virtuella datorn är skyddad och har en fullständig återställningspunkt har sparats.


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
