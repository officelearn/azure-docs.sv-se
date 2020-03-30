---
title: Alternativ för tillståndslagring på Azure Service Fabric Mesh
description: Lär dig mer om tillförlitlig lagring av tillstånd i Service Fabric Mesh-program som körs på Azure Service Fabric Mesh.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259102"
---
# <a name="state-management-with-service-fabric"></a>Statlig förvaltning med Service Fabric

Service Fabric stöder många olika alternativ för tillståndslagring. En begreppsmässig översikt över tillståndshanteringsmönstren och servicestrukturen finns i [Service Fabric Concepts: State](/azure/service-fabric/service-fabric-concepts-state). Alla dessa samma begrepp gäller oavsett om dina tjänster körs inom eller utanför Service Fabric Mesh. 

Med Service Fabric Mesh kan du enkelt distribuera ett nytt program och ansluta det till ett befintligt datalager som finns i Azure. Förutom att använda en fjärrdatabas finns det flera alternativ för att lagra data, beroende på om tjänsten önskar lokal lagring eller fjärrlagring. 

## <a name="volumes"></a>Volymer

Behållare använder ofta tillfälliga diskar. Tillfälliga diskar är dock tillfälliga, så du får en ny tillfällig disk och förlorar informationen när en behållare kraschar. Det är också svårt att dela information om tillfälliga diskar med andra behållare. Volymer är kataloger som monteras i behållarinstanserna som du kan använda för att bevara tillståndet. Volymer ger dig allmänt ändamål fillagring och kan du läsa / skriva filer med hjälp av vanliga disk I / O-fil API: er. Volymresursen beskriver hur du monterar en katalog och vilket stödlagring som ska användas. Du kan välja antingen Azure File Storage eller Service Fabric Volume disk för att lagra data.

![Volymer][image3]

### <a name="service-fabric-reliable-volume"></a>Tillförlitlig volym för serviceinfrastruktur

Service Fabric Reliable Volume är en Docker-volymdrivrutin som används för att montera en lokal volym på en behållare. Läser och skriver är lokala operationer och snabba. Data replikeras till sekundära noder, vilket gör dem mycket tillgängliga. Redundans är också snabb. När en behållare kraschar växlar den över till en nod som redan har en kopia av dina data. Ett exempel finns i [Så här distribuerar du en app med tillförlitlig volym](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md)för serviceinfrastruktur .

### <a name="azure-files-volume"></a>Volym för Azure-filer

Azure Files Volume är en Docker-volymdrivrutin som används för att montera en Azure-filresurs till en behållare. Azure Files storage använder nätverkslagring, så läsningar och skrivningar sker över nätverket. Jämfört med Service Fabric Reliable Volume är Azure Files storage mindre högpresterande men ger ett billigare och helt tillförlitligt dataalternativ. Ett exempel finns i [Så här distribuerar du en app med Azure Files Volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Nästa steg

Information om programmodellen finns i [Service Fabric-resurser](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
