---
title: Om ohanterade (sidblob) och managed disks-lagring för Microsoft Azure Windows Virtual Machines | Microsoft Docs
description: Lär dig grunderna i ohanterade (sidblob) och managed disks-lagring för Windows-datorer i Azure.
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.component: disks
ms.openlocfilehash: b1127475b873c5679946121e570ef2bb51f25be1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234722"
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>Om disklagring för virtuella Azure Windows-datorer

Precis som alla andra datorer Använd virtuella datorer i Azure diskar som en plats för att lagra ett operativsystem, program och data. Alla Azure virtuella datorer har minst två diskar – en operativsystemdisk för Windows och en tillfällig disk. Operativsystemdisken har skapats från en avbildning och både operativsystemdisken och avbildningen är virtuella hårddiskar (VHD) lagras i ett Azure storage-konto. Virtuella datorer kan också ha en eller flera datadiskar som lagras också som virtuella hårddiskar. 

I den här artikeln ska vi prata om olika användningsområden för diskarna och beskrivs de olika typerna av diskar som du kan skapa och använda. Den här artikeln är också tillgängligt för [Linux-datorer](../linux/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Diskar som används av virtuella datorer

Låt oss ta en titt på hur diskarna som används av de virtuella datorerna.

### <a name="operating-system-disk"></a>Operativsystemdisk

Varje virtuell dator har en ansluten operativsystemdisk. Den har registrerats som en SATA-enhet och märkta som C:-enheten som standard. Den här disken har en maxkapacitet på 2 048 GB (Gigabyte).

### <a name="temporary-disk"></a>Temporär disk

Varje virtuell dator innehåller en tillfällig disk. Den temporära disken tillhandahåller kortsiktig lagring för program och processer och är avsedd att endast lagra data, till exempel växlingsfiler. Data på den temporära disken kan gå förlorade under en [underhållshändelse](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) eller när du [distribuera om en virtuell dator](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Data på den temporära enheten vid en lyckad standard omstart av den virtuella datorn ska sparas. Men finns det fall där data inte kanske finns kvar, till exempel flyttar till en ny värd. Därför får alla data på den temporära enheten inte vara data som är nödvändiga i systemet.

Den temporära disken är märkta som enheten D: som standard och det används för att lagra pagefile.sys. Om du vill mappa om den här disken till en annan enhetsbeteckning, se [ändra enhetsbeteckningen för den temporära disken Windows](change-drive-letter.md). Storleken på den temporära disken varierar baserat på storleken på den virtuella datorn. Mer information finns i [storlekar för Windows-datorer](sizes.md).

Läs mer om hur Azure använder den temporära disken [förstå den temporära enheten på Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

### <a name="data-disk"></a>Datadisk

En datadisk är en virtuell Hårddisk som är kopplad till en virtuell dator för att lagra programdata eller andra data som du behöver. Datadiskar är registrerade som SCSI-enheter och är märkta med en bokstav som du väljer. Varje datadisk har en maxkapacitet på 4095 GB, hanterade diskar har en maxkapacitet på 32 767 GiB. Storleken på den virtuella datorn avgör hur många datadiskar som du kan koppla till det och vilken typ av lagring som du kan använda som värd för diskarna.

> [!NOTE]
> Mer information om kapacitet för virtuella datorer finns i [storlekar för Windows-datorer](sizes.md).

När du skapar en virtuell dator från en avbildning skapar Azure en operativsystemdisk. Om du använder en avbildning som innehåller datadiskar, skapar Azure även på diskar när den virtuella datorn skapas. Annars kan du lägga till datadiskar när du har skapat den virtuella datorn.

Du kan lägga till datadiskar till en virtuell dator när som helst av **koppla** disken till den virtuella datorn. Du kan använda en virtuell Hårddisk som du har laddat upp eller kopieras till ditt storage-konto eller använda en tom virtuell Hårddisk som Azure skapar åt dig. Koppla en datadisk associerar VHD-filen med den virtuella datorn genom att placera ett lån på den virtuella Hårddisken så att den inte kan tas bort från lagring när den är fortfarande ansluten.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

Förhandsgranska storlekar, se vår [vanliga frågor och svar](faq-for-disks.md#new-disk-sizes-managed-and-unmanaged) vill veta vilka regioner som de är tillgängliga i.

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>En senaste rekommendation: Använd TRIM med ohanterade standarddiskar

Om du använder ohanterade standarddiskar (HDD), bör du aktivera TRIMNING. TRIM ignorerar oanvända block på disken så att du debiteras endast för lagring som du faktiskt använder. Detta kan sänka kostnaderna om du skapar stora filer och ta bort dem.

Du kan köra detta kommando för att kontrollera TRIM inställningen. Öppna en kommandotolk på din virtuella Windows-datorn och skriv:

```
fsutil behavior query DisableDeleteNotify
```

Om kommandot returnerar 0, är TRIMNING aktiverat på rätt sätt. Om den returnerar 1, kör du följande kommando för att aktivera TRIM:

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> Obs: Stöd för Trim börjar med Windows Server 2012 / Windows 8 och senare, se [nytt API kan appar skicka ”TRIMMA och Avmappa” tips till lagringsmedia](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints).
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Nästa steg
* [Anslut en disk](attach-disk-portal.md) att lägga till ytterligare lagringsutrymme för din virtuella dator.
* [Skapa en ögonblicksbild](snapshot-copy-managed-disk.md).
* [Konvertera till managed disks](convert-unmanaged-to-managed-disks.md).


