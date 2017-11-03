---
title: "Azure-behållaren registret databaser | Microsoft Docs"
description: "Hur du använder Azure-behållare registret databaser för Docker bilder"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.openlocfilehash: 2090d4c951e2261529bf1b7b361510d5822060a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Azure-behållaren registret databaser

Azure Container register är kompatibla med en mängd olika tjänster och orchestrators. Om du vill göra det enklare att spåra källa tjänster och agenter som ACR används, har vi igång med Docker huvudfältet i filen Docker.config.



## <a name="viewing-repositories-in-the-portal"></a>Visa databaser i portalen

ACR-huvuden följa formatet:
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Molnet: Azure, Azure Stack eller andra myndigheter eller landsspecifika Azure-moln. Även om Azure-stacken och offentliga moln inte stöds för närvarande, kan den här parametern framtida stöd.
* Tjänst: namnet på tjänsten.
* Optionalservicename: valfri parameter för tjänster med subservices eller ange en SKU (ex: webbappar motsvarar Azure/app-/-webbappar).

Partnertjänster och orchestrators uppmuntras att använda specifika huvudvärden för att hjälpa till med våra telemetri. Användare kan också ändra värdet som skickas till huvudet om de så önskar.

Värdena som vi vill ACR partner att använda för att fylla i fältet ”X-Meta-käll-klient” är nedan:

| Tjänstnamn              | Huvudet                                |
| ------------------------- | ------------------------------------- |
| Azure Container Service   | Azure/beräkning/azure-behållaren-tjänst |
| App Service - Webbappar    | Azure/app-/-webbappar            |
| Apptjänst - Logic Apps  | Azure/app-tjänsten /-logikappar          |
| Batch                     | batch-Azure/beräkning                   |
| Cloud-konsolen             | Azure-cloud-konsolen                   |
| Funktioner                 | Azure-beräknings-funktioner               |
| Internet saker - hubb  | Azure-iot-hubb                         |
| HDInsight                 | hdinsight-Azure/data                  |
| Jenkins                   | Azure/jenkins                         |
| Machine Learning          | Azure/data/machile-utbildning           |
| Service Fabric            | Azure/beräkning/service-infrastrukturresurser          |
| VSTS                      | Azure/vsts                            |


## <a name="next-steps"></a>Nästa steg
[Lär dig mer om register, tjänster som stöds och orchestrators](container-registry-intro.md)
