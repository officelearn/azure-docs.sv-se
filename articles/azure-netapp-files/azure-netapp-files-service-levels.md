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
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75832589"
---
# <a name="service-levels-for-azure-netapp-files"></a>Tjänstnivåer för Azure NetApp Files
Service nivåer är ett attribut för en pool med kapacitets värden. Service nivåer definieras och särskiljs av det tillåtna maximala data flödet för en volym i kapacitetsutnyttjandet baserat på den kvot som har tilldelats till volymen.

## <a name="supported-service-levels"></a>Service nivåer som stöds

Azure NetApp Files stöder tre service nivåer: *Ultra*, *Premium*och *standard*. 

* <a name="Ultra"></a>Ultra Storage

    Den ultra lagrings nivån ger upp till 128 MiB/s av data flöde per 1 TiB volym kvot tilldelad. 

* <a name="Premium"></a>Premium-lagring

    Premium Storage-nivån ger upp till 64 MiB/s av data flöde per 1 TiB volym kvot tilldelad. 

* <a name="Standard"></a>Standard lagring

    Standard lagrings nivån ger upp till 16 MiB/s med data flöde per 1 TiB volym kvot tilldelad.

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Data flödes gränsen för en volym bestäms av kombinationen av följande faktorer:
* Service nivå för den kapacitets pool som volymen tillhör
* Kvoten som tilldelats volymen  

Det här konceptet illustreras i diagrammet nedan:

![Bild på service nivå](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

I exempel 1 ovan tilldelas en volym från en kapacitets pool med Premium Storage-nivån som tilldelas 2 TiB kvoten en data flödes gräns på 128 MiB/s (2 TiB * 64 MiB/s). Det här scenariot gäller oavsett storleken på kapacitets gruppen eller den faktiska volym förbrukningen.

I exempel 2 ovan tilldelas en volym från en kapacitets pool med Premium Storage-nivån som tilldelas 100 GiB av kvoten en data flödes gräns på 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Det här scenariot gäller oavsett storleken på kapacitets gruppen eller den faktiska volym förbrukningen.

## <a name="next-steps"></a>Nästa steg

- Priserna för olika tjänstnivåer finns på [prissättningssidan för Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Se [kostnads modell för Azure NetApp Files](azure-netapp-files-cost-model.md) för att beräkna kapacitets förbrukningen i en kapacitets pool 
- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
- Se [serviceavtal (SLA) för Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)