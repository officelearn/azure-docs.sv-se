---
title: Prestandamått för Azure NetApp Files | Microsoft Docs
description: Beskriver resultaten från benchmark-testerna för prestanda för Azure NetApp filer på volymnivå.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870889"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Prestandamått för Azure NetApp-filer

Den här artikeln beskrivs resultaten från benchmark-testerna för prestanda för Azure NetApp filer på volymnivå. 

## <a name="sample-application-used-for-the-tests"></a>Exempelprogrammet som används för testerna

Prestandatester kördes med ett exempelprogram som använder Azure NetApp-filer. Programmet har följande egenskaper: 

* Ett Linux-baserade program som skapats för molnet
* Kan skalas linjärt med har lagts till virtuella datorer (VM) för att öka beräkningskraft efter behov
* Kräver snabb tillgängligheten för data lake
* Har i/o-mönster som ibland är slumpmässig och ibland sekventiella 
    * Ett slumpmässigt mönster kräver låg fördröjning för stora mängder i/o. 
    * Ett sekventiellt mönster kräver stora mängder bandbredd. 

## <a name="about-the-workload-generator"></a>Om arbetsbelastningen generator

Resultatet kommer från Vdbench sammanfattning av filer. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) är ett kommandoradsverktyg som genererar diskens i/o-arbetsbelastningar för att verifiera lagringsprestanda. Konfigurationen för klient-server som används är skalbar.  Den innehåller en enda blandat master/klient och klient-14 dedikerade VM.

## <a name="about-the-tests"></a>Om testerna

Testerna har utformats för att identifiera de gränser som exempelprogrammet kan ha och svarstiden som kurvor upp till gränserna.  

Följande tester kördes: 

* 100% 8 KiB slumpmässiga Läs
* 100% 8 KiB slumpmässiga skrivåtgärder
* 100% 64 KiB sekventiella Läs
* 100% 64 KiB sekventiella skrivåtgärder
* 50% 64 KiB sekventiell Läs-, 50% 64 KiB sekventiella skrivåtgärder
* 50% 8 KiB slumpmässiga Läs, 50% 8 KiB slumpmässiga skrivåtgärder

## <a name="bandwidth"></a>Bandbredd

NetApp-filer som Azure erbjuder flera [servicenivåer](azure-netapp-files-service-levels.md). Varje servicenivå erbjuder en annan mängd bandbredd per TiB etablerad kapacitet (kvot för samtalsvolym). Bandbreddsgräns för en volym etableras baserat på en kombination av servicenivån och kvot för samtalsvolym. Observera att bandbreddsbegränsningen är bara en faktor för att fastställa den faktiska mängden dataflöde som kommer realiseras.  

4 500 MiB är för närvarande högsta dataflöde som har uppnåtts av en arbetsbelastning mot en enskild volym i testet.  En kvot för samtalsvolym av 70.31 TiB med önskad service Premium-nivå kommer att etablera tillräckligt med bandbredd för att använda den här dataflöde per beräkningen nedan: 

![Bandbredd formel](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kvot- och tjänstnivå](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Dataflöde-intensiva arbetsbelastningar

Dataflöde testet används Vdbench och en kombination av 12xD32s V3 virtuella datorer. Exemplet volymen i testet uppnås följande dataflöde nummer:

![Testning av dataflöde](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O-intensiva arbetsbelastningar

I/o-testet används Vdbench och en kombination av 12xD32s V3 virtuella datorer. Exemplet volymen i testet uppnås följande i/o-nummer:

![I/o-test](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Svarstid

Avståndet mellan testet virtuella datorer och Azure NetApp Files volymen påverkar ett i/o-prestanda.  I tabellen nedan jämförs IOPS jämfört med svarstid svar kurvor för två olika uppsättningar av virtuella datorer.  En uppsättning virtuella datorer som är nära Azure NetApp filer och den andra uppsättningen är mer direkt.  Observera att ökad latens för ytterligare uppsättningen av virtuella datorer har en inverkan på mängden IOPS uppnås på en viss nivå av parallellitet.  Oavsett kan läsning mot en volym överstiga 300 000 IOPS som på bilden nedan: 

![Svarstid-studien](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Sammanfattning

Känslig arbetsbelastningar (databaser) kan ha en en millisekunds svarstid. Transaktionell prestanda kan vara än 300 kB IOPS för en enskild volym.

Dataflöde fördöjningskänsliga program (för direktuppspelning och avbildning) kan ha 4.5GiB / s genomströmning.
