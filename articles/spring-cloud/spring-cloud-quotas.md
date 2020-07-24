---
title: Tjänste planer och kvoter för Azure våren Cloud
description: Läs mer om tjänst kvoter och service planer för Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cda22c63a70c5121e6a6972c66bdc0a4bb5158fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089472"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvoter och service planer för Azure våren Cloud

Alla Azure-tjänster anger standard gränser och kvoter för resurser och funktioner.   Azure våren Cloud erbjuder två pris nivåer: Basic och standard. Vi kommer att ha detalj begränsningar för båda nivåerna i den här artikeln.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure våren Cloud Service-nivåer och-gränser

| Resurs | Basic | Standard
------- | ------- | -------
Virtuell processor | 1 per tjänst instans | 4 per tjänst instans
Minne | 2 GB per tjänst instans | 8 GB per tjänst instans
Azure våren Cloud Service-instanser per region per prenumeration | 10 | 10
Totalt antal App-instanser per Azure våren Cloud Service-instans | 25 | 500
Beständiga volymer | 1 GB/app x 10-appar | 50 GB/app x 10-appar


Under för hands versions perioden erbjuder Azure våren Cloud bara en tjänst nivå. När du når en gräns får du ett 400-fel som läser: "kvoten överskrider gränsen för prenumeration *på prenumerationen i region* *region där din Azure våren Cloud Service skapas*.

## <a name="next-steps"></a>Nästa steg

Vissa standard gränser kan ökas. Om installationen kräver en ökning skapar du [en support förfrågan](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
