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
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: a10ac319fe362011531e00f832bb8e471fd56fdf
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430901"
---
# <a name="service-levels-for-azure-netapp-files"></a>Servicenivåer för Azure NetApp-filer
Azure NetApp-filer stöder två tjänstnivåer: Premium och Standard. 

## <a name="Premium"></a>Premium-lagring

Den *Premium* storage tillhandahåller upp till 64 MiB/s per TiB dataflöde. Dataflödesprestanda indexeras mot kvot för samtalsvolym. En volym från Premium-lagring med 2 TiB etablerade kvot (oavsett faktisk förbrukning) har exempelvis ett dataflöde på 128 MiB/s.

## <a name="Standard"></a>Standardlagring

Den *Standard* storage tillhandahåller upp till 16 MiB/s per TiB dataflöde. Dataflödesprestanda indexeras mot kvot för samtalsvolym. En volym från standardlagring med 2 TiB etablerade kvot (oavsett faktisk förbrukning) har exempelvis ett dataflöde på 32 MiB/s.

## <a name="next-steps"></a>Nästa steg

- Se den [sidan med priser för Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) för olika servicenivåer
- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
