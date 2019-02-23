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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668352"
---
# <a name="service-levels-for-azure-netapp-files"></a>Tjänstnivåer för Azure NetApp Files
Azure NetApp-filer stöder två tjänstnivåer: Premium och Standard. 

## <a name="Premium"></a>Premium-lagring

Den *Premium* storage tillhandahåller upp till 64 MiB/s per TiB dataflöde. Dataflödesprestanda indexeras mot kvot för samtalsvolym. En volym från Premium-lagring med 2 TiB etablerade kvot (oavsett faktisk förbrukning) har exempelvis ett dataflöde på 128 MiB/s.

## <a name="Standard"></a>Standardlagring

Den *Standard* storage tillhandahåller upp till 16 MiB/s per TiB dataflöde. Dataflödesprestanda indexeras mot kvot för samtalsvolym. En volym från standardlagring med 2 TiB etablerade kvot (oavsett faktisk förbrukning) har exempelvis ett dataflöde på 32 MiB/s.

## <a name="next-steps"></a>Nästa steg

- Se den [sidan med priser för Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) för olika servicenivåer
- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
