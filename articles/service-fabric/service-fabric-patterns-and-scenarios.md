---
title: Azure Service Fabric-mönster och scenarier | Microsoft Docs
description: Läs om bästa praxis och beprövade, återanvändbara mönster för att designa, utveckla och kör dina mikrotjänster på Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 3bd77891cc7508eeb1fee2152d37478c654a7e37
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294714"
---
# <a name="service-fabric-patterns-and-scenarios"></a>Service Fabric-mönster och scenarier
Om du tittar på att bygga storskaliga mikrotjänster som använder Azure Service Fabric, lär du dig från experterna som utformat och skapat den här plattformen som en tjänst (PaaS). Kom igång med rätt arkitektur och lär dig att optimera resurser för ditt program. Den [Service Fabric mönster och praxis](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) kursen får du svar på frågor som oftast och av verkliga kunder svar om Service Fabric-scenarier och moduler.
 
Lär dig hur du utformar, utvecklar och kör dina mikrotjänster på Service Fabric på det bästa sättet med beprövade och återanvändbara mönster. Få en översikt över Service Fabric och sedan gör en djupdykning i avsnitten som innehåller klustret optimering och säkerhet, migrera äldre appar IoT i skala, som är värd för spelmotorer och mycket mer. Titta på kontinuerlig leverans för olika arbetsbelastningar och även få information om Linux-support och behållare. 

## <a name="introduction"></a>Introduktion
Utforska metodtips och Läs om hur du väljer plattform som en tjänst (PaaS) över infrastruktur som en tjänst (IaaS). Få information om följande designprinciper för beprövade program.

<table><tr><th>Video</th><th>PowerPoint-presentationer</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Introduktion till Service Fabric</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>Planering och hantering av kluster
Läs mer om kapacitetsplanering kluster och optimering Klustersäkerhet i den här titt på Azure Service Fabric.

<table><tr><th>Video</th><th>PowerPoint-presentationer</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">Klustret planering och hantering</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>Storskalig web
Granska information om webb-storskaliga, inklusive tillgänglighet och tillförlitlighet, storskaliga och tillståndshantering.

<table><tr><th>Video</th><th>PowerPoint-presentationer</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">Storskalig web</a></td></tr>
</table>

## <a name="iot"></a>IoT
Utforska Internet of Things (IoT) i samband med Azure Service Fabric, inklusive Azure IoT-pipeline, flera innehavare och IoT i stor skala.

<table><tr><th>Video</th><th>PowerPoint-presentationer</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>Spel
Titta på tur-baserade spel, interaktiva spel och som är värd för befintliga spelmotorer.

<table><tr><th>Video</th><th>PowerPoint-presentationer</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Spel</a></td></tr>
</table>

## <a name="continuous-delivery"></a>Kontinuerlig leverans
Utforska begrepp, inklusive kontinuerlig integrering/kontinuerlig leverans med Azure Pipelines, build/paket/publicera arbetsflöde, installation av flera och tjänsten paketresursen.

<table><tr><th>Video</th><th>PowerPoint-presentationer</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">Kontinuerlig leverans</a></td></tr>
</table>

## <a name="migration"></a>Migrering
Lär dig mer om hur du migrerar från en tjänst i molnet, förutom att migrering av äldre appar.

<table><tr><th>Video</th><th>PowerPoint-presentationer</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">Migrering</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>Behållare och Linux-support
Få svar på frågan ”varför behållare”? Läs mer om förhandsversionen för Windows-behållare, har stöd för Linux och Linux-behållare samordning. Dessutom ta reda på hur du migrerar .NET Core-appar till Linux.

<table><tr><th>Video</th><th>PowerPoint-presentationer</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">Behållare och Linux-support</a></td></tr>
</table>

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig om Service Fabric-mönster och scenarier, Läs mer om hur du [skapa och hantera kluster](service-fabric-deploy-anywhere.md), [migrera appar för Cloud Services till Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [konfigurera kontinuerlig leverans](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), och [distribuera behållare](service-fabric-containers-overview.md).
