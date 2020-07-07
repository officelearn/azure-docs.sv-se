---
title: Flytta virtuella Azure-datorer till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta virtuella datorer till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: e5bd004b6619db9c9882b8e9e6005309317b8ca5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744641"
---
# <a name="move-guidance-for-virtual-machines"></a>Flytta vägledning för virtuella datorer

I den här artikeln beskrivs scenarier som inte stöds för närvarande och stegen för att flytta virtuella datorer med säkerhets kopiering.

## <a name="scenarios-not-supported"></a>Scenarier som inte stöds

Följande scenarier stöds inte än:

* Det går inte att flytta Managed Disks i Tillgänglighetszoner till en annan prenumeration.
* Virtual Machine Scale Sets med standard-SKU Load Balancer eller offentlig IP-adress för standard-SKU: n kan inte flyttas.
* Virtuella datorer som skapats från Marketplace-resurser med anslutna planer kan inte flyttas över resurs grupper eller prenumerationer. Avetablera den virtuella datorn i den aktuella prenumerationen och distribuera igen i den nya prenumerationen.
* Det går inte att flytta virtuella datorer i ett befintligt virtuellt nätverk till en ny prenumeration när du inte flyttar alla resurser i det virtuella nätverket.
* Det går inte att flytta virtuella datorer med låg prioritet och den virtuella datorns skalnings uppsättningar med låg prioritet flyttas över resurs grupper eller prenumerationer.
* Det går inte att flytta virtuella datorer i en tillgänglighets uppsättning individuellt.

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
* Hitta resursen med den typ `Microsoft.Compute/restorePointCollections` som har namngivnings mönstret`AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

### <a name="azure-cli"></a>Azure CLI

* Hitta platsen för den virtuella datorn.
* Hitta en resurs grupp med följande namn mönster: till `AzureBackupRG_<location of your VM>_1` exempel AzureBackupRG_westus2_1
* Om i CLI använder du`az resource list -g AzureBackupRG_<location of your VM>_1`
* Hitta resursen med den typ `Microsoft.Compute/restorePointCollections` som har namngivnings mönstret`AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.

## <a name="next-steps"></a>Nästa steg

* För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).

* Information om hur du flyttar Recovery Service-valv för säkerhets kopiering finns i [Recovery Services begränsningar](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
