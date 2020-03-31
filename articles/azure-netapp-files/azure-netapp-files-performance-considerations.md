---
title: Prestandaöverväganden för Azure NetApp-filer | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454144"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Saker att tänka på gällande prestanda för Azure NetApp Files

[Dataflödesgränsen](azure-netapp-files-service-levels.md) för en volym bestäms av en kombination av den kvot som tilldelats volymen och den valda servicenivån. När du gör prestandaplaner om Azure NetApp-filer måste du förstå flera överväganden. 

## <a name="quota-and-throughput"></a>Kvot och dataflöde  

Dataflödesgränsen är bara en avgörande faktor för den faktiska prestanda som ska realiseras.  

Typiska överväganden för lagringsprestanda, inklusive läs- och skrivmix, överföringsstorlek, slumpmässiga eller sekventiella mönster, och många andra faktorer kommer att bidra till den totala prestandan som levereras.  

Den maximala empiriska genomströmningen som har observerats vid testning är 4 500 MiB/s.  På premiumlagringsnivån etablerar en volymkvot på 70,31 TiB en dataflödesgräns som är tillräckligt hög för att uppnå den här prestandanivån.  

Om du funderar på att tilldela volymkvotbelopp utöver 70,31 TiB kan ytterligare kvot tilldelas en volym för lagring av ytterligare data. Den tillagda kvoten kommer dock inte att leda till en ytterligare ökning av det faktiska genomströmningen.  

Mer [information finns i Prestandariktmärken för Azure NetApp-filer.](azure-netapp-files-performance-benchmarks.md)

## <a name="overprovisioning-the-volume-quota"></a>Överetablering av volymkvoten

Om en arbetsbelastnings prestanda är begränsad till dataflöde är det möjligt att överetablera volymkvoten för att ange en högre dataflödesnivå och uppnå högre prestanda.  

Om till exempel en volym i Premium-lagringsnivån bara har 500 GiB data men kräver 128 MiB/s dataflöde, kan du ställa in kvoten till 2 TiB så att dataflödesnivån ställs in i enlighet med detta (64 MiB/s per TB * 2 TiB = 128 MiB/s).  

Om du konsekvent överetablera en volym för att uppnå ett högre dataflöde kan du överväga att använda en högre servicenivå i stället.  I exemplet ovan kan du uppnå samma dataflödesgräns med halva volymkvoten genom att använda Lagringsnivån Ultra istället (128 MiB/s per TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamiskt ökande eller minskande volymkvot

Om dina prestandakrav är tillfälliga till sin natur, eller om du har ökade prestandabehov under en bestämd tidsperiod, kan du dynamiskt öka eller minska volymkvoten för att omedelbart justera dataflödesgränsen.  Observera följande överväganden: 

* Volymkvoten kan ökas eller minskas utan att i/o behöver pausas, och åtkomsten till volymen avbryts inte eller påverkas.  

    Du kan justera kvoten under en aktiv I/O-transaktion mot en volym.  Observera att volymkvoten aldrig kan minskas under mängden logiska data som lagras i volymen.

* När volymkvoten ändras är motsvarande ändring av dataflödesgränsen nästan omedelbar. 

    Ändringen avbryter eller påverkar inte volymåtkomsten eller I/O.  

* Justering av volymkvoten kräver en ändring av kapacitetspoolens storlek.  

    Kapacitetspoolens storlek kan justeras dynamiskt och utan att påverka volymtillgängligheten eller I/O.

## <a name="next-steps"></a>Nästa steg

- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestandamått för Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)