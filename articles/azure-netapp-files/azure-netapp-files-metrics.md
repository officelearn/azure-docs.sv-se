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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848791"
---
# <a name="metrics-for-azure-netapp-files"></a>Mått för Azure NetApp Files

Azure NetApp Files tillhandahåller mått för allokerat lagrings utrymme, faktisk lagrings användning, volym data flöde, IOPS och latens. Genom att analysera dessa mått kan du få en bättre förståelse för användnings mönstret och volym prestandan för dina NetApp-konton.  

## <a name="capacity_pools"></a>Användnings statistik för kapacitets grupper

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Allokerad volym pool används*  
    Summan av volym kvoten (GiB) i en specifik pool (det vill säga summan av volymernas etablerade storlekar i kapacitets gruppen)  
    Detta är den storlek som du valde när du skapade volymen.  
- *Total logisk storlek i volymprocent*  
    Totalt logiskt utrymme (GiB) som används för volymer i en kapacitets grupp  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>Användnings statistik för volymer

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Logisk volym storlek*   
    Det totala logiska utrymmet som används i en volym (GiB)  
    Den här storleken inkluderar det logiska utrymmet som används av aktiva fil system och ögonblicks bilder.  
- *Storlek på volym ögonblicks bild*   
   Det stegvisa logiska utrymmet som används av ögonblicks bilder i en volym  

## <a name="performance-metrics-for-volumes"></a>Prestanda mått för volymer

- *AverageReadLatency*   
    Genomsnittlig tid för läsningar från volymen i millisekunder
- *AverageWriteLatency*   
    Genomsnittlig tid för skrivningar från volymen i millisekunder
- *ReadIops*   
    Antalet läsningar till volymen per sekund
- *WriteIops*   
    Antalet skrivningar till volymen per sekund

## <a name="next-steps"></a>Nästa steg

* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
