---
title: Felsöka diskar kopplade till virtuella datorer i Azure | Microsoft Docs
description: Azure Blob storage är utformat för att lagra stora mängder ostrukturerade objektdata, exempelvis text eller binära data. Dina program kan komma åt objekt i Blob storage från PowerShell eller Azure CLI, från kod via Azure Storage-klientbibliotek eller via REST.
services: storage
author: genlin
manager: cshepard
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 766062b085c359499046151f337921a51d948715
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362714"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Felsöka diskar kopplade till virtuella Azure-datorer 

Virtuella Azure-datorer (VM) som förlitar sig på virtuella hårddiskar (VHD) för OS-disken och alla anslutna diskar. Virtuella hårddiskar lagras som sidblobbar i en eller flera Azure Storage-konton. Den här artikeln pekar på felsökningsinnehåll för vanliga problem som kan uppstå med virtuella hårddiskar. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Felsök borttagningsfel i lagring för en virtuell dator

I vissa fall kan du påträffar ett fel när du tar bort en lagringsresurs när en virtuell dator i en Resource Manager distribution innehåller anslutna virtuella hårddiskar. Hjälp med att lösa det här problemet finns i följande artiklar: 

  * På virtuella Linux-datorer: [lagring borttagningsfel i Resource Manager-distribution](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * På virtuella Windows-datorer: [lagring borttagningsfel i Resource Manager-distribution](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsökning av oväntad omstarter av virtuella datorer med anslutna virtuella hårddiskar

Om det uppstår oväntade omstarter av en virtuell dator med ett stort antal anslutna virtuella hårddiskar finns i följande artiklar:

  * På virtuella Linux-datorer: [oväntad startas om av virtuella datorer med anslutna virtuella hårddiskar](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * På virtuella Windows-datorer: [oväntad startas om av virtuella datorer med anslutna virtuella hårddiskar](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
