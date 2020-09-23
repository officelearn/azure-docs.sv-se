---
title: Tjänste planer och kvoter för Azure våren Cloud
description: Läs mer om tjänst kvoter och service planer för Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7c4c832819f61d208d0722823d0a74354960f182
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904270"
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

Vissa standard gränser kan ökas. Om installationen kräver en ökning skapar du [en support förfrågan](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
