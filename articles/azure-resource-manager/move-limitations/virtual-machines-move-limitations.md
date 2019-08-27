---
title: Flytta Azure-Virtual Machines till en ny prenumeration eller resurs grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta virtuella datorer till en ny resurs grupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 7b9cce7ac367f42329e3198c75a7640a205d01fe
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035529"
---
# <a name="move-guidance-for-virtual-machines"></a>Flytta vägledning för virtuella datorer

I den här artikeln beskrivs scenarier som inte stöds för närvarande och stegen för att flytta virtuella datorer med säkerhets kopiering.

## <a name="scenarios-not-supported"></a>Scenarier som inte stöds

Följande scenarier stöds inte än:

* Det går inte att flytta Managed Disks i Tillgänglighetszoner till en annan prenumeration.
* Virtual Machine Scale Sets med standard-SKU Load Balancer eller offentlig IP-adress för standard-SKU: n kan inte flyttas.
* Virtuella datorer som skapats från Marketplace-resurser med anslutna-planer kan inte flyttas mellan resursgrupper eller prenumerationer. Avetablera den virtuella datorn i den aktuella prenumerationen och distribuera igen i den nya prenumerationen.
* Virtuella datorer i ett befintligt virtuellt nätverk, men du flyttar inte alla resurser i det virtuella nätverket.
* Det går inte att flytta virtuella datorer med låg prioritet och den virtuella datorns skalnings uppsättningar med låg prioritet flyttas över resurs grupper eller prenumerationer.

## <a name="virtual-machines-with-azure-backup"></a>Virtuella datorer med Azure Backup

Om du vill flytta virtuella datorer som kon figurer ATS med Azure Backup använder du följande lösning:

* Hitta platsen för den virtuella datorn.
* Hitta en resurs grupp med följande namn mönster: `AzureBackupRG_<location of your VM>_1` till exempel AzureBackupRG_westus2_1
* Azure-portalen och sedan kontrollera om ”Visa dolda typer”
* Om du är i PowerShell, använder de `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Om du är i CLI, använder den `az resource list -g AzureBackupRG_<location of your VM>_1`
* Hitta resursen med den typ `Microsoft.Compute/restorePointCollections` som har namngivnings mönstret`AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bara bort direkta återställnings punkter, inte säkerhetskopierade data i valvet.
* När borttagningen är klar kan du flytta valvet och den virtuella datorn till mål prenumerationen. Efter flytten kan du fortsätta att säkerhetskopiera utan att data går förlorade.
* Information om hur du flyttar Recovery Service-valv för säkerhets kopiering finns i [Recovery Services begränsningar](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../resource-group-move-resources.md).
