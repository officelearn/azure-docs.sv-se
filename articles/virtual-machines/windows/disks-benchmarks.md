---
title: Benchmarking av ditt program på Azure Disk Storage
description: Lär dig mer om hur du jämför ditt program på Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 77e542e9bff399e58b433286385864478bcb5076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75719553"
---
# <a name="benchmarking-a-disk"></a>Benchmarking av en disk

Benchmarking är processen att simulera olika arbetsbelastningar på ditt program och mäta programmets prestanda för varje arbetsbelastning. Med hjälp av stegen som beskrivs i [designen för högpresterande artikel](premium-storage-performance.md)har du samlat in kraven för programmets prestanda. Genom att köra benchmarkingverktyg på de virtuella datorerna som är värdar för programmet kan du bestämma vilka prestandanivåer som ditt program kan uppnå med Premium Storage. I den här artikeln ger vi dig exempel på benchmarking av en standard DS14 VM-etablerad med Azure Premium Storage-diskar.

Vi har använt gemensamma benchmarking verktyg Iometer och FIO, för Windows och Linux respektive. Dessa verktyg ger upphov till flera trådar som simulerar en produktion som arbetsbelastning och mäter systemets prestanda. Med hjälp av verktygen kan du också konfigurera parametrar som blockstorlek och ködjup, som du normalt inte kan ändra för ett program. Detta ger dig större flexibilitet att driva maximal prestanda på en storskalig virtuell dator som etablerats med premiumdiskar för olika typer av programarbetsbelastningar. Om du vill veta mer om varje benchmarking verktyg besök [Iometer](http://www.iometer.org/) och [FIO](http://freecode.com/projects/fio).

Om du vill följa exemplen nedan skapar du en virtuell standarddS14-dator och ansluter 11 Premium Storage-diskar till den virtuella datorn. Av de 11 diskarna konfigurerar du 10 diskar med värdcachelagring som "Ingen" och stripe dem till en volym som kallas NoCacheWrites. Konfigurera värdcachelagring som "ReadOnly" på den återstående disken och skapa en volym som kallas CacheReads med den här disken. Med den här inställningen kan du se den maximala läs- och skrivprestandan från en virtuell standarddS14-dator. Detaljerade steg om hur du skapar en DS14-virtuell dator med premium-SSD:er finns i [Designa för hög prestanda](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Nästa steg

Gå vidare till vår artikel om [design för hög prestanda.](premium-storage-performance.md)

I den artikeln skapar du en checklista som liknar ditt befintliga program för prototypen. Med hjälp av benchmarkingverktyg kan du simulera arbetsbelastningar och mäta prestanda på prototypprogrammet. På så sätt kan du avgöra vilket diskerbjudande som kan matcha eller överträffa dina krav på programmets prestanda. Sedan kan du implementera samma riktlinjer för ditt produktionsprogram.