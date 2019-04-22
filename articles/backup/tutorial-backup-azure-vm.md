---
title: Säkerhetskopiera flera virtuella Azure-datorer med PowerShell
description: Den här självstudiekursen förklarar hur du säkerhetskopierar flera virtuella Azure-datorer till ett Recovery Services-valv med hjälp av Azure PowerShell.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2a35435f56a4bb09a8a1958fbc175ef7c889c380
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58863023"
---
# <a name="back-up-azure-vms-with-powershell"></a>Säkerhetskopiera virtuella Azure-datorer med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här självstudien beskrivs hur du distribuerar en [Azure Backup](backup-overview.md) Recovery Services-valvet för att säkerhetskopiera flera virtuella Azure-datorer med hjälp av PowerShell.  

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett Recovery Services-valv och ange valvets sammanhang.
> * definierar en säkerhetskopieringspolicy
> * applicerar säkerhetskopieringspolicyn för att skydda flera virtuella datorer
> * Utlösa en säkerhetskopiering på begäran för de skyddade virtuella datorerna innan du kan säkerhetskopiera (eller skydda) en virtuell dator måste du slutföra de [krav](backup-azure-arm-vms-prepare.md) att förbereda miljön för att skydda dina virtuella datorer. 

> [!IMPORTANT]
> Den här självstudien förutsätter att du redan har skapat en resursgrupp och en virtuell Azure-dator.


## <a name="log-in-and-register"></a>Logga in och registrera


1. Logga in på Azure-prenumerationen med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

    ```powershell
    Connect-AzAccount
    ```
2. Första gången du använder Azure Backup, måste du registrera Azure Recovery Services-providern i din prenumeration med [registrera AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Hoppa över det här steget om du redan har registrerat.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

En [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhetskopieringsjobb körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.


- I den här självstudien skapar du valvet i samma resursgrupp och plats som den virtuella datorn som du vill säkerhetskopiera.
- Azure Backup hanterar automatiskt lagring för säkerhetskopierade data. Valvet använder som standard [Geo-Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). GEO-redundans garanterar att säkerhetskopierade data replikeras till en sekundär Azure region hundratals mil bort från den primära regionen.

Skapa valvet på följande sätt:

1. Använd den [New AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)att skapa valvet. Ange resursgruppens namn och plats för den virtuella datorn som du vill säkerhetskopiera.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. Anger du valvkontexten med [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   - Valvets sammanhang är typen av data som skyddas i valvet.
   - När kontexten har angetts gäller som för alla efterföljande cmdletar

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Säkerhetskopiera virtuella Azure-datorer

Säkerhetskopiering köras i enlighet med det schema som angetts i säkerhetskopieringsprincipen. När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning.

- Principen för standardskydd utlöser ett säkerhetskopieringsjobb en gång om dagen vid en viss tidpunkt.
- Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar. 

För att aktivera och in Azure VM-säkerhetskopiering på den här självstudien gör vi följande:

1. Ange en behållare i valvet som innehåller dina säkerhetskopierade data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Varje virtuell dator för säkerhetskopiering är en artikel. Om du vill starta ett säkerhetskopieringsjobb du få information om den virtuella datorn med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Köra en ad hoc-säkerhetskopiering med[Backup AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem). 
    - Det första första säkerhetskopieringsjobbet skapar en fullständig återställningspunkt.
    - Efter den första säkerhetskopian skapas varje säkerhetskopiering inkrementella återställningspunkter.
    - Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

Aktivera och köra säkerhetskopieringen på följande sätt:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Felsökning 

Om du får problem när du säkerhetskopierar den virtuella datorn kan du granska det [felsökningsartikeln](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

Om du vill ta bort ett valv kan först ta bort återställningspunkter i valvet och avregistrera valvet, enligt följande:


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Nästa steg

- [Granska](backup-azure-vms-automation.md) en mer detaljerad genomgång av säkerhetskopiera och återställa virtuella datorer i Azure med PowerShell. 
- [Hantera och övervaka virtuella Azure-datorer](backup-azure-manage-vms.md)
- [Återställa virtuella datorer i Azure](backup-azure-arm-restore-vms.md)
