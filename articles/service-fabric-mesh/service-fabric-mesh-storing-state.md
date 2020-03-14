---
title: Alternativ för tillstånds lagring på Azure Service Fabric-nät
description: Läs om tillförlitligt lagrings tillstånd i Service Fabric nätprogram som körs på Azure Service Fabric-nätet.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259102"
---
# <a name="state-management-with-service-fabric"></a>Tillstånds hantering med Service Fabric

Service Fabric stöder många olika alternativ för tillstånds lagring. En konceptuell översikt över mönstren för tillstånds hantering och Service Fabric finns i [Service Fabric begrepp: tillstånd](/azure/service-fabric/service-fabric-concepts-state). Alla dessa koncept gäller oavsett om tjänsterna körs i eller utanför Service Fabric nät. 

Med Service Fabric nät kan du enkelt distribuera ett nytt program och ansluta det till ett befintligt data lager som finns i Azure. Förutom att använda en fjärran sluten databas finns det flera alternativ för att lagra data, beroende på om tjänsten är lokal eller Fjärrlagring. 

## <a name="volumes"></a>Volymer

Behållare använder ofta temporära diskar. Temporära diskar är tillfälliga, så du får en ny temporär disk och förlorar informationen när en behållare kraschar. Det är också svårt att dela information på temporära diskar med andra behållare. Volymer är kataloger som monteras i dina behållar instanser som du kan använda för att spara tillstånd. -Volymer ger dig generell fil lagring och gör att du kan läsa och skriva filer med hjälp av normal disk-I/O-fil-API: er. Volym resursen beskriver hur du monterar en katalog och vilken lagrings plats som ska användas. Du kan välja antingen Azure File Storage eller Service Fabric volym disk för att lagra data.

![Volymer][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric tillförlitlig volym

Service Fabric tillförlitlig volym är en Docker-volym driv rutin som används för att montera en lokal volym till en behållare. Läsningar och skrivningar är lokala åtgärder och snabbt. Data replikeras ut till sekundära noder, vilket gör den hög tillgänglig. Redundansväxlingen är också snabbt. När en behållare kraschar växlar den över till en nod som redan har en kopia av dina data. Ett exempel finns i [så här distribuerar du en app med Service Fabric tillförlitlig volym](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Azure Files volym

Azure Files volym är en Docker-volym driv rutin som används för att montera en Azure Files resurs till en behållare. Azure Files lagring använder nätverks lagring, så läsningar och skrivningar sker över nätverket. Jämfört med Service Fabric tillförlitlig volym är Azure Files Storage mindre gjort men ger ett billigare och fullständigt tillförlitligt data alternativ. Ett exempel finns i [så här distribuerar du en app med Azure Files volym](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Nästa steg

Information om program modellen finns [Service Fabric resurser](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
