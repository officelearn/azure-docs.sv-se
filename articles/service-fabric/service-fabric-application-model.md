---
title: Azure Service Fabric-programmodellen | Microsoft Docs
description: Så här att modellera och beskriva program och tjänster i Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 750970233cbcb14d901dbb5fa94f649f6ff8ae6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621449"
---
# <a name="model-an-application-in-service-fabric"></a>Modellera ett program i Service Fabric
Den här artikeln innehåller en översikt över Azure Service Fabric-programmodellen och hur du definierar en och en tjänst via manifestfiler.

## <a name="understand-the-application-model"></a>Förstå programmodellen
Ett program är en samling av dessa tjänster som utför en viss funktion eller funktioner. En tjänst kan utför en fullständig och fristående funktion och starta och köra oberoende av andra tjänster.  En tjänst består av kod, konfiguration och data. För varje tjänst, kod består av körbara binärfilerna, konfigurationen består av service-inställningar som kan läsas in vid körning och data består av godtycklig statiska data som ska konsumeras av tjänsten. Varje komponent i den här hierarkisk programmodell kan vara versionsnummer och uppgraderas separat.

![Service Fabric-programmodellen][appmodel-diagram]

Programtyp är en kategorisering av ett program och består av ett paket med tjänsttyper. En tjänst är en kategorisering av en tjänst. Kategorisering kan ha olika inställningar och konfigurationer, men huvudfunktionerna förblir densamma. Instanser av en tjänst finns olika service configuration varianter av samma service-typen.  

Klasser (eller ”typer”) för program och tjänster beskrivs via XML-filer (programmanifesten och tjänstmanifest).  Manifesten Beskriv program och tjänster så är de mallar som program kan skapas från klustrets avbildningslager.  Manifest beskrivs i detalj i [App och tjänstmanifest](service-fabric-application-and-service-manifests.md). Schemadefinitionen för filen ServiceManifest.xml och ApplicationManifest.xml installeras med Service Fabric SDK och verktyg att *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. XML-schema dokumenteras i [ServiceFabricServiceModel.xsd schemat dokumentation](service-fabric-service-model-schema.md).

Koden för olika programinstanser kör som separata processer även när de ligger på samma Service Fabric-noden. Dessutom livscykeln för varje programinstans kan hanteras (t.ex, uppgraderas) oberoende av varandra. Följande diagram visar hur programtyper består av olika typer av tjänster, som i sin tur består av kod, konfiguration och data paket. För att förenkla diagrammet endast kod/config/data paket för `ServiceType4` visas, även om varje typ av tjänst skulle innehålla några eller alla de pakettyper.

![Service Fabric programtyper och tjänsttyper][cluster-imagestore-apptypes]

Det kan finnas en eller flera instanser av en tjänsttyp aktiv i klustret. Till exempel få tillståndskänslig tjänstinstanser eller repliker, hög tillförlitlighet genom att replikera tillstånd mellan repliker som finns på olika noder i klustret. Replikering tillhandahåller i stort sett redundans för att tjänsten är tillgänglig även om en nod i ett kluster misslyckas. En [partitionerad service](service-fabric-concepts-partitioning.md) ytterligare dividerar dess tillstånd (och mönster i databasåtkomst till det aktuella tillståndet) över noder i klustret.

Följande diagram visar relationen mellan program och instanser av tjänsten, partitioner och repliker.

![Partitioner och -repliker i en tjänst][cluster-application-instances]

> [!TIP]
> Du kan visa layouten för program i ett kluster med hjälp av verktyget Service Fabric Explorer på http://&lt;yourclusteraddress&gt;: 19080/Explorer. Mer information finns i [visualisera klustret med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
- Läs om service [tillstånd](service-fabric-concepts-state.md), [partitionering](service-fabric-concepts-partitioning.md), och [tillgänglighet](service-fabric-availability-services.md).
- Läs om hur program och tjänster definieras i [App och tjänstmanifest](service-fabric-application-and-service-manifests.md).
- [Program som är värd för modeller](service-fabric-hosting-model.md) beskriver relationen mellan repliker (eller instanser) för en distribuerad tjänst och värdprocessen för tjänsten.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


