---
title: Azure HPC-cachedata intas - msrsync
description: Så här använder du msrsync för att flytta data till ett Blob-lagringsmål i Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168422"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC-cachedata intas - msrsync-metod

Den här artikeln innehåller detaljerade instruktioner för hur du använder ``msrsync`` verktyget för att kopiera data till en Azure Blob-lagringsbehållare för användning med Azure HPC-cache.

Mer information om hur du flyttar data till Blob-lagring för Din Azure HPC-cache läser du [Flytta data till Azure Blob storage](hpc-cache-ingest.md).

Verktyget ``msrsync`` kan användas för att flytta data till ett lagringsmål för säkerhetskopiering för Azure HPC-cachen. Det här verktyget är utformat för ``rsync`` att optimera bandbreddsanvändningen genom att köra flera parallella processer. Den är tillgänglig från https://github.com/jbd/msrsyncGitHub på .

``msrsync``delar upp källkatalogen i separata "buckets" och kör sedan enskilda ``rsync`` processer på varje bucket.

Preliminära tester med en virtuell dator med fyra kärnor visade bästa effektivitet när du använder 64 processer. Använd ``msrsync`` alternativet ``-p`` för att ställa in antalet processer till 64.

Observera ``msrsync`` att det bara kan skriva till och från lokala volymer. Källan och målet måste vara tillgängliga som lokala fästen på den arbetsstation som används för att utfärda kommandot.

Följ dessa instruktioner ``msrsync`` för att använda för att fylla Azure Blob-lagring med Azure HPC-cache:

1. Installera ``msrsync`` och dess``rsync`` förutsättningar ( och Python 2.6 eller senare)
1. Bestäm det totala antalet filer och kataloger som ska kopieras.

   Använd till exempel ``prime.py`` verktyget ```prime.py --directory /path/to/some/directory``` med argument (tillgängligt genom att <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>hämta ).

   Om du ``prime.py``inte använder kan du beräkna antalet ``find`` artiklar med GNU-verktyget enligt följande:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividera antalet artiklar med 64 för att bestämma antalet artiklar per process. Använd det här ``-f`` numret med alternativet att ange storleken på buckets när du kör kommandot.

1. Utfärda ``msrsync`` kommandot för att kopiera filer:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Det här kommandot är till exempel utformat för att flytta 11 000 filer i 64 processer från /test/source-repository till /mnt/hpccache/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
