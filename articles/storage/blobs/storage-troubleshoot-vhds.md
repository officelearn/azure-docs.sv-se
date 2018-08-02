---
title: Felsöka diskar anslutna till virtuella Azure-datorer | Microsoft Docs
description: Azure Blob storage är utformat för att lagra stora mängder ostrukturerade objektdata, exempelvis text eller binära data. Dina program kan komma åt objekt i Blob storage från PowerShell eller Azure CLI, från kod via Azure Storage-klientbibliotek eller via REST.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.component: disks
ms.openlocfilehash: 0dbd89c28d18d64908d92cd38d8bd1cf3138fd5c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397975"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Felsöka diskar anslutna till virtuella Azure-datorer 

Azure-datorer (VM) är beroende av virtuella hårddiskar (VHD) för OS-disken och eventuella anslutna datadiskar. Virtuella hårddiskar lagras som sidblobar i en eller flera Azure Storage-konton. Den här artikeln pekar på felsökning av innehåll för vanliga problem som kan uppstå med virtuella hårddiskar. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Felsöka fel vid borttagning av lagring för en virtuell dator

I vissa fall kan det uppstå ett fel när du tar bort en resurs för lagring när en virtuell dator i en Resource Manager-distribution innehåller anslutna virtuella hårddiskar. Hjälp med att lösa det här problemet, finns i följande artiklar: 

  * På virtuella Linux-datorer: [fel vid borttagning av lagring i Resource Manager-distribution](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * På Windows virtuella datorer: [fel vid borttagning av lagring i Resource Manager-distribution](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar

Om det uppstår oväntade omstarter av en virtuell dator med ett stort antal anslutna virtuella hårddiskar finns i följande artiklar:

  * På virtuella Linux-datorer: [oväntat omstarter av virtuella datorer med anslutna virtuella hårddiskar](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * På Windows virtuella datorer: [oväntat omstarter av virtuella datorer med anslutna virtuella hårddiskar](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
