---
title: Flytta virtuella Azure-datorer till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta virtuella datorer till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: d522eb4a6496bc2cc65b4937a19b9ac5228e7f2b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933247"
---
# <a name="move-guidance-for-virtual-machines"></a>Flytta vägledning för virtuella datorer

I den här artikeln beskrivs scenarier som inte stöds för närvarande och stegen för att flytta virtuella datorer med säkerhets kopiering.

## <a name="scenarios-not-supported"></a>Scenarier som inte stöds

Följande scenarier stöds inte än:

* Virtual Machine Scale Sets med standard-SKU Load Balancer eller offentlig IP-adress för standard-SKU: n kan inte flyttas.
* Virtuella datorer som skapats från Marketplace-resurser med anslutna planer kan inte flyttas mellan prenumerationer. Avetablera den virtuella datorn i den aktuella prenumerationen och distribuera igen i den nya prenumerationen.
* Det går inte att flytta virtuella datorer i ett befintligt virtuellt nätverk till en ny prenumeration när du inte flyttar alla resurser i det virtuella nätverket.
* Det går inte att flytta virtuella datorer med låg prioritet och den virtuella datorns skalnings uppsättningar med låg prioritet flyttas över resurs grupper eller prenumerationer.
* Det går inte att flytta virtuella datorer i en tillgänglighets uppsättning individuellt.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Du kan inte flytta en virtuell dator som är integrerad med ett nyckel valv för att implementera [Azure Disk Encryption för virtuella Linux-datorer](../../../virtual-machines/linux/disk-encryption-overview.md) eller [Azure Disk Encryption för virtuella Windows-datorer](../../../virtual-machines/windows/disk-encryption-overview.md). Om du vill flytta den virtuella datorn måste du inaktivera kryptering.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Virtuella datorer med Azure Backup

Om du vill flytta virtuella datorer som kon figurer ATS med Azure Backup måste du ta bort återställnings punkterna från valvet.

Om [mjuk borttagning](../../../backup/backup-azure-security-feature-cloud.md) är aktiverat för den virtuella datorn kan du inte flytta den virtuella datorn medan återställnings punkterna behålls. [Inaktivera mjuk borttagning](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) eller vänta 14 dagar efter att återställnings punkterna har tagits bort.

### <a name="portal"></a>Portalen

1. Stoppa säkerhets kopieringen tillfälligt och behåll säkerhets kopierings data.
2. Gör så här om du vill flytta virtuella datorer som kon figurer ATS med Azure Backup:

   1. Hitta platsen för den virtuella datorn.
   2. Hitta en resurs grupp med följande namn mönster: `AzureBackupRG_<location of your VM>_1` . Till exempel *AzureBackupRG_westus2_1*
   3. I Azure Portal markerar du **Visa dolda typer**.
   4. Hitta resursen med typen **Microsoft. Compute/restorePointCollections** som har namngivnings mönstret `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.
   6. När borttagnings åtgärden har slutförts kan du flytta den virtuella datorn.

3. Flytta den virtuella datorn till mål resurs gruppen.
4. Återuppta säkerhets kopieringen.

### <a name="powershell"></a>PowerShell

* Hitta platsen för den virtuella datorn.
* Hitta en resurs grupp med följande namn mönster: till `AzureBackupRG_<location of your VM>_1` exempel AzureBackupRG_westus2_1
* Om i PowerShell använder du `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdleten
* Hitta resursen med den typ `Microsoft.Compute/restorePointCollections` som har namngivnings mönstret `AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

### <a name="azure-cli"></a>Azure CLI

* Hitta platsen för den virtuella datorn.
* Hitta en resurs grupp med följande namn mönster: till `AzureBackupRG_<location of your VM>_1` exempel AzureBackupRG_westus2_1
* Om i CLI använder du `az resource list -g AzureBackupRG_<location of your VM>_1`
* Hitta resursen med den typ `Microsoft.Compute/restorePointCollections` som har namngivnings mönstret `AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

## <a name="next-steps"></a>Nästa steg

* För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).

* Information om hur du flyttar Recovery Service-valv för säkerhets kopiering finns i [Recovery Services begränsningar](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
