---
title: Servicenivåer för Azure NetApp Files | Microsoft Docs
description: Beskriver kapaciteten för servicenivåer för Azure NetApp-filer.
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
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523109"
---
# <a name="service-levels-for-azure-netapp-files"></a>Tjänstnivåer för Azure NetApp Files
Servicenivåer är ett attribut för en pool kapacitet. Servicenivåer definieras och differentierade av tillåtna maximalt dataflöde för en volym i kapacitet poolen baserat på den kvot som har tilldelats till volymen.

## <a name="supported-service-levels"></a>Stöds servicenivåer

Azure NetApp-filer stöder tre tjänstnivåer: *Ultra*, *Premium*, och *Standard*. 

* <a name="Ultra"></a>Ultra lagring

    Ultra lagringsnivå tillhandahåller upp till 128 MiB/s genomströmning per 1 TiB tilldelad kvot för samtalsvolym. 

* <a name="Premium"></a>Premium-lagring

    Premium storage-nivå ger upp till 64 MiB/s genomströmning per 1 TiB tilldelad kvot för samtalsvolym. 

* <a name="Standard"></a>Standardlagring

    Standard storage-nivån ger upp till 16 MiB/s genomströmning per 1 TiB tilldelad kvot för samtalsvolym.

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Dataflödesgräns för en volym bestäms av kombinationen av följande faktorer:
* Servicenivå på poolen kapacitet som volymen tillhör
* Kvoten för volymen  

Detta begrepp visas i diagrammet nedan:

![Service på bild](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

I ovanstående exempel 1, en volym från en kapacitet pool med Premium storage-nivå som är tilldelad 2 TiB kvot kommer att tilldelas ett dataflöde på högst 128 MiB/s (2 TiB * 64 MiB/s). Det här scenariot gäller oavsett poolstorlek kapacitet eller faktiska förbrukningen.

I ovanstående exempel 2, kommer en volym från en kapacitet pool med Premium storage-nivå som är tilldelad kvot 100 GiB tilldelas en dataflödesgräns på 6.25 MiB/s (0.09765625 TiB * 64 MiB/s). Det här scenariot gäller oavsett poolstorlek kapacitet eller faktiska förbrukningen.

## <a name="next-steps"></a>Nästa steg

- Priserna för olika tjänstnivåer finns på [prissättningssidan för Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Se [kostnadsmodell för Azure NetApp Files](azure-netapp-files-cost-model.md) för beräkning av användningen av kapacitet i poolen kapacitet 
- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)