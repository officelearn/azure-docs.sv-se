---
title: Prestandamätningar ditt program i Azure Disk Storage – hanterade diskar
description: Läs mer om inom ditt program i Azure.
services: virtual-machines-linux,storage
author: rockboyfor
ms.author: v-yeche
origin.date: 01/11/2019
ms.date: 04/01/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 21ed4e9a6b1da10d0ae4c276612459506e13d94f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474376"
---
# <a name="benchmarking-a-disk"></a>Prestandamätningar en disk

Prestandamätningar är processen för att simulera olika arbetsbelastningar på ditt program och mäta programprestanda för varje arbetsbelastning. Med hjälp av anvisningarna i den [designa för höga prestanda artikeln](premium-storage-performance.md). Du kan fastställa prestandanivåer som ditt program kan uppnå med Premium Storage genom att köra prestandamätningsverktyg på de virtuella datorerna som är värd för programmet. I den här artikeln får du exempel på prestandamätningar en Standard DS14 virtuell dator som etablerats med Azure Premium Storage-diskar.

Vi har använt common prestandamätningsverktyg Iometer och FIO, för Windows och Linux respektive. Dessa verktyg skapar flera trådar som simulerar en produktionsliknande arbetsbelastning och mäta systemets prestanda. Med hjälp av verktyg kan du också konfigurera parametrar som block storlek och kön djup, som normalt inte kan ändra för ett program. Detta ger dig större flexibilitet att driva ut maximala prestanda i hög skala virtuella datorer med premium-diskar för olika typer av arbetsbelastningar för program. Läs mer om varje benchmarking verktyg finns [Iometer](http://www.iometer.org/) och [FIO](http://freecode.com/projects/fio).

Om du vill följa exemplen nedan, skapa en Standard DS14 virtuell dator och koppla 11 Premium Storage-diskar till den virtuella datorn. Konfigurera 10 diskar med värdcachelagring som ”None” och stripe-dem till en volym med namnet NoCacheWrites 11 diskar. Konfigurera värdcachelagring som ”skrivskyddad” på den återstående disken och skapa en volym med namnet CacheReads med den här disken. Med den här inställningen, kan du se ut maximala prestanda för läsning och skrivning från en Standard DS14 virtuell dator. Detaljerade anvisningar om hur du skapar en DS14 virtuell dator med premium disks går du till [designa för höga prestanda](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Nästa steg

Gå igenom våra design för hög prestanda artikeln. I det skapar du en checklista liknar ditt befintliga program för den aktuella typen. Med hjälp av Benchmarking verktyg kan du simulera arbetsbelastningarna och mäta prestanda på prototyp programmet. Då kan du bestämma vilka disk erbjudande kan matcha eller överträffa dina prestandakrav för programmet. Du kan sedan implementera samma riktlinjer som för dina produktionsprogram.

> [!div class="nextstepaction"]
> Läsa artikeln om [designa för höga prestanda](premium-storage-performance.md) börjar.