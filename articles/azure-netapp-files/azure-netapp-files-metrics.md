---
title: Mått för Azure NetApp-filer | Microsoft-dokument
description: Beskriver mått för Azure NetApp-filer.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460440"
---
# <a name="metrics-for-azure-netapp-files"></a>Mått för Azure NetApp Files

Azure NetApp Files tillhandahåller mått på allokerad lagring, faktisk lagringsanvändning, volym-IOPS och svarstid. Genom att analysera dessa mått kan du få en bättre förståelse för användningsmönstret och volymprestanda för dina NetApp-konton.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Användningsmått för kapacitetspooler

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Pool allokerad till volymstorlek*  
    Den totala volymkvoten (GiB) i en viss kapacitetspool (det vill än summan av volymernas etablerade storlekar i kapacitetspoolen).  
    Den här storleken är den storlek du valde när du skapades i volym.  
- *Förbrukad poolstorlek*  
    Det totala logiska utrymme (GiB) som används över volymer i en kapacitetspool.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Användningsmått för volymer

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Förbrukad volymstorlek*   
    Det totala logiska utrymmet som används i en volym (GiB).  
    Den här storleken innehåller logiskt utrymme som används av aktiva filsystem och ögonblicksbilder.  
- *Storlek för ögonblicksbild av volym*   
   Det inkrementella logiska utrymme som används av ögonblicksbilder i en volym.  

## <a name="performance-metrics-for-volumes"></a>Prestandamått för volymer

- *Genomsnittligläsning*   
    Genomsnittlig tid för läsningar från volymen i millisekunder.
- *AverageWriteLatency*   
    Den genomsnittliga tiden för skrivningar från volymen i millisekunder.
- *ReadIops (läs)*   
    Antalet läsningar till volymen per sekund.
- *WriteIops*   
    Antalet skrivningar till volymen per sekund.

## <a name="next-steps"></a>Nästa steg

* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
