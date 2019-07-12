---
title: Flytta Azure virtuella datorer till ny prenumeration eller resursgrupp grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta virtuella datorer till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723550"
---
# <a name="move-guidance-for-virtual-machines"></a>Flytta vägledning för virtuella datorer

Den här artikeln beskrivs de scenarier som inte stöds för närvarande och stegen för att flytta virtuella datorer med backup.

## <a name="scenarios-not-supported"></a>Scenarier som inte stöds

Följande scenarier stöds inte ännu:

* Hanterade diskar i Availability Zones kan inte flyttas till en annan prenumeration.
* Virtuella datorer med certifikat som lagras i Key Vault kan flyttas till en ny resursgrupp i samma prenumeration, men inte mellan prenumerationer.
* Virtual Machine Scale Sets med Standard-SKU-belastningsutjämnare eller SKU offentlig IP kan inte flyttas.
* Virtuella datorer som skapats från Marketplace-resurser med anslutna-planer kan inte flyttas mellan resursgrupper eller prenumerationer. Avetablera den virtuella datorn i den aktuella prenumerationen och distribuera igen i den nya prenumerationen.
* Virtuella datorer i ett befintligt virtuellt nätverk, men du flytta inte alla resurser i det virtuella nätverket.

## <a name="virtual-machines-with-azure-backup"></a>Virtuella datorer med Azure Backup

Om du vill flytta virtuella datorer som konfigurerats med Azure Backup, använder du följande lösning:

* Hitta platsen för den virtuella datorn.
* Hitta en resursgrupp med följande namngivningsmönstret: `AzureBackupRG_<location of your VM>_1` exempelvis AzureBackupRG_westus2_1
* Azure-portalen och sedan kontrollera om ”Visa dolda typer”
* Om du är i PowerShell, använder de `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Om du är i CLI, använder den `az resource list -g AzureBackupRG_<location of your VM>_1`
* Hitta resursen med typen `Microsoft.Compute/restorePointCollections` som har namngivningsmönstret `AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bort bara de direkta återställningspunkterna, inte säkerhetskopierade data i valvet.
* När delete är klar kan flytta du valv och virtuell dator till målprenumerationen. Du kan fortsätta säkerhetskopieringar utan att förlora data efter överflyttningen.
* Information om hur du flyttar Recovery Service-valv för säkerhetskopiering finns i [begränsningar för Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Nästa steg

Kommandon att flytta resurser finns i [flytta resurser till ny resursgrupp eller prenumeration](../resource-group-move-resources.md).
