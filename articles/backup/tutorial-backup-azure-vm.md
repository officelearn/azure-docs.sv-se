---
title: Säkerhetskopiera virtuella Azure-datorer i skala
description: Den här självstudiekursen förklarar hur du säkerhetskopierar flera virtuella Azure-datorer till ett Recovery Services-valv.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: virtual machine backup; back up virtual machine; backup and disaster recovery
ms.service: backup
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk
ms.custom: mvc
ms.openlocfilehash: d2b83963f7af52101ed298e85b6c7fd64fc99a07
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875594"
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Säkerhetskopiera Azure Virtual Machines i Azure i skala

Den här självstudiekursen förklarar hur du säkerhetskopierar virtuella Azure-datorer till ett Recovery Services-valv. Det mesta av arbetet för att säkerhetskopiera virtuella datorer är förberedelserna. Innan du kan säkerhetskopiera (eller skydda) en virtuell dator måste du slutföra [förutsättningarna](backup-azure-arm-vms-prepare.md) för att förbereda miljön för att skydda dina virtuella datorer. 

> [!IMPORTANT]
> Den här självstudien förutsätter att du redan har skapat en resursgrupp och en virtuell Azure-dator.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en container som rymmer återställningspunkterna för objekten som säkerhetskopieras. Ett Recovery Services-valv är en Azure-resurs som kan distribueras och hanteras som en del av en Azure-resursgrupp. I den här självstudien skapar du ett Recovery Services-valv i samma resursgrupp som den virtuella datorn som skyddas.


Första gången du använder Azure Backup måste du registrera Azure Recovery Services-providern i din prenumeration. Om du redan har registrerat providern med din prenumeration går du till nästa steg.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Skapa ett Recovery Services-valv med **New-AzureRmRecoveryServicesVault**. Se till att ange resursgruppens namn och den plats som används när du konfigurerar den virtuella datorn som du vill säkerhetskopiera. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel. Använd sedan **Set-AzureRmRecoveryServicesBackupProperties** för att ställa in alternativet **-BackupStorageRedundancy** på [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Säkerhetskopiera virtuella Azure-datorer

Innan du kan köra den första säkerhetskopian måste du ange valvets sammanhang. Valvets sammanhang är typen av data som skyddas i valvet. När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning. Principen för standardskydd utlöser ett säkerhetsjobb varje dag vid en viss tidpunkt. Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar. I den här självstudiekursen accepterar du standardprincipen. 

Använd **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** för att ställa in valvets sammanhang. När valvet sammanhang är inställt gäller det alla efterkommande cmdletar. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Använd **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** för att utlösa säkerhetskopieringsjobbet. Säkerhetskopieringsjobbet skapar en återställningspunkt. Om det är den första säkerhetskopian är återställningspunkten en fullständig säkerhetskopia. Efterföljande säkerhetskopieringar skapar en inkrementell kopia.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Ta bort Recovery Services-valvet

Om du vill ta bort Recovery Services-valvet måste du först ta bort alla återställningspunkter i valvet och avregistrera valvet. Följande kommandon redogör för stegen. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Felsöka fel
Om du stöter på problem när du säkerhetskopierar den virtuella datorn kan du läsa [felsökningsartikeln om att säkerhetskopiera virtuella Azure-datorer](backup-azure-vms-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg
Nu när du har skyddat de virtuella datorerna kan du läsa följande artiklar för att lära dig mer om att hantera uppgifter och återställa virtuella datorer från en återställningspunkt.

* Om du vill ändra principen för säkerhetskopiering läser du informationen om att [använda AzureRM.RecoveryServices.Backup-cmdletar för att säkerhetskopiera virtuella datorer](backup-azure-vms-automation.md#create-a-protection-policy).
* [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md)
* [Återställa virtuella datorer](backup-azure-arm-restore-vms.md)
