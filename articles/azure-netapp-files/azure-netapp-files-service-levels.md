---
title: Tjänstnivåer för Azure NetApp-filer | Microsoft-dokument
description: Beskriver dataflödesprestanda för tjänstnivåerna för Azure NetApp-filer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832589"
---
# <a name="service-levels-for-azure-netapp-files"></a>Tjänstnivåer för Azure NetApp Files
Servicenivåer är ett attribut för en kapacitetspool. Servicenivåer definieras och differentieras med det tillåtna maximala dataflödet för en volym i kapacitetspoolen baserat på den kvot som har tilldelats volymen.

## <a name="supported-service-levels"></a>Servicenivåer som stöds

Azure NetApp Files stöder tre tjänstnivåer: *Ultra*, *Premium*och *Standard*. 

* <a name="Ultra"></a>Ultra lagring

    Ultra-lagringsnivån ger upp till 128 MiB/s dataflöde per 1 TiB volymkvot tilldelad. 

* <a name="Premium"></a>Premium-lagring

    Premium-lagringsnivån ger upp till 64 MiB/s dataflöde per 1 TiB volymkvot tilldelad. 

* <a name="Standard"></a>Standardlagring

    Standardlagringsnivån ger upp till 16 MiB/s dataflöde per 1 TiB volymkvot tilldelad.

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Genomströmningsgränsen för en volym bestäms av kombinationen av följande faktorer:
* Servicenivån för den kapacitetspool som volymen tillhör
* Den kvot som tilldelats volymen  

Detta begrepp illustreras i diagrammet nedan:

![Illustration på servicenivå](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

I exempel 1 ovan tilldelas en volym från en kapacitetspool med Premium-lagringsnivån som tilldelas 2 TiB kvot en dataflödesgräns på 128 MiB/s (2 TiB * 64 MiB/s). Det här scenariot gäller oavsett kapacitetspoolstorlek eller faktisk volymförbrukning.

I exempel 2 ovan tilldelas en volym från en kapacitetspool med Premium-lagringsnivån som tilldelas 100 GiB kvot en dataflödesgräns på 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Det här scenariot gäller oavsett kapacitetspoolstorlek eller faktisk volymförbrukning.

## <a name="next-steps"></a>Nästa steg

- Priserna för olika tjänstnivåer finns på [prissättningssidan för Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Se [Kostnadsmodell för Azure NetApp-filer](azure-netapp-files-cost-model.md) för beräkning av kapacitetsförbrukningen i en kapacitetspool 
- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
- Se [Service Level Agreement (SLA) för Azure NetApp-filer](https://azure.microsoft.com/support/legal/sla/netapp/)