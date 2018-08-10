---
title: Om ohanterade (sidblob) och managed disks-lagring för Microsoft Azure Linux-datorer | Microsoft Docs
description: Lär dig grunderna i ohanterade (sidblob) och hanterad disklagring för virtuella Linux-datorer i Azure.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.component: disks
ms.openlocfilehash: feb3e60ee1b43ec85c81912fbce086858bb33742
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715938"
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>Om disklagring för virtuella Linux-datorer
Precis som alla andra datorer Använd virtuella datorer i Azure diskar som en plats för att lagra ett operativsystem, program och data. Alla Azure virtuella datorer har minst två diskar – en operativsystemdisk för Linux och en tillfällig disk. Operativsystemdisken har skapats från en avbildning och både operativsystemdisken och avbildningen är virtuella hårddiskar (VHD) lagras i ett Azure storage-konto. Virtuella datorer kan också ha en eller flera datadiskar som lagras också som virtuella hårddiskar.

I den här artikeln ska vi prata om olika användningsområden för diskarna och beskrivs de olika typerna av diskar som du kan skapa och använda. Den här artikeln är också tillgängligt för [Windows virtuella datorer](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Diskar som används av virtuella datorer

Låt oss ta en titt på hur diskarna som används av de virtuella datorerna.

## <a name="operating-system-disk"></a>Operativsystemdisk

Varje virtuell dator har en ansluten operativsystemdisk. Den är registrerad som en SATA-enhet och är märkt med /dev/sda som standard. Den här disken har en maxkapacitet på 2 048 GB (Gigabyte).

## <a name="temporary-disk"></a>Temporär disk

Varje virtuell dator innehåller en tillfällig disk. Den temporära disken tillhandahåller kortsiktig lagring för program och processer och är avsedd att endast lagra data, till exempel växlingsfiler. Data på den temporära disken kan gå förlorade under en [underhållshändelse](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) eller när du [distribuera om en virtuell dator](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Data på den temporära enheten under en standard omstart av den virtuella datorn ska sparas. Men finns det fall där data inte kanske finns kvar, till exempel flyttar till en ny värd. Därför får alla data på den temporära enheten inte vara data som är nödvändiga i systemet.

På Linux-datorer, disken är vanligtvis **/dev/sdb** och formateras och monterade i **/mnt** av Azure Linux-agenten. Storleken på den temporära disken varierar baserat på storleken på den virtuella datorn. Mer information finns i [storlekar för virtuella Linux-datorer](../windows/sizes.md).

Läs mer om hur Azure använder den temporära disken [förstå den temporära enheten på Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Datadisk

En datadisk är en virtuell Hårddisk som är kopplad till en virtuell dator för att lagra programdata eller andra data som du behöver. Datadiskar är registrerade som SCSI-enheter och är märkta med en bokstav som du väljer. Varje datadisk har en maxkapacitet på 4 095 GB. Storleken på den virtuella datorn avgör hur många datadiskar som du kan koppla till det och vilken typ av lagring som du kan använda som värd för diskarna.

> [!NOTE]
> Mer information om kapacitet för virtuella datorer finns i [storlekar för virtuella Linux-datorer](./sizes.md).

När du skapar en virtuell dator från en avbildning skapar Azure en operativsystemdisk. Om du använder en avbildning som innehåller datadiskar, skapar Azure även på diskar när den virtuella datorn skapas. Annars kan du lägga till datadiskar när du har skapat den virtuella datorn.

Du kan lägga till datadiskar till en virtuell dator när som helst av **koppla** disken till den virtuella datorn. Du kan använda en virtuell Hårddisk som du har laddat upp eller kopieras till ditt storage-konto eller en som Azure skapar åt dig. Koppla en datadisk associerar VHD-filen med den virtuella datorn genom att placera ett lån på den virtuella Hårddisken så att den inte kan tas bort från lagring när den är fortfarande ansluten.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Nästa steg
* [Anslut en disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) att lägga till ytterligare lagringsutrymme för din virtuella dator.
* [Skapa en ögonblicksbild](snapshot-copy-managed-disk.md).
* [Konvertera till managed disks](convert-unmanaged-to-managed-disks.md).

