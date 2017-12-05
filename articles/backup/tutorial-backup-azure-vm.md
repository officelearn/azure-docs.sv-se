---
title: "Säkerhetskopiera virtuella Azure-datorer i Azure i skala | Microsoft Docs"
description: "Den här självstudiekursen information säkerhetskopiera flera virtuella Azure-datorer till Recovery Services-valvet."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "säkerhetskopiering av virtuella datorer Säkerhetskopiera virtuella; säkerhetskopiering och katastrofåterställning"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 01609c00c6f0585eff4843932b9eb7a090a59c19
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Säkerhetskopiera virtuella Azure-datorer i Azure i skala

Den här självstudiekursen beskrivs hur du säkerhetskopierar virtuella Azure-datorer till Recovery Services-valvet. Det mesta av arbetet för att säkerhetskopiera virtuella datorer är förberedelserna. Innan du kan säkerhetskopiera upp (eller skydda) en virtuell dator måste du slutföra de [krav](backup-azure-arm-vms-prepare.md) att förbereda miljön för att skydda dina virtuella datorer. 

> [!IMPORTANT]
> Den här kursen förutsätter att du redan har skapat en resursgrupp och en virtuell Azure-dator.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

En [Recovery Services-valvet](backup-azure-recovery-services-vault-overview.md) är en behållare som innehåller återställningspunkter för objekten som säkerhetskopieras. Recovery Services-valvet är en Azure-resurs som kan distribueras och hanteras som en del av en Azure-resursgrupp. I kursen får skapa du ett Recovery Services-valv i samma resursgrupp som den virtuella datorn som skyddas.


Första gången du använder Azure-säkerhetskopiering, måste du registrera providern Azure Recovery-tjänsten med din prenumeration. Om du redan har registrerat providern med din prenumeration går du till nästa steg.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Skapa ett Recovery Services-valv med **New-AzureRmRecoveryServicesVault**. Se till att ange resursgruppens namn och plats som används när du konfigurerar den virtuella dator som du vill säkerhetskopiera. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Många Azure Backup-cmdletar kräver Recovery Services-valvet objekt som indata. Därför är det praktiskt att lagra säkerhetskopian Recovery Services-valvet objekt i en variabel. Använd sedan **Set AzureRmRecoveryServicesBackupProperties** att ange den **- BackupStorageRedundancy** att [Geo-Redundant lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Säkerhetskopiera virtuella Azure-datorer

Innan du kan köra den första säkerhetskopian, måste du ange kontexten valvet. Kontexten valvet är typ av data som skyddas i valvet. När du skapar ett Recovery Services-valv kommer med standardskydd och bevarandeprinciper. Standardprincipen för protection utlöser en säkerhetskopiering varje dag vid en viss tidpunkt. Bevarandeprincip standard behåller den dagliga återställningspunkten för 30 dagar. Acceptera standardprincipen för den här kursen. 

Använd  **[Set AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**  ange kontexten valvet. När kontexten valvet har angetts gäller alla efterföljande cmdletar. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Använd  **[säkerhetskopiering AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)**  att utlösa säkerhetskopieringsjobbet. Säkerhetskopieringsjobbet skapar en återställningspunkt. Om det är den första säkerhetskopian är en fullständig säkerhetskopiering med återställningspunkten. Efterföljande säkerhetskopieringar skapa en inkrementell kopia.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Ta bort Recovery Services-valvet

Om du vill ta bort Recovery Services-valvet måste du först ta bort alla återställningspunkter i valvet och avregistrera valvet. Följande kommandon i detalj de här stegen. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Felsökning av fel
Om du stöter på problem när säkerhetskopiera den virtuella datorn finns på [säkerhetskopiera virtuella datorer i Azure artikeln Felsöka](backup-azure-vms-troubleshoot.md) för hjälp.

## <a name="next-steps"></a>Nästa steg
Nu när du har skyddat de virtuella datorerna finns i följande artiklar om du vill veta mer om administrativa uppgifter och återställa virtuella datorer från en återställningspunkt.

* Om du vill ändra principen för säkerhetskopiering finns [Använd AzureRM.RecoveryServices.Backup-cmdletar för att säkerhetskopiera virtuella datorer](backup-azure-vms-automation.md#create-a-protection-policy).
* [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md)
* [Återställa virtuella datorer](backup-azure-arm-restore-vms.md)
