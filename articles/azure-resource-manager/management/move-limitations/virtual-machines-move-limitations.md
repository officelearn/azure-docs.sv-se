---
title: Flytta virtuella Azure-datorer till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta virtuella datorer till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 3878113f6874c40953bec87518a89519bdc6cb1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230967"
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
   2. Hitta en resurs grupp med följande namn mönster: `AzureBackupRG_<VM location>_1` . Namnet har till exempel formatet *AzureBackupRG_westus2_1*.
   3. I Azure Portal markerar du **Visa dolda typer**.
   4. Hitta resursen med typen **Microsoft. Compute/restorePointCollections** som har namngivnings mönstret `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.
   6. När borttagnings åtgärden har slutförts kan du flytta den virtuella datorn.

3. Flytta den virtuella datorn till mål resurs gruppen.
4. Återuppta säkerhets kopieringen.

### <a name="powershell"></a>PowerShell

1. Hitta platsen för den virtuella datorn.

1. Hitta en resurs grupp med namngivnings mönstret – `AzureBackupRG_<VM location>_1` . Namnet kan till exempel vara `AzureBackupRG_westus2_1` .

1. Använd följande kommando för att hämta återställnings punkt samlingen.

   ```azurepowershell
   $RestorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

1. Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

   ```azurepowershell
   Remove-AzResource -ResourceId $RestorePointCollection.ResourceId -Force
   ```

### <a name="azure-cli"></a>Azure CLI

1. Hitta platsen för den virtuella datorn.

1. Hitta en resurs grupp med namngivnings mönstret – `AzureBackupRG_<VM location>_1` . Namnet kan till exempel vara `AzureBackupRG_westus2_1` .

1. Använd följande kommando för att hämta återställnings punkt samlingen.

   ```azurecli
   az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections
   ```

1. Hitta resurs-ID för resursen med namngivnings mönstret `AzureBackup_<VM name>_###########`

1. Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

   ```azurecli
   az resource delete --ids /subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.Compute/restorePointCollections/<name>
   ```

## <a name="next-steps"></a>Nästa steg

* För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).

* Information om hur du flyttar Recovery Service-valv för säkerhets kopiering finns i [Recovery Services begränsningar](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
