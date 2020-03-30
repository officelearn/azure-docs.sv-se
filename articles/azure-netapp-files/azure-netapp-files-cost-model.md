---
title: Kostnadsmodell för Azure NetApp-filer | Microsoft-dokument
description: Beskriver kostnadsmodellen för Azure NetApp-filer för hantering av utgifter från tjänsten.
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
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995104"
---
# <a name="cost-model-for-azure-netapp-files"></a>Kostnadsmodell för Azure NetApp Files 

Genom att förstå kostnadsmodellen för Azure NetApp Files kan du hantera dina utgifter från tjänsten.

## <a name="calculation-of-capacity-consumption"></a>Beräkning av kapacitetsförbrukning

Azure NetApp-filer faktureras på etablerad lagringskapacitet.  Etablerad kapacitet allokeras genom att skapa kapacitetspooler.  Kapacitetspooler faktureras baserat på $/provisioned-GiB/month i steg per timme. Den minsta storleken för en enda kapacitet pool är 4 TiB, och kapacitet pooler kan därefter utökas i 1-TiB steg. Volymer skapas inom kapacitetspooler.  Varje volym tilldelas en kvot som minskar från den pooler-etablerade kapaciteten. Den kvot som kan tilldelas volymer varierar från minst 100 GiB till högst 100 TiB.  

För en aktiv volym baseras kapacitetsförbrukning mot kvot på logisk (effektiv) kapacitet.

Om den faktiska kapacitetsförbrukningen för en volym överstiger dess lagringskvot kan volymen fortsätta att öka. Skrivningar är fortfarande tillåtna så länge den faktiska volymstorleken är mindre än systemgränsen (100 TiB).  

Den totala använda kapaciteten i en kapacitetspool mot det avsatta beloppet är summan av summan av antingen den tilldelade kvoten eller den faktiska förbrukningen av alla volymer inom poolen: 

   ![Beräkning av total använd kapacitet](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Diagrammet nedan illustrerar dessa begrepp.  
* Vi har en kapacitet pool med 4 TiB av etablerad kapacitet.  Poolen innehåller tre volymer.  
    * Volym 1 tilldelas en kvot på 2 TiB och har 800 GiB förbrukning.  
    * Volym 2 tilldelas en kvot på 1 TiB och har 100 GiB förbrukning.  
    * Volym 3 tilldelas en kvot på 500 GiB men har 800 GiB förbrukning (överanvändning).  
* Kapacitetspoolen mäts för 4 TiB kapacitet (det avstämmda beloppet).  
    3.8 TB kapacitet förbrukas (2 TiB och 1 TiB kvot från volym 1 och 2 och 800 GiB av den faktiska förbrukningen för volym 3). Och 200 GiB kapacitet återstår.

   ![Kapacitetspool med tre volymer](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Överförbrukning av kapacitet  

När den totala använda kapaciteten för en pool överskrider sin etablerade kapacitet är dataskrivningar fortfarande tillåtna.  Efter respitperioden (en timme), om poolens använda kapacitet fortfarande överstiger dess etablerade kapacitet, ökas poolstorleken automatiskt i steg om 1 tib tills den etablerade kapaciteten är större än den totala använda kapaciteten.  Till exempel, i bilden ovan, om volym 3 fortsätter att växa och den faktiska förbrukningen når 1,2 TiB, sedan efter respitperioden, kommer poolen automatiskt att ändra storlek till 5 TiB.  Resultatet är att den etablerade poolkapaciteten (5 TiB) överstiger den använda kapaciteten (4,2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Manuella ändringar av poolens storlek  

Du kan manuellt öka eller minska poolens storlek. Följande begränsningar gäller dock:
* Lägsta och högsta tillåtna servicegränser  
    Se artikeln om [resursgränser](azure-netapp-files-resource-limits.md).
* En 1-TiB ökning efter den första 4-TiB minsta köp
* En minsta faktureringsökning på en timme
* Den etablerade poolens storlek får inte minskas till mindre än den totala använda kapaciteten i poolen.

## <a name="behavior-of-maximum-size-pool-overage"></a>Beteende för högsta storlek pool överkörning   

Den maximala storleken på en kapacitetspool som du kan skapa eller ändra storlek på är 500 TiB.  När den totala använda kapaciteten i en kapacitetspool överstiger 500 TiB uppstår följande situationer:
* Dataskrivningar kommer fortfarande att tillåtas (om volymen är lägre än systemet högst 100 TiB).
* Efter en timmes respitperiod ändras poolen automatiskt i steg om 1-TiB, tills den poolbeetablerade kapaciteten överstiger den totala använda kapaciteten.
* Den ytterligare etablerade och fakturerade poolkapaciteten som överstiger 500 TiB kan inte användas för att tilldela volymkvot. Det kan inte heller användas för att utöka prestanda QoS gränser.  
    Se [servicenivåer](azure-netapp-files-service-levels.md) om prestandagränser och QoS-storlek.

Diagrammet nedan illustrerar följande begrepp:
* Vi har en kapacitetspool med en Premium-lagringsnivå och en kapacitet på 500 TIB. Poolen innehåller nio volymer.
    * Volymerna 1 till 8 tilldelas en kvot på 60 TiB vardera.  Den totala använda kapaciteten är 480 TiB.  
        Varje volym har en QoS-gräns på 3,75 GiB/s genomströmning (60 TiB * 64 MiB/s).  
    * Volym 9 tilldelas en kvot på 20 TiB.  
        Volym 9 har en QoS-gräns på 1,25 GiB/s genomströmning (20 TiB * 64 MiB/s).
* Volym 9 är ett överkörningsscenario. Den har 25 TiB av faktisk förbrukning.  
    * Efter en timmes respitperiod kommer kapacitetspoolen att storleksas om till 505 TiB.  
        Det vill än den totala använda kapaciteten = 8 * 60-TiB-kvot för volymerna 1 till 8 och 25 Tiab för den faktiska förbrukningen för volym 9.
    * Den fakturerade kapaciteten är 505 TiB.
    * Volymkvoten för volym 9 kan inte ökas (eftersom den totala tilldelade kvoten för poolen inte får överstiga 500 TiB).
    * Ytterligare QoS-dataflöde kanske inte tilldelas (eftersom den totala QoS för poolen fortfarande baseras på 500 TiB).

   ![Kapacitetspool med nio volymer](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Kapacitetsförbrukning för ögonblicksbilder 

Kapacitetsförbrukningen för ögonblicksbilder i Azure NetApp-filer debiteras mot kvoten för den överordnade volymen.  Som ett resultat delar den samma faktureringsgrad som kapacitetspoolen som volymen tillhör.  Till skillnad från den aktiva volymen mäts dock ögonblicksbildförbrukningen baserat på den inkrementella kapacitet som förbrukas.  Ögonblicksbilder av Azure NetApp-filer är av olika karaktär. Beroende på dataändringshastigheten förbrukar ögonblicksbilderna ofta mycket mindre kapacitet än den aktiva volymens logiska kapacitet. Anta till exempel att du har en ögonblicksbild av en 500-GiB-volym som bara innehåller 10 GiB differentiella data. Den kapacitet som debiteras mot volymkvoten för den ögonblicksbilden skulle vara 10 GiB, inte 500 GiB. 

## <a name="next-steps"></a>Nästa steg

* [Prissida för Azure NetApp-filer](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
