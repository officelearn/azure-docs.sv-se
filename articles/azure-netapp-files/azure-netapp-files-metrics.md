---
title: Mått för Azure NetApp Files | Microsoft Docs
description: Beskriver mått för Azure NetApp Files.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460440"
---
# <a name="metrics-for-azure-netapp-files"></a>Mått för Azure NetApp Files

Azure NetApp Files tillhandahåller mått för allokerat lagrings utrymme, faktisk lagrings användning, volym-IOPS och latens. Genom att analysera dessa mått kan du få en bättre förståelse för användnings mönstret och volym prestandan för dina NetApp-konton.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Användnings statistik för kapacitets grupper

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Pool allokerad till volym storlek*  
    Summan av volym kvoten (GiB) i en specifik kapacitets grupp (det vill säga summan av volymernas etablerade storlekar i kapacitets gruppen).  
    Den här storleken är den storlek som du valde när du skapade volymen.  
- *Förbrukad pool storlek*  
    Det totala logiska utrymmet (GiB) som används för volymer i en kapacitets grupp.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Användnings statistik för volymer

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Storlek på förbrukad volym*   
    Det totala logiska utrymmet som används i en volym (GiB).  
    Den här storleken inkluderar det logiska utrymmet som används av aktiva fil system och ögonblicks bilder.  
- *Storlek på volym ögonblicks bild*   
   Det stegvisa logiska utrymmet som används av ögonblicks bilder i en volym.  

## <a name="performance-metrics-for-volumes"></a>Prestanda mått för volymer

- *AverageReadLatency*   
    Genomsnittlig tid för läsningar från volymen i millisekunder.
- *AverageWriteLatency*   
    Genomsnittlig tid för skrivningar från volymen i millisekunder.
- *ReadIops*   
    Antalet läsningar till volymen per sekund.
- *WriteIops*   
    Antalet skrivningar till volymen per sekund.

## <a name="next-steps"></a>Nästa steg

* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
