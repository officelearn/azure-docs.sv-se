---
title: Tjänste planer och kvoter för Azure våren Cloud
description: Läs mer om tjänst kvoter och service planer för Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 496f2e812a102e85fea92a535552daaaadf5f31e
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093437"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvoter och service planer för Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Alla Azure-tjänster anger standard gränser och kvoter för resurser och funktioner.   Azure våren Cloud erbjuder två pris nivåer: Basic och standard. Vi kommer att ha detalj begränsningar för båda nivåerna i den här artikeln.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure våren Cloud Service-nivåer och-gränser

| Resurs | Basic | Standard
------- | ------- | -------
Virtuell processor | 1 per tjänst instans | 4 per tjänst instans
Minne | 2 GB per tjänst instans | 8 GB per tjänst instans
Azure våren Cloud Service-instanser per region per prenumeration | 10 | 10
Totalt antal App-instanser per Azure våren Cloud Service-instans | 25 | 500
Beständiga volymer | 1 GB/app x 10-appar | 50 GB/app x 10-appar

## <a name="next-steps"></a>Nästa steg

Vissa standard gränser kan ökas. Om installationen kräver en ökning skapar du [en support förfrågan](../azure-portal/supportability/how-to-create-azure-support-request.md).