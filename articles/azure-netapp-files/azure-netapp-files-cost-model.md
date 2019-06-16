---
title: Kostnad modell för Azure NetApp Files | Microsoft Docs
description: Beskriver kostnadsmodellen för Azure NetApp-filer för att hantera utgifter från tjänsten.
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65524227"
---
# <a name="cost-model-for-azure-netapp-files"></a>Kostnadsmodell för Azure NetApp Files 

Så här fungerar kostnadsmodellen för Azure NetApp Files kan du hantera dina utgifter från tjänsten.

## <a name="calculation-of-capacity-consumption"></a>Beräkning av kapacitetsförbrukning

Azure NetApp filer görs etablerade lagringskapacitet.  Etablerad kapacitet tilldelas genom att skapa kapacitet pooler.  Kapacitet pooler faktureras baserat på $/ etablerad-GiB/månad indelad i timmar. Den minsta storleken för en enda kapacitet-pool är 4 TiB och kapacitet pooler kan utökas senare i steg om 1 TiB. Volymer skapas i kapacitet poolerna.  Varje volym tilldelas en kvot som minskar i pooler allokerad kapacitet. Den kvot som kan tilldelas till volymer intervall från minst 100 GiB till högst 92 TiB.  

För en aktiv volym baseras kapacitetsförbrukning mot kvoten på logisk (effektiv) kapacitet.

Om faktiska kapacitetsförbrukningen för en volym överskrider sin lagringskvot, kan volymen fortsätta att växa. Skrivningar fortfarande tillåts så länge som den faktiska storleken är mindre än systemgränsen (100 TiB).  

Total Använd kapacitet i poolen kapacitet mot det etablerade värdet är summan av större av den tilldelade kvoten eller faktiska förbrukningen av alla volymer i poolen: 

   ![Total Använd kapacitet beräkning](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Diagrammet nedan illustrerar dessa begrepp.  
* Vi har en kapacitet pool med 4 TiB etablerad kapacitet.  Poolen innehåller tre volymer.  
    * Volym 1 tilldelas en kvot på 2 TiB och har 800 GiB användning.  
    * Volymen 2 tilldelas en kvot på 1 TiB och har 100 GiB användning.  
    * Volymen 3 tilldelas en kvot på 500 GiB men har 800 GiB förbrukning (överanvändning).  
* Poolen kapacitet mäts för 4 TiB kapacitet (det etablerade beloppet).  
    3.8 TiB kapacitet används (2 TiB och 1 TiB kvoten mellan volymer-1 och 2 och faktisk förbrukning för volymen 3 800 GiB). Och 200 GiB kapacitet som är kvar.

   ![Kapacitet pool med tre volymer](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Överförbrukning i kapacitetsförbrukning  

När kapacitet för en pool som används av det totala antalet överskrider dess etablerad kapacitet, skrivna data tillåts.  När tidsperioden (en timme) om Använd kapacitet i poolen överskrider fortfarande dess etablerad kapacitet, kommer sedan poolstorleken att automatiskt öka i steg om 1 TiB tills etablerad kapacitet är större än den totala kapaciteten som används.  Till exempel i bilden ovan ändras om volymen 3 fortsätter att växa och den faktiska förbrukningen når 1,2 TiB sedan efter respitperioden på poolen automatiskt till 5 TiB.  Resultatet är att etablerade pool-kapacitet (5 TiB) överskrider Använd kapacitet (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Manuella ändringar av pool-storlek  

Du kan manuellt öka eller minska poolstorleken. Dock gäller följande begränsningar:
* Lägsta och högsta tjänstbegränsningar  
    Finns i artikeln [resursgränser](azure-netapp-files-resource-limits.md).
* En 1 TiB ökningen efter de första 4 TiB minimiköpet
* En lägsta fakturering ökningen för per timme
* Etablerade poolstorleken kan inte minskas till mindre än det totala antalet använda kapacitet i poolen.

## <a name="behavior-of-maximum-size-pool-overage"></a>Beteendet för maximal storlek pool överförbrukning   

Den maximala storleken på poolen kapacitet som du kan skapa eller ändra storlek till är 500 TiB.  När det totala antalet använda kapacitet i poolen kapacitet överskrider 500 TiB sker i följande situationer:
* Skrivna data fortfarande tillåts (om volymen finns under system högst 100 TiB).
* När tidsperioden för en timmes storleksändras poolen automatiskt i steg om 1 TiB, tills poolkapacitet etablerats överskrider total Använd kapacitet.
* De ytterligare etablerad och faktureras pool-kapacitet som överstiger 500 TiB inte kan användas för att tilldela kvot för samtalsvolym. Heller det kan inte användas att expandera prestandabegränsningarna för QoS.  
    Se [servicenivåer](azure-netapp-files-service-levels.md) om prestandabegränsningarna och QoS storlek.

Diagrammet nedan illustrerar dessa begrepp:
* Vi har en kapacitet pool med ett Premium storage-nivå och en 500 TiB-kapacitet. Poolen innehåller nio volymer.
    * Volymer 1 och 8 tilldelas en kvot på 60 TiB.  Total Använd kapacitet är 480 TiB.  
        Varje volym har en QoS-begränsning på 3,75 GiB/s genomströmning (60 TiB * 64 MiB/s).  
    * Volymen 9 tilldelas en kvot på 20 TiB.  
        Volymen 9 har en QoS-begränsning på 1,25 GiB/s genomströmning (60 TiB * 64 MiB/s).
* Volymen 9 är ett scenario för överförbrukning. Den har 25 TiB faktiska användning.  
    * När tidsperioden för en timmes storleksändras poolen kapacitet till 505 TiB.  
        Det vill säga kapacitet som används av totalt = 8 * 60-TiB-kvoten för volymer 1 till och med 8 och 25 TiB faktisk förbrukning för volymen 9.
    * Fakturerade kapacitet är 505 TiB.
    * Kvot för samtalsvolym för volymen 9 kan inte höjas (eftersom den totala tilldelade kvoten för poolen inte får överstiga 500 TiB).
    * Ytterligare QoS dataflöde kan inte tilldelas (eftersom den totala QoS för poolen är fortfarande baserat på 500 TiB).

   ![Kapacitet pool med nio volymer](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Kapacitet förbrukning av ögonblicksbilder 

Kapacitetsförbrukningen ögonblicksbilder i Azure NetApp Files debiteras till kvoten för den överordnade volymen.  Därför kan delar den samma debiteringen som poolen kapacitet som tillhör volymen.  Men till skillnad från den aktiva volymen ögonblicksbild förbrukningen mäts baserat på den inkrementella kapaciteten som förbrukats.  Azure NetApp filer ögonblicksbilder är differentiella sin natur. Beroende på förändringstakten för data använda ögonblicksbilder ofta mycket mindre kapacitet än den logiska kapaciteten på den aktiva volymen. Anta att du har en ögonblicksbild av en volym med 500 GiB som endast innehåller 10 GiB differentiella data. Den kapacitet som debiteras mot kvot för samtalsvolym för denna ögonblicksbild är 10 GiB, inte 500 GiB. 

## <a name="next-steps"></a>Nästa steg

* [Sidan med priser för Azure NetApp-filer](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
