---
title: Tillstånd lagringsalternativ på Azure Service Fabric nät | Microsoft Docs
description: Lär dig mer om hur du lagrar på ett tillförlitligt sätt tillstånd i Service Fabric-nät program som körs på Azure Service Fabric-nät.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: ef51040d1bad74ee74d5901d1f5acbe875c02a07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810531"
---
# <a name="state-management-with-service-fabric"></a>Tillståndshantering med Service Fabric

Service Fabric stöder många olika alternativ för lagring av användartillstånd. En översikt över mönster för hantering av tillstånd och Service Fabric Se [Service Fabric-koncept: Tillstånd](/azure/service-fabric/service-fabric-concepts-state). Dessa samma koncept gäller oavsett om dina tjänster körs i eller utanför Service Fabric-nät. 

Med Service Fabric nät, kan du enkelt distribuera ett nytt program och ansluter den till ett befintligt dataarkiv i Azure. Förutom att använda någon fjärrdatabas, finns det flera alternativ för att lagra data, beroende på om tjänsten vill lokal eller fjärransluten lagring. 

## <a name="volumes"></a>Volymer

Behållare är ofta använda temporära diskar. Temporära diskar är dock tillfälliga, så du får en ny tillfällig disk och förlora informationen när en behållare kraschar. Det är också svårt att dela information om temporära diskar med andra behållare. Volymer är kataloger som monteras i dina behållarinstanser som du kan använda för att bevara tillstånd. Volymer ger dig allmänna fillagring och gör att du kan läsa/skriva filer med hjälp av normal disk-i/o-fil som API: er. Volymresursen beskriver hur du monterar en katalog och vilka datalagret för att använda. Du kan välja antingen Azure File storage eller Service Fabric volym disken för att lagra data.

![Volymer][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric Reliable Volume

Service Fabric tillförlitliga volym är en drivrutin för Docker-volym som används för att montera en lokal volym till en behållare. Läsningar och skrivningar finns lokala åtgärder och fast. Data replikeras ut till sekundära noder, vilket gör det med hög tillgänglighet. Redundans är också snabbt. När en behållare kraschar växlar den över till en nod som redan har en kopia av dina data. Ett exempel finns i [hur du distribuerar en app med Service Fabric tillförlitliga volym](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Azure Files-volym

Azure filer volym är en drivrutin för Docker-volym som används för att montera en Azure Files-resurs till en behållare. Azure Files storage använder nätverkslagring, så läser och skriver sker över nätverket. Jämfört med Service Fabric tillförlitliga volym, Azure Files storage är mindre effektivt men innehåller ett billigare och lämpliga alternativ. Ett exempel finns i [hur du distribuerar en app med Azure filer volym](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Nästa steg

Information om programmodellen finns [Service Fabric-resurser](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
