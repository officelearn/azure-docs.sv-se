---
title: Felsöka diskar som är kopplade till virtuella Azure-datorer | Microsoft-dokument
description: Innehåller länkar till felsökningsresurser för virtuella hårddiskar (Virtual Machine Virtual Hard Disks).
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061190"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Felsöka diskar som är kopplade till virtuella Azure-datorer 

Virtuella Azure-datorer (VMs) förlitar sig på virtuella hårddiskar (VHD) för OS-disken och alla anslutna datadiskar. Virtuella hårddiskar lagras som sidblobar i ett eller flera Azure Storage-konton. Den här artikeln pekar på felsökning av innehåll för vanliga problem som kan uppstå med virtuella hårddiskar. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Felsöka lagringsborttagningsfel för en virtuell dator

I vissa fall kan du stöta på ett fel när du tar bort en lagringsresurs när en virtuell dator i en Resource Manager-distribution innehåller kopplade virtuella hårddiskar. Mer information om hur du löser problemet finns i någon av följande artiklar: 

  * På virtuella Linux-datorer: [Lagringsborttagningsfel i Resurshanterarens distribution](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * På virtuella datorer i Windows: [Fel för borttagning av lagring i Resurshanterarens distribution](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar

Om du stöter på oväntade omstarter av en virtuell dator med ett stort antal anslutna virtuella hårddiskar läser du någon av följande artiklar:

  * På Virtuella Linux-datorer: [Oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * På virtuella Windows-datorer: [Oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
