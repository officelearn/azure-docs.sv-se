---
title: Aktivera Azure Disk Encryption för Virtual Machine Scale Sets
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0097d0e1d5ea7de092da14683d4bab3d673b2219
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177776"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption för Virtual Machine Scale Sets

Azure Disk Encryption tillhandahåller volym kryptering för operativ system och data diskar för dina virtuella datorer, vilket hjälper till att skydda och skydda dina data för att uppfylla organisationens säkerhet och efterlevnad. Mer information finns i [Azure Disk Encryption: virtuella Linux-datorer](../virtual-machines/linux/disk-encryption-overview.md) och [Azure Disk Encryption: virtuella Windows-datorer](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption kan också tillämpas på skalnings uppsättningar för virtuella Windows-och Linux-datorer, i följande instanser:
- Skalnings uppsättningar som skapats med hanterade diskar. Azure Disk Encryption stöds inte för interna (eller ohanterade) disk skalnings uppsättningar.
- OS-och data volymer i Windows skalnings uppsättningar.
- Data volymer i Linux-skalnings uppsättningar. Operativ system disk kryptering stöds inte för närvarande för Linux Scale Sets.

Du kan lära dig grunderna i Azure Disk Encryption för skalnings uppsättningar för virtuella datorer på bara några minuter med [kryptering av skalnings uppsättningar för virtuella datorer med hjälp av Azure CLI](disk-encryption-cli.md) eller [kryptera en virtuell dators skalnings uppsättningar med hjälp av Azure PowerShell](disk-encryption-powershell.md) Självstudier.

## <a name="next-steps"></a>Nästa steg

- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md)
- [Använd Azure Disk Encryption med sekvensering av tillägg för skalnings uppsättning för virtuell dator](disk-encryption-extension-sequencing.md)
