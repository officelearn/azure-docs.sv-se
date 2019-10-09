---
title: Tjänste planer och kvoter för Azure våren Cloud
description: Läs mer om tjänst kvoter och service planer för Azure våren Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038787"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Kvoter och service planer för Azure våren Cloud

Alla Azure-tjänster anger standard gränser och kvoter för resurser och funktioner.  Under för hands versions perioden erbjuder Azure våren Cloud bara en tjänste plan.

Den här artikeln beskriver de tjänst kvoter som erbjuds under den aktuella för hands perioden.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure våren Cloud Service-nivåer och kvoter

Under för hands versions perioden erbjuder Azure våren Cloud bara en tjänst nivå.

Resource | Belopp
------- | -------
Virtuell processor | 4
Minne | 8 GBytes
Azure våren Cloud-prenumeration | 1
Azure våren Cloud Service-instanser per region per prenumeration | 2
Totalt antal App-instanser per Azure våren Cloud Service-instans | 50
Totalt antal App-instanser per våren-program | 20
Beständiga volymer | 10 x 50 GBytes

När du når en kvot får du ett 400-fel som läser: "Kvoten överskrider gränsen för prenumeration *på prenumerationen i region* *region där moln tjänsten Azure våren skapas*.

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om din resurs kräver en ökning skickar du oss din begäran: azure-spring-cloud@service.microsoft.com.
