---
title: Flytta virtuella Azure-datorer till ny prenumeration eller resursgrupp
description: Använd Azure Resource Manager för att flytta virtuella datorer till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: df34268b7741f76621c290e9979cf24d828ddc09
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478662"
---
# <a name="move-guidance-for-virtual-machines"></a>Flytta vägledning för virtuella datorer

I den här artikeln beskrivs de scenarier som för närvarande inte stöds och stegen för att flytta virtuella datorer med säkerhetskopiering.

## <a name="scenarios-not-supported"></a>Scenarier stöds inte

Följande scenarier stöds ännu inte:

* Hanterade diskar i tillgänglighetszoner kan inte flyttas till en annan prenumeration.
* Det går inte att flytta standard-SKU-belastningsutjämningsuppsättningar med standard SKU-belastningsutjämning eller standard SKU Public IP.
* Virtuella datorer som skapats från Marketplace-resurser med bifogade planer kan inte flyttas mellan resursgrupper eller prenumerationer. Avetablering den virtuella datorn i den aktuella prenumerationen och distribuera igen i den nya prenumerationen.
* Virtuella datorer i ett befintligt virtuellt nätverk kan inte flyttas till en ny prenumeration när du inte flyttar alla resurser i det virtuella nätverket.
* Virtuella datorer med låg prioritet och skalningsuppsättningar med låg prioritet kan inte flyttas mellan resursgrupper eller prenumerationer.
* Virtuella datorer i en tillgänglighetsuppsättning kan inte flyttas individuellt.

## <a name="virtual-machines-with-azure-backup"></a>Virtuella datorer med Azure Backup

Om du vill flytta virtuella datorer som konfigurerats med Azure Backup måste du ta bort återställningspunkterna från valvet.

Om [mjuk borttagning](../../../backup/backup-azure-security-feature-cloud.md) är aktiverad för den virtuella datorn kan du inte flytta den virtuella datorn medan återställningspunkterna behålls. Inaktivera [antingen mjuk borttagning](../../../backup/backup-azure-security-feature-cloud.md#disabling-soft-delete) eller vänta 14 dagar efter att återställningspunkterna har tagits bort.

### <a name="portal"></a>Portalen

1. Välj den virtuella dator som är konfigurerad för säkerhetskopiering.

1. Välj **Säkerhetskopiering**i den vänstra rutan .

1. Välj **Stoppa säkerhetskopiering**.

1. Välj **Ta bort tillbakadata**.

1. När borttagningen är klar kan du flytta valvet och den virtuella datorn till målprenumerationen. Efter flytten kan du fortsätta säkerhetskopiorna.

### <a name="powershell"></a>PowerShell

* Hitta platsen för din virtuella dator.
* Hitta en resursgrupp med följande `AzureBackupRG_<location of your VM>_1` namngivningsmönster: till exempel AzureBackupRG_westus2_1
* Om du använder cmdleten `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` i PowerShell
* Hitta resursen `Microsoft.Compute/restorePointCollections` med typ som har namngivningsmönstret`AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bara bort omedelbara återställningspunkter, inte säkerhetskopierade data i valvet.

### <a name="azure-cli"></a>Azure CLI

* Hitta platsen för din virtuella dator.
* Hitta en resursgrupp med följande `AzureBackupRG_<location of your VM>_1` namngivningsmönster: till exempel AzureBackupRG_westus2_1
* Om du är i CLI, använd`az resource list -g AzureBackupRG_<location of your VM>_1`
* Hitta resursen `Microsoft.Compute/restorePointCollections` med typ som har namngivningsmönstret`AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bara bort omedelbara återställningspunkter, inte säkerhetskopierade data i valvet.

## <a name="next-steps"></a>Nästa steg

* Kommandon som ska flyttas finns i [Flytta resurser till ny resursgrupp eller prenumeration](../move-resource-group-and-subscription.md).

* Information om hur du flyttar Recovery Service-valv för säkerhetskopiering finns i [Begränsningar för Återställningstjänster](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
