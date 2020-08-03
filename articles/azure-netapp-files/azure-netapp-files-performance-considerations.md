---
title: Prestanda överväganden för Azure NetApp Files | Microsoft Docs
description: Lär dig mer om prestanda för Azure NetApp Files, inklusive relationen mellan kvot-och data flödes gränsen och hur du dynamiskt ökar/minskar volym kvoten.
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
ms.openlocfilehash: 5f88b4755c7b4c0b20f27065cf9de2351251bc1c
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513884"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Saker att tänka på gällande prestanda för Azure NetApp Files

[Data flödes gränsen](azure-netapp-files-service-levels.md) för en volym bestäms av en kombination av den kvot som tilldelats volymen och den valda Service nivån. När du gör prestanda planer om Azure NetApp Files måste du förstå flera saker att tänka på. 

## <a name="quota-and-throughput"></a>Kvot och data flöde  

Data flödes gränsen är bara en Determinant av de faktiska prestanda som kommer att realiseras.  

Vanliga överväganden för lagrings prestanda, inklusive Läs-och skriv mixning, överförings storlek, slumpmässiga eller sekventiella mönster, och många andra faktorer bidrar till den totala prestanda som levereras.  

Det maximala empiriska data flödet som har observerats vid testning är 4 500 MiB/s.  På Premium Storage-nivån tillhandahåller en volym kvot på 70,31 TiB en gräns för data flöde som är tillräckligt hög för att uppnå den här prestanda nivån.  

Om du överväger att tilldela volym kvots mängder utöver 70,31 TiB kan ytterligare kvot tilldelas till en volym för lagring av ytterligare data. Den tillagda kvoten kommer dock inte att resultera i en ytterligare ökning av det faktiska data flödet.  

## <a name="overprovisioning-the-volume-quota"></a>Överetablera volym kvoten

Om arbets Belastningens prestanda är data flödes gräns, är det möjligt att överetablera volym kvoten för att ange en högre data flödes nivå och uppnå högre prestanda.  

Om en volym på nivån Premium lagring till exempel bara har 500 GiB av data men kräver 128 MiB/s av data flödet, kan du ange kvoten till 2 TiB så att data flödes nivån anges enligt detta (64 MiB/s per TB * 2 TiB = 128 MiB/s).  

Om du konsekvent överetablerar en volym för att uppnå ett högre data flöde bör du överväga att använda en högre service nivå i stället.  I exemplet ovan kan du uppnå samma data flödes gräns med hälften av volym kvoten genom att använda Ultra Storage-nivå i stället (128 MiB/s per TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Öka eller minska volym kvoten dynamiskt

Om dina prestanda krav är temporära i natur, eller om du har ökat prestanda behov under en viss tids period, kan du dynamiskt öka eller minska volym kvoten för att omedelbart justera data flödes gränsen.  Tänk på följande: 

* Volym kvoten kan ökas eller minskas utan att du behöver pausa IO och åtkomst till volymen avbryts eller påverkas inte.  

    Du kan justera kvoten under en aktiv I/O-transaktion mot en volym.  Observera att volym kvoten aldrig kan minskas under mängden logiska data som lagras i volymen.

* När volym kvoten ändras är motsvarande förändring i data flödes gränsen nästan momentant. 

    Ändringen avbryter eller påverkar inte volym åtkomsten eller I/O.  

* Anpassning av volym kvoten kräver en ändring i storleken på kapacitets gruppen.  

    Storleken på kapacitets poolen kan justeras dynamiskt och utan att volymens tillgänglighet eller I/O påverkas.

## <a name="next-steps"></a>Nästa steg

- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestandamått för Linux](performance-benchmarks-linux.md)