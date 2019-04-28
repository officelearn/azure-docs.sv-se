---
title: Mätvärden för Azure NetApp-filer | Microsoft Docs
description: Beskriver mätvärden för Azure NetApp-filer.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61084947"
---
# <a name="metrics-for-azure-netapp-files"></a>Mått för Azure NetApp Files

NetApp-filer som Azure tillhandahåller mått för allokerat lagringsutrymme, faktiska lagringsanvändning, genomströmning, IOPS och svarstider. Genom att analysera de här måtten kan få du en bättre förståelse på användning mönstret och volym prestanda för dina NetApp-konton.  

## <a name="capacity_pools"></a>Användningsstatistik för kapacitet pooler

- *Volymstorlek för poolen som allokerats*  
    Det här är etablerad kapacitet poolen storlek (GiB).  
- *Volymen poolen allokeras används*  
    Detta är summan av kvot för samtalsvolym (GiB) i en viss kapacitet pool (d.v.s. summan av de volymer etablerade storlekar i poolen kapacitet). Detta är den storleken som du valde under skapa volymer.  
- *Volymen Totalt antal logiska poolstorlek*  
    Det här är det totala antalet logiska utrymme (GiB) används av volymer i en pool kapacitet.  
- *Volymstorlek pool totala ögonblicksbild*  
    Detta är summan av inkrementell logiska utrymmet som används av ögonblicksbilder.  

## <a name="volumes"></a>Användningsstatistik för volymer

- *Volymen som allokerats storlek*   
    Det här är volymens storlek (quota) som etablerats i GiB.  
- *Logisk storlek*   
    Det här är det totala logiska utrymmet som används i en volym (GiB). Den här storleken innehåller logiska utrymmet som används av aktiva filsystem och ögonblicksbilder.  
- *Volymstorlek för ögonblicksbild*   
    Det här är inkrementell logiska utrymmet som används av ögonblicksbilder för en volym.  

## <a name="next-steps"></a>Nästa steg

* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
