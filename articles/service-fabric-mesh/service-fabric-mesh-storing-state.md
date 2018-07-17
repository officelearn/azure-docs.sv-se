---
title: Tillstånd lagringsalternativ på Azure Service Fabric nät | Microsoft Docs
description: Lär dig mer om hur du lagrar på ett tillförlitligt sätt tillstånd i Service Fabric-nät program som körs på Azure Service Fabric-nät.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076660"
---
# <a name="state-management-with-service-fabric"></a>Tillståndshantering med Service Fabric
Service Fabric stöder många olika alternativ för lagring av användartillstånd. En översikt över mönster för hantering av tillstånd och Service Fabric Se [Service Fabric-koncept: tillstånd](/azure/service-fabric/service-fabric-concepts-state). Dessa samma koncept gäller oavsett om dina tjänster körs i eller utanför Service Fabric-nät. 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Statliga lagringsalternativ i Azure Service Fabric nät
Med Service Fabric nät, kan du enkelt distribuera ett nytt program och ansluter den till ett befintligt dataarkiv i Azure. Förutom att använda någon fjärrdatabas, finns det flera alternativ för att lagra data, beroende på om tjänsten vill lokal eller fjärransluten lagring. 

* Lokalt lagrade replikerade data
  * Tillförlitliga samlingar (inte tillgängligt i förhandsversionen)
    * Ett bibliotek som implementerar datastrukturer som köer och nyckel / värde-par att använda i tjänsten
    * Detta ger det enklaste och snabbaste sättet att interagera med data, samtidigt som det ger enkel partition routning i kombination med intelligenta routning i Service Fabric-nät
  * Drivrutin för Service Fabric-volymer (inte tillgängligt i förhandsversionen)
    * En drivrutin för volymen docker för att montera en lokal volym till en behållare
    * Detta ger dig den ultimata flexibiliteten i lagra data lokalt, via alla API: er, som har stöd för fillagring.

* Fjärrlagring
  * Azure filer volym-drivrutin
    * En drivrutin för volymen docker för att montera en Azure Files-resurs till en behållare
    * Ger dig ett mindre effektivt, men billigare fullständig också är flexibla och pålitliga dataalternativet via alla API: er, som har stöd för fillagring.
    * [Här guiden: distribuera en app med Azure Files-volym](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>Nästa steg

Information om programmodellen finns [Service Fabric-resurser](service-fabric-mesh-service-fabric-resources.md)
