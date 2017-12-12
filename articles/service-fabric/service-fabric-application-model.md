---
title: Azure Service Fabric-programmodell | Microsoft Docs
description: "Så här modell och beskriver program och tjänster i Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: ryanwi
ms.openlocfilehash: d5f6fbb9d9c0bc0d9762f8d6b4b4eb3b02d29adc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="model-an-application-in-service-fabric"></a>Modellen är ett program i Service Fabric
Den här artikeln innehåller en översikt över Azure Service Fabric-programmodell och hur du definierar ett program och tjänsten via manifest-filer.

## <a name="understand-the-application-model"></a>Förstå programmet modellen
Ett program är en samling de tjänster som utför en viss funktion eller funktioner. En utför en fullständig och fristående funktion och kan starta och köra oberoende av andra tjänster.  En tjänst består av koden, konfiguration och data. Koden består av körbara binärfilerna för varje tjänst, konfigurationen består av service-inställningar som kan läsas in vid körning och data består av godtycklig statiska data som ska konsumeras av tjänsten. Varje komponent i den här modellen hierarkiska programmet kan vara en ny version och uppgraderade oberoende av varandra.

![Programmodell Service Fabric][appmodel-diagram]

En typ av program består av ett paket av tjänsttyper är en kategorisering av ett program. En service är en kategorisering av en tjänst. Kategorisering kan ha olika inställningar och konfigurationer, men huvudfunktionerna är densamma. Instanser av en tjänst finns olika service configuration varianter av samma service-typen.  

Klasser (eller ”typer”) för program och tjänster beskrivs via XML-filer (applikationsmanifest och service manifest).  Manifesten beskrivs program och tjänster och mallar som program kan initieras från avbildningsarkivet i klustret.  Manifest beskrivs i detalj i [programmet och service manifest](service-fabric-application-and-service-manifests.md). Schemadefinitionen för filen ServiceManifest.xml och ApplicationManifest.xml har installerats med Service Fabric-SDK och verktyg för *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. XML-schemat dokumenteras i [ServiceFabricServiceModel.xsd schemadokumentationen](service-fabric-service-model-schema.md).

Koden för olika programinstanser köra som separata processer även om de finns på samma Service Fabric-noden. Dessutom livscykeln för varje instans av programmet kan hanteras (till exempel uppgraderas) oberoende av varandra. Följande diagram visar hur programtyper består av tjänsttyper, som i sin tur består av koden, konfiguration och data paket. För att förenkla diagrammet bara kod/config/data paket för `ServiceType4` visas, även om varje typ av tjänst skulle lägga till några eller alla pakettyper.

![Service Fabric programtyper och typer av tjänster][cluster-imagestore-apptypes]

Det kan finnas en eller flera instanser av en typ av aktiva i klustret. Till exempel uppnå tillståndskänslig tjänstinstanser eller repliker hög tillförlitlighet genom att replikera tillstånd mellan repliker som finns på olika noder i klustret. Replikering i stort sett ger redundans för tjänsten ska vara tillgängliga även om en nod i ett kluster misslyckas. En [partitionerad service](service-fabric-concepts-partitioning.md) ytterligare dividerar dess tillstånd (och åtkomstmönster till det aktuella tillståndet) mellan noder i klustret.

Följande diagram visar relationen mellan program och instanser av tjänsten, partitioner och repliker.

![Partitioner och repliker i en tjänst][cluster-application-instances]

> [!TIP]
> Du kan visa layouten för program i ett kluster med verktyget Service Fabric-Utforskaren på http://&lt;yourclusteraddress&gt;: 19080/Explorer. Mer information finns i [visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [programmet skalbarhet](service-fabric-concepts-scalability.md).
- Lär dig mer om tjänsten [tillstånd](service-fabric-concepts-state.md), [partitionering](service-fabric-concepts-partitioning.md), och [tillgänglighet](service-fabric-availability-services.md).
- Läs mer om hur program och tjänster definieras i [programmet och service manifest](service-fabric-application-and-service-manifests.md).
- [Program värd modeller](service-fabric-hosting-model.md) beskriver relationen mellan repliker (eller instanser) av en distribuerad tjänst och värdprocessen för tjänsten.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


