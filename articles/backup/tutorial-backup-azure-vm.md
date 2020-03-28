---
title: 'Självstudiekurs: Flera Azure VM-säkerhetskopieringar med PowerShell'
description: Den här självstudien innehåller information om säkerhetskopiering av flera virtuella Azure-datorer till ett Recovery Services-valv med Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171786"
---
# <a name="back-up-azure-vms-with-powershell"></a>Säkerhetskopiera virtuella Azure-datorer med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här självstudien beskriver hur du distribuerar ett [Azure Backup](backup-overview.md) Recovery Services-valv för att säkerhetskopiera flera Virtuella Azure-datorer med PowerShell.  

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
>
> * Skapa ett Recovery Services-valv och ange valvkontexten.
> * Definierar en säkerhetskopieringspolicy
> * applicerar säkerhetskopieringspolicyn för att skydda flera virtuella datorer
> * Utlösa ett säkerhetskopieringsjobb på begäran för skyddade virtuella datorer Innan du kan säkerhetskopiera (eller skydda) en virtuell dator måste du slutföra [förutsättningarna](backup-azure-arm-vms-prepare.md) för att förbereda din miljö för att skydda dina virtuella datorer.

> [!IMPORTANT]
> Den här självstudien förutsätter att du redan har skapat en resursgrupp och en virtuell Azure-dator.

## <a name="sign-in-and-register"></a>Logga in och registrera dig

1. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

    ```powershell
    Connect-AzAccount
    ```

2. Första gången du använder Azure Backup måste du registrera Azure Recovery Service-providern i din prenumeration med [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Om du redan har registrerat dig hoppar du över det här steget.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhetskopieringsjobb körs skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

* I den här självstudien skapar du valvet i samma resursgrupp och plats som den virtuella dator som du vill säkerhetskopiera.
* Azure Backup hanterar automatiskt lagring för säkerhetskopierade data. Som standard använder valvet [GEO-Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md). Geo-redundans säkerställer att säkerhetskopierade data replikeras till en sekundär Azure-region, hundratals miles från den primära regionen.

Skapa valvet enligt följande:

1. Använd [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)för att skapa valvet. Ange resursgruppsnamnet och platsen för den virtuella dator som du vill säkerhetskopiera.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Ange valvkontexten med [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * Valvets sammanhang är typen av data som skyddas i valvet.
   * När kontexten har ställts in gäller det för alla efterföljande cmdlets

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Säkerhetskopiera virtuella Azure-datorer

Säkerhetskopior körs i enlighet med det schema som anges i säkerhetskopieringsprincipen. När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning.

* Standardskyddsprincipen utlöser ett säkerhetskopieringsjobb en gång om dagen vid en angiven tidpunkt.
* Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar.

För att aktivera och säkerhetskopiera Den virtuella Azure-datorn i den här självstudien gör vi följande:

1. Ange en behållare i valvet som innehåller dina säkerhetskopierade data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Varje virtuell dator för säkerhetskopiering är ett objekt. Om du vill starta ett säkerhetskopieringsjobb får du information om den virtuella datorn med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Kör en säkerhetskopiering på begäran med[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * Det första första säkerhetskopieringsjobbet skapar en fullständig återställningspunkt.
    * Efter den första säkerhetskopieringen skapar varje säkerhetskopieringsjobb inkrementella återställningspunkter.
    * Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

Aktivera och kör säkerhetskopian på följande sätt:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Felsökning

Om du stöter på problem när du säkerhetskopierar den virtuella datorn läser du den här [felsökningsartikeln](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

Om du behöver ta bort ett valv tar du först bort återställningspunkter i valvet och avregistrerar sedan valvet enligt följande:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Nästa steg

* [Granska](backup-azure-vms-automation.md) en mer detaljerad genomgång av säkerhetskopiering och återställning av virtuella Azure-datorer med PowerShell.
* [Hantera och övervaka virtuella Azure-datorer](backup-azure-manage-vms.md)
* [Återställa virtuella Azure-datorer](backup-azure-arm-restore-vms.md)
