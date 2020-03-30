---
title: Hantera tillstånd i Azure Service Fabric-tjänster
description: Lär dig mer om tillståndet i Azure Service Fabric, inklusive hur du definierar och hanterar tjänsttillstånd i Service Fabric-tjänster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614578"
---
# <a name="service-state"></a>Tjänsttillstånd
**Tjänsttillstånd** avser i minnet eller på diskdata som en tjänst behöver för att fungera. Den innehåller till exempel datastrukturer och medlemsvariabler som tjänsten läser och skriver för att arbeta. Beroende på hur tjänsten är utformad kan den även innehålla filer eller andra resurser som lagras på disken. De filer som en databas till exempel använder för att lagra data och transaktionsloggar.

Som ett exempel tjänst, låt oss överväga en miniräknare. En grundläggande kalkylator tjänst tar två nummer och returnerar deras summa. Att utföra den här beräkningen innebär inga medlemsvariabler eller annan information.

Nu överväga samma kalkylator, men med en ytterligare metod för att lagra och returnera den sista summan den har beräknat. Denna tjänst är nu tillståndskänslig. Tillståndskänslig betyder att den innehåller ett tillstånd som den skriver till när den beräknar en ny summa och läser från när du ber den att returnera den senaste beräknade summan.

I Azure Service Fabric kallas den första tjänsten för en tillståndslös tjänst. Den andra tjänsten kallas en tillståndskänslig tjänst.

## <a name="storing-service-state"></a>Lagra tjänsttillstånd
Tillstånd kan antingen externaliseras eller samlokaliseras med koden som manipulerar tillståndet. Externalisering av tillstånd görs vanligtvis med hjälp av en extern databas eller annat datalager som körs på olika datorer över nätverket eller utanför processen på samma dator. I vårt kalkylatorexempel kan datalagret vara en SQL-databas eller instans av Azure Table Store. Varje begäran om att beräkna summan utför en uppdatering på dessa data och begär att tjänsten ska returnera värdet resulterar i att det aktuella värdet hämtas från arkivet. 

Tillstånd kan också samlokalas med koden som manipulerar tillståndet. Tillståndskänsliga tjänster i Service Fabric skapas vanligtvis med den här modellen. Service Fabric tillhandahåller infrastrukturen för att säkerställa att det här tillståndet är mycket tillgängligt, konsekvent och hållbart och att de tjänster som byggs på detta sätt enkelt kan skalas.

## <a name="next-steps"></a>Nästa steg
Mer information om service fabric-begrepp finns i följande artiklar:

* [Tillgång till Service Fabric-tjänster](service-fabric-availability-services.md)
* [Skalbarhet för Service Fabric-tjänster](service-fabric-concepts-scalability.md)
* [Tjänster för partitionering av tjänst fabric](service-fabric-concepts-partitioning.md)
* [Pålitliga tjänster för serviceinfrastruktur](service-fabric-reliable-services-introduction.md)
