---
title: Flytta virtuella Azure-datorer till ny prenumeration eller resursgrupp
description: Använd Azure Resource Manager för att flytta virtuella datorer till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 97c49f90dab2aafd89de322e57ad44ff1fc9d367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479766"
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

Om du vill flytta virtuella datorer som konfigurerats med Azure Backup använder du följande lösning:

* Hitta platsen för din virtuella dator.
* Hitta en resursgrupp med följande `AzureBackupRG_<location of your VM>_1` namngivningsmönster: till exempel AzureBackupRG_westus2_1
* Om du är i Azure-portalen kontrollerar du "Visa dolda typer"
* Om du använder cmdleten `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` i PowerShell
* Om du är i CLI, använd`az resource list -g AzureBackupRG_<location of your VM>_1`
* Hitta resursen `Microsoft.Compute/restorePointCollections` med typ som har namngivningsmönstret`AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bara bort omedelbara återställningspunkter, inte säkerhetskopierade data i valvet.
* När borttagningen är klar kan du flytta valvet och den virtuella datorn till målprenumerationen. Efter flytten kan du fortsätta säkerhetskopiorna utan förlust av data.
* Information om hur du flyttar Recovery Service-valv för säkerhetskopiering finns i [Begränsningar för Återställningstjänster](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Nästa steg

Kommandon som ska flyttas finns i [Flytta resurser till ny resursgrupp eller prenumeration](../move-resource-group-and-subscription.md).
