---
title: Prestandaöverväganden för Azure NetApp Files | Microsoft Docs
description: Beskriver prestandaöverväganden för Azure NetApp-filer.
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454144"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Saker att tänka på gällande prestanda för Azure NetApp Files

Den [dataflödesgräns](azure-netapp-files-service-levels.md) för en volym bestäms av en kombination av kvoten för volymen och valda-tjänsten. När du gör prestanda planer om Azure NetApp filer, måste du förstå flera saker. 

## <a name="quota-and-throughput"></a>Kvoter och dataflöde  

Dataflöde gränsen är endast ett faktorn vad gäller den faktiska prestanda kommer realiseras.  

Prestandaöverväganden för vanliga lagring, inklusive läser och skriver blandning, överföringsstorlek slumpmässiga eller sekventiella mönster och många andra faktorer kommer att bidra till den totala prestanda som levereras.  

Det maximala empiriska dataflöde som har observerats vid testning är 4 500 MiB/s.  På Premium storage-nivån är etableras en kvot för samtalsvolym av 70.31 TiB en dataflödesgräns som är tillräckligt högt för att uppnå den här nivån av prestanda.  

Om du funderar på att tilldela volymen kvot belopp utöver 70.31 TiB kan större kvot tilldelas till en volym för att lagra ytterligare data. Kvoten som har lagts till ska inte resultera i en ytterligare ökning av det verkliga dataflödet.  

Se [prestandatest för Azure NetApp Files](azure-netapp-files-performance-benchmarks.md) för ytterligare information.

## <a name="overprovisioning-the-volume-quota"></a>Överetablering kvot för samtalsvolym

Om arbetsbelastningens prestanda är dataflödesgräns bunden, är det möjligt att därför överetablerar kvot för samtalsvolym för att ange en högre dataflödesnivå och få bättre prestanda.  

Till exempel om en volym i Premium storage-nivå har endast 500 GiB data men kräver 128 MiB/s genomströmning, du kan ange kvoten till 2 TiB så att dataflödesnivå är inställt därefter (64 MiB/s per TB * 2 TiB = 128 MiB/s).  

Överväg att använda en högre servicenivå i stället om du konsekvent därför överetablerar en volym för att uppnå ett högre dataflöde.  I exemplet ovan kan du uppnå samma dataflödesgräns med halva kvot för samtalsvolym med hjälp av Ultra lagringsnivå i stället (128 MiB/s per TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamiskt öka eller minska kvot för samtalsvolym

Om dina prestandakrav är tillfällig, eller om du har ökat prestandabehov för en fastställd tidsperiod, kan du dynamiskt öka eller minska kvot för samtalsvolym omedelbart justera dataflödet storleksgränsen.  Observera följande överväganden: 

* Kvot för samtalsvolym kan ökas eller minskas utan något behov av att pausa i/o och åtkomst till volymen inte avbryts eller påverkas.  

    Du kan justera kvoten under en aktiv i/o-transaktion mot en volym.  Observera att kvot för samtalsvolym kan aldrig minskas nedan hur mycket av logiska data som lagras på volymen.

* När kvot för samtalsvolym ändras är motsvarande ändring i dataflödesgräns nästan omedelbart. 

    Ändringen inte avbryta eller påverka åtkomst till en volym eller i/o.  

* Justera kvot för samtalsvolym kräver en ändring i poolstorlek för kapacitet.  

    Poolstorlek kapacitet kan justeras dynamiskt och utan att påverka volymtillgängligheten eller i/o.

## <a name="next-steps"></a>Nästa steg

- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestandamått för Azure NetApp-filer](azure-netapp-files-performance-benchmarks.md)