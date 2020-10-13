---
title: Pool för manuell QoS-kapacitet för Azure NetApp Files | Microsoft Docs
description: Innehåller en introduktion till poolen för manuella QoS-kapaciteter och referenser för ytterligare information.
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 14b905c69f0dab933159b414028db3e985d314a3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935840"
---
# <a name="manual-qos-capacity-pool"></a>Manuell pool för QoS-kapacitet

Den här artikeln innehåller en introduktion till funktionerna för manuell QoS-kapacitet (Quality of Service).

## <a name="how-manual-qos-differs-from-auto-qos"></a>Hur manuell QoS skiljer sig från automatisk QoS

[QoS-typen](azure-netapp-files-understand-storage-hierarchy.md#qos_types) är ett attribut för en pool för kapacitet. Azure NetApp Files tillhandahåller två QoS-typer av kapacitets grupper – automatisk (standard) och manuell.  

I en *manuell* pool för QoS-kapacitet kan du tilldela kapaciteten och data flödet för en volym oberoende av varandra. Det totala data flödet för alla volymer som skapats med en manuell QoS-kapacitet begränsas av poolens totala genomflöde. Det bestäms av kombinationen av poolens storlek och data flödet på tjänst nivå. 

I en pool med *Automatisk* QoS-kapacitet tilldelas data flöde automatiskt till volymerna i poolen, proportionella mot storleks kvoten som tilldelats volymerna.  

Se [Storage-hierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) och [prestanda överväganden för Azure NetApp Files](azure-netapp-files-performance-considerations.md) för överväganden om QoS-typer.

## <a name="how-to-specify-the-manual-qos-type"></a>Ange den manuella QoS-typen

När du [skapar en pool för kapacitet](azure-netapp-files-set-up-capacity-pool.md)kan du ange för kapacitets poolen att använda den manuella QoS-typen.  Du kan också [ändra en befintlig kapacitets uppsättning](manage-manual-qos-capacity-pool.md#change-to-qos) så att den använder den manuella QoS-typen. 

Att ställa in kapacitets typen till manuell QoS är en permanent ändring. Du kan inte konvertera ett manuellt kapacitets verktyg för QoS-typ till en pool med automatisk QoS-kapacitet. 

Om du använder den manuella QoS-typen måste du [Registrera funktionen](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Nästa steg

* [Hantera en manuell QoS-kapacitetspool](manage-manual-qos-capacity-pool.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Lagringspool](azure-netapp-files-understand-storage-hierarchy.md) 
* [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Saker att tänka på gällande prestanda för Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Kostnadsmodell för Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Skapa en NFS-volym](azure-netapp-files-create-volumes.md)
* [Skapa en SMB-volym](azure-netapp-files-create-volumes-smb.md)
* [Skapa en volym med dubbla protokoll](create-volumes-dual-protocol.md)
* [Mått för Azure NetApp Files](azure-netapp-files-metrics.md)
* [Felsök problem med kapacitets pooler](troubleshoot-capacity-pools.md)
