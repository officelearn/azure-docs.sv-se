---
title: Felsöka diskar anslutna till virtuella Azure-datorer | Microsoft Docs
description: Innehåller länkar till felsökningsresurser för Azure-dator, virtuella hårddiskar (VHD).
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 10/31/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.openlocfilehash: bf1aa75399f28b8dd1732bc9ec09c15e46132939
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098107"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Felsöka diskar anslutna till virtuella Azure-datorer 

Azure-datorer (VM) är beroende av virtuella hårddiskar (VHD) för OS-disken och eventuella anslutna datadiskar. Virtuella hårddiskar lagras som sidblobar i en eller flera Azure Storage-konton. Den här artikeln pekar på felsökning av innehåll för vanliga problem som kan uppstå med virtuella hårddiskar. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Felsöka fel vid borttagning av lagring för en virtuell dator

I vissa fall kan det uppstå ett fel när du tar bort en resurs för lagring när en virtuell dator i en Resource Manager-distribution innehåller anslutna virtuella hårddiskar. Hjälp med att lösa det här problemet, finns i följande artiklar: 

  * På virtuella Linux-datorer: [Fel vid borttagning av lagring i Resource Manager-distribution](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * På Windows virtuella datorer: [Fel vid borttagning av lagring i Resource Manager-distribution](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar

Om det uppstår oväntade omstarter av en virtuell dator med ett stort antal anslutna virtuella hårddiskar finns i följande artiklar:

  * På virtuella Linux-datorer: [Oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * På Windows virtuella datorer: [Oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
