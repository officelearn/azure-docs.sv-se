---
title: Tjänste planer och kvoter för Azure våren Cloud
description: Läs mer om tjänst kvoter och service planer för Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278897"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvoter och service planer för Azure våren Cloud

Alla Azure-tjänster anger standard gränser och kvoter för resurser och funktioner.  Under för hands versions perioden erbjuder Azure våren Cloud bara en tjänste plan.

Den här artikeln beskriver de tjänst kvoter som erbjuds under den aktuella för hands perioden.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure våren Cloud Service-nivåer och kvoter

Under för hands versions perioden erbjuder Azure våren Cloud bara en tjänst nivå.

Resurs | Belopp
------- | -------
Virtuell processor | 4 per tjänst instans
Minne | 8 GBytes per tjänst instans
Azure våren Cloud Service-instanser per region per prenumeration | 10
Totalt antal App-instanser per Azure våren Cloud Service-instans | 500
Totalt antal App-instanser per våren-program | 20
Beständiga volymer | 10 x 50 GBytes

När du når en kvot får du ett 400-fel som läser: "kvoten överskrider gränsen för prenumeration *på prenumerationen i region* *region där din Azure våren Cloud Service skapas*.

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om din resurs kräver en ökning kan du [skapa en support förfrågan](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
