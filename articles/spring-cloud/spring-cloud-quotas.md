---
title: Tjänste planer och kvoter för Azure våren Cloud
description: Läs mer om tjänst kvoter och service planer för Azure våren Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607673"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvoter och service planer för Azure våren Cloud

Alla Azure-tjänster anger standard gränser och kvoter för resurser och funktioner.  Under för hands versions perioden erbjuder Azure våren Cloud bara en tjänste plan.

Den här artikeln beskriver de tjänst kvoter som erbjuds under den aktuella för hands perioden.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure våren Cloud Service-nivåer och kvoter

Under för hands versions perioden erbjuder Azure våren Cloud bara en tjänst nivå.

Resurs | Belopp
------- | -------
Virtuell processor | 4
Minne | 8 GBytes
Azure våren Cloud-prenumeration | 1
Azure våren Cloud Service-instanser per region per prenumeration | 2
Totalt antal App-instanser per Azure våren Cloud Service-instans | 50
Totalt antal App-instanser per våren-program | 20
Beständiga volymer | 10 x 50 GBytes

När du når en kvot får du ett 400-fel som läser: "kvoten överskrider gränsen för prenumeration *på prenumerationen i region* *region där din Azure våren Cloud Service skapas*.

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om din resurs kräver en ökning kan du [skapa en support förfrågan](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
