---
title: Felsöka diskar anslutna till virtuella Azure-datorer | Microsoft Docs
description: Innehåller länkar till felsökningsresurser för Azure-dator, virtuella hårddiskar (VHD).
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: c00fdf3ad02edc1faf0d3257d3836a1c2f44d682
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150775"
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
