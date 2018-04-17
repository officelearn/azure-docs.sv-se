---
title: Kontrollera händelseloggarna för behållare i Azure Service Fabric | Microsoft Docs
description: Beskriver hur du visar behållaren loggar för ett Service Fabric-behållartjänster som körs med hjälp av Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Visa loggfiler för en tjänst för Service Fabric-behållare
Azure Service Fabric är en behållare orchestrator och stöder både [behållare för Linux och Windows](service-fabric-containers-overview.md).  Den här artikeln beskriver hur du visar behållaren loggar en körs container Service så att du kan diagnostisera och felsöka problem.

## <a name="access-container-logs"></a>Behållaren åtkomstloggar
Behållaren loggar kan nås med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  I en webbläsare, öppna Service Fabric Explorer från klusterslutpunkten management genom att gå till [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Behållaren loggfilerna finns på den noden i klustret som container service-instans som körs på. Exempelvis hämta loggarna för web front-end behållaren med den [Linux röstning exempelprogrammet](service-fabric-quickstart-containers-linux.md). I trädvyn expanderar **klustret**>**program**>**VotingType**>**fabric: / Röstningsdatabasen / azurevotefront**.  Sedan expandera partitionen (d1aa737e-f22a-e347-be16-eec90be24bc1, i det här exemplet) och att behållaren körs på klusternoden *_lnxvm_0*.

Hitta kodpaketet i trädvyn på den *_lnxvm_0* nod genom att expandera **noder**>**_lnxvm_0**>**fabric: / röstning**  > **azurevotfrontPkg**>**kod paket**>**kod**.  Välj sedan den **behållare loggar** alternativet för att visa loggar för behållaren.

![Service Fabric-plattform][Image1]


## <a name="next-steps"></a>Nästa steg
- Gå igenom den [skapa ett Linux-behållaren självstudien](service-fabric-tutorial-create-container-images.md).
- Lär dig mer om [Service Fabric och behållare](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
