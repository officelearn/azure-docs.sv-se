---
title: Service nivåer för Azure NetApp Files | Microsoft Docs
description: Beskriver data flödes prestanda för service nivåer för Azure NetApp Files.
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
ms.date: 10/06/2020
ms.author: b-juche
ms.openlocfilehash: 1c64bd10b34b61797cb3bf3de0cd7d2aa819e795
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777146"
---
# <a name="service-levels-for-azure-netapp-files"></a>Tjänstnivåer för Azure NetApp Files
Service nivåer är ett attribut för en pool med kapacitets värden. Service nivåer definieras och särskiljs av det tillåtna maximala data flödet för en volym i kapacitetsutnyttjandet baserat på den kvot som har tilldelats till volymen.

## <a name="supported-service-levels"></a>Service nivåer som stöds

Azure NetApp Files stöder tre service nivåer: *Ultra*, *Premium*och *standard*. 

* <a name="Ultra"></a>Ultra Storage

    Ultra Storage-nivån ger upp till 128 MiB/s data flöde per 1 TiB med kapacitet. 

* <a name="Premium"></a>Premium-lagring

    Premium Storage-nivån ger upp till 64 MiB/s genom strömning per 1 TiB kapacitet. 

* <a name="Standard"></a>Standard lagring

    Standard lagrings nivån ger upp till 16 MiB/s genom strömning per 1 TiB kapacitet.

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Data flödes gränsen för en volym bestäms av kombinationen av följande faktorer:
* Service nivå för den kapacitets pool som volymen tillhör
* Kvoten som tilldelats volymen  
* QoS-typen (*Auto* eller *manuell*) för kapacitets gruppen  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Data flödes gräns exempel på volymer i en pool med automatisk QoS-kapacitet

Diagrammet nedan visar exempel på data flödes gränser för volymer i en pool med automatisk QoS-kapacitet:

![Bild på service nivå](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* I exempel 1 ovan tilldelas en volym från en pool med automatisk QoS-kapacitet med Premium Storage-nivån som tilldelas 2 TiB kvoten en data flödes gräns på 128 MiB/s (2 TiB * 64 MiB/s). Det här scenariot gäller oavsett storleken på kapacitets gruppen eller den faktiska volym förbrukningen.

* I exempel 2 ovan tilldelas en volym från en pool med automatisk QoS-kapacitet med Premium Storage-nivån som tilldelas 100 GiB av kvoten en data flödes gräns på 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Det här scenariot gäller oavsett storleken på kapacitets gruppen eller den faktiska volym förbrukningen.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Data flödes gräns exempel på volymer i en manuell pool för QoS-kapacitet 

Om du använder en manuell pool för QoS-kapacitet kan du tilldela kapaciteten och data flödet för en volym oberoende av varandra. När du skapar en volym i en manuell pool för QoS-kapacitet kan du ange data flöde (MiB/S)-värdet. Det totala data flödet som tilldelats volymer i en manuell QoS-kapacitet beror på Poolens storlek och på tjänst nivå. Det är ett tak av (storleken på TiB x service nivå genom strömning/TiB). Till exempel har en pool med 10 TiB kapacitet med Ultra Service nivån en total data flödes kapacitet på 1280 MiB/s (10 TiB x 128 MiB/s/TiB) tillgängliga för volymerna.

För ett SAP HANA system kan den här kapacitets poolen användas för att skapa följande volymer. Varje volym ger en individuell storlek och data flöde som uppfyller dina program krav:

* SAP HANA data volym: storlek 4 TiB med upp till 704 MiB/s
* SAP HANA logg volym: storlek 0,5 TiB med upp till 256 MiB/s
* SAP HANA delad volym: storlek 1 TiB med upp till 64 MiB/s
* SAP HANA säkerhets kopierings volym: storlek 4,5 TiB med upp till 256 MiB/s

Diagrammet nedan visar scenarier för SAP HANA volymer:

![QoS-SAP HANA volym scenarier](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Nästa steg

- [Sidan Azure NetApp Files prissättning](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Kostnadsmodell för Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
- [Serviceavtal (SLA) för Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Ändra tjänstnivå för en volym dynamiskt](dynamic-change-volume-service-level.md) 
- [Service nivå mål för replikering över flera regioner](cross-region-replication-introduction.md#service-level-objectives)
