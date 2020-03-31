---
title: Aktivera Azure-diskkryptering för skalningsuppsättningar för virtuella datorer
description: Den här artikeln innehåller instruktioner om hur du aktiverar Microsoft Azure Disk Encryption for Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278974"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Diskkryptering för skalningsuppsättningar för virtuella datorer

Azure Disk Encryption tillhandahåller volymkryptering för operativsystem och datadiskar på dina virtuella datorer, vilket hjälper till att skydda och skydda dina data för att uppfylla organisatoriska säkerhets- och efterlevnadsåtaganden. Mer information finns i [Azure Disk Encryption: Linux-virtuella datorer](../virtual-machines/linux/disk-encryption-overview.md) och Azure Disk [Encryption: Windows VIRTUELLA datorer](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption kan också tillämpas på Windows och Linux virtuella dator skalningsuppsättningar, i dessa fall:
- Skalningsuppsättningar som skapats med hanterade diskar. Azure Disk-kryptering stöds inte för inbyggda (eller ohanterada) diskskalningsuppsättningar.
- OS- och datavolymer i Windows-skalningsuppsättningar.
- Datavolymer i Linux-skalningsuppsättningar. OS-diskkryptering stöds INTE för närvarande för Linux-skalningsuppsättningar.

Du kan lära dig grunderna i Azure Disk Encryption för skalningsuppsättningar för virtuella datorer på bara några minuter med [skaluppsättningarna Kryptera en virtuell dator med Azure CLI](disk-encryption-cli.md) eller kryptera en virtuell [datorskalauppsättningar med azure PowerShell-självstudierna.](disk-encryption-powershell.md)

## <a name="next-steps"></a>Nästa steg

- [Kryptera en skala för en virtuell dator med Hjälp av Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Skapa och konfigurera ett nyckelvalv för Azure DiskKryptering](disk-encryption-key-vault.md)
- [Använda Azure Disk Encryption med sekvensering av tillägg för virtuell datoruppsättning](disk-encryption-extension-sequencing.md)
