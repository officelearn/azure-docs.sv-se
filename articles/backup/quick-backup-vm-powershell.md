---
title: Snabbstart – Säkerhetskopiera en virtuell dator med PowerShell
description: I den här snabbstarten kan du läsa om hur du säkerhetskopierar dina virtuella Azure-datorer med Azure PowerShell-modulen.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74171952"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Säkerhetskopiera en virtuell dator med PowerShell

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) AZ-modulen används för att skapa och hantera Azure-resurser från kommandoraden eller skript.

[Azure Backup](backup-overview.md) säkerhetskopierar lokala datorer och appar och virtuella Azure-datorer. Den här artikeln visar hur du säkerhetskopierar en Azure VM med AZ-modulen. Du kan också säkerhetskopiera en virtuell dator med [Azure CLI](quick-backup-vm-cli.md)eller i [Azure-portalen](quick-backup-vm-portal.md).

I den här snabbstarten sker säkerhetskopieringen på en befintlig virtuell Azure-dator. Om du behöver skapa en virtuell dator kan du [skapa en virtuell dator med Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Den här snabbstarten kräver Azure PowerShell AZ-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Logga in och registrera dig

1. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

    ```powershell
    Connect-AzAccount
    ```

2. Första gången du använder Azure Backup måste du registrera Azure Recovery Service-providern i din prenumeration med [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider)enligt följande:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhetskopieringsjobb körs skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

När du skapar valvet:

- För resursgruppen och platsen anger du resursgruppen och platsen för den virtuella dator som du vill säkerhetskopiera.
- Om du använde det här [exempelskriptet](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) för att skapa den virtuella datorn är resursgruppen **myResourceGroup**, den virtuella datorn ***myVM**och resurserna finns i regionen **WestEurope.**
- Azure Backup hanterar automatiskt lagring för säkerhetskopierade data. Som standard använder valvet [GEO-Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md). Geo-redundans säkerställer att säkerhetskopierade data replikeras till en sekundär Azure-region, hundratals miles från den primära regionen.

Nu skapa ett valv:

1. Använd [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) för att skapa valvet:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Ange valvkontexten med [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)enligt följande:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Ändra konfigurationen för lagringsredundans (LRS/GRS) i valvet med [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)enligt följande:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Lagringsredundans kan bara ändras om det inte finns några säkerhetskopior som skyddas till valvet.

## <a name="enable-backup-for-an-azure-vm"></a>Aktivera säkerhetskopiering för en virtuell Azure-dator

Du aktiverar säkerhetskopiering för en Virtuell Azure-dator och anger en princip för säkerhetskopiering.

- Principen definierar när säkerhetskopieringar körs och hur länge återställningspunkter som skapas av säkerhetskopiorna ska behållas.
- Standardskyddsprincipen kör en säkerhetskopia en gång om dagen för den virtuella datorn och behåller de skapade återställningspunkterna i 30 dagar. Du kan använda den här standardprincipen för att snabbt skydda din virtuella dator.

Aktivera säkerhetskopiering enligt följande:

1. Ange först standardprincipen med [Get-AzRecoveryServicesBackupProtectionPolicy:](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Aktivera vm-säkerhetskopiering med [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Ange principen, resursgruppen och vm-namnet.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb

Säkerhetskopior körs enligt det schema som anges i säkerhetskopieringsprincipen. Du kan också köra en säkerhetskopiering på begäran:

- Det första första säkerhetskopieringsjobbet skapar en fullständig återställningspunkt.
- Efter den första säkerhetskopieringen skapar varje säkerhetskopieringsjobb inkrementella återställningspunkter.
- Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

Om du vill köra en säkerhetskopiering på begäran använder du [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Du anger en behållare i valvet som innehåller dina säkerhetskopierade data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Varje virtuell dator som säkerhetskopieras hanteras som ett objekt. Om du vill starta ett säkerhetskopieringsjobb får du information om den virtuella datorn med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Kör ett säkerhetskopieringsjobb på begäran enligt följande:

1. Ange behållaren, hämta VM-information och kör säkerhetskopian.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Du kan behöva vänta upp till 20 minuter, eftersom det första säkerhetskopieringsjobbet skapar en fullständig återställningspunkt. Övervaka jobbet enligt beskrivningen i nästa procedur.

## <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen

1. Kör [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) för att övervaka jobbstatusen.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Utdata liknar följande exempel, som visar jobbet som **InProgress**:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. När jobbstatusen **är slutförd**skyddas den virtuella datorn och har en fullständig återställningspunkt lagrad.

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
