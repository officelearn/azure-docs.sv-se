---
title: Programmodell för Azure Service Fabric
description: Så här modellerar och beskriver du program och tjänster i Azure Service Fabric med hjälp av program- och tjänstmanifestfiler.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7179686b7d4ef2df267cb95ece8f83d5fb7682b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551887"
---
# <a name="model-an-application-in-service-fabric"></a>Modellera ett program i Service Fabric
Den här artikeln innehåller en översikt över programmodellen för Azure Service Fabric och hur du definierar ett program och en tjänst via manifestfiler.

## <a name="understand-the-application-model"></a>Förstå programmodellen
Ett program är en samling ingående tjänster som utför en viss funktion eller funktioner. En tjänst utför en komplett och fristående funktion och kan starta och köra oberoende av andra tjänster.  En tjänst består av kod, konfiguration och data. För varje tjänst består koden av de körbara binärfilerna, konfigurationen består av tjänstinställningar som kan läsas in vid körning och data består av godtyckliga statiska data som ska förbrukas av tjänsten. Varje komponent i den här hierarkiska programmodellen kan versionsutas och uppgraderas oberoende av varandra.

![Programmodellen Service Fabric][appmodel-diagram]

En programtyp är en kategorisering av ett program och består av ett paket av tjänsttyper. En tjänsttyp är en kategorisering av en tjänst. Kategoriseringen kan ha olika inställningar och konfigurationer, men kärnfunktionerna förblir desamma. Instanserna av en tjänst är de olika tjänstkonfigurationsvariationerna av samma tjänsttyp.  

Klasser (eller "typer") av program och tjänster beskrivs via XML-filer (programmanifest och tjänstmanifest).  Manifesten beskriver program och tjänster och är de mallar mot vilka program kan instansieras från klustrets bildlagringslager.  Manifest behandlas i detalj i [Program- och tjänstmanifest](service-fabric-application-and-service-manifests.md). Schemadefinitionen för filen ServiceManifest.xml och ApplicationManifest.xml installeras med Service Fabric SDK och verktyg till *C:\Program Files\Microsoft SDK\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. XML-schemat dokumenteras i [ServiceFabricServiceModel.xsd-schemadokumentationen](service-fabric-service-model-schema.md).

Koden för olika programinstanser körs som separata processer även när den finns i samma Service Fabric-nod. Dessutom kan livscykeln för varje programinstans hanteras (till exempel uppgraderas) oberoende av varandra. Följande diagram visar hur programtyper består av tjänsttyper, som i sin tur består av kod-, konfigurations- och datapaket. För att förenkla diagrammet visas endast kod-/config/datapaket för, `ServiceType4` även om varje tjänsttyp skulle innehålla vissa eller alla dessa pakettyper.

![Programtyper och tjänsttyper för Service Fabric][cluster-imagestore-apptypes]

Det kan finnas en eller flera instanser av en tjänsttyp som är aktiv i klustret. Tillståndskänsliga tjänstinstanser, eller repliker, uppnår till exempel hög tillförlitlighet genom att replikera tillstånd mellan repliker som finns på olika noder i klustret. Replikering ger i huvudsak redundans för att tjänsten ska vara tillgänglig även om en nod i ett kluster misslyckas. En [partitionerad tjänst](service-fabric-concepts-partitioning.md) delar ytterligare sitt tillstånd (och åtkomstmönster till det tillståndet) över noder i klustret.

I följande diagram visas förhållandet mellan program och tjänstinstanser, partitioner och repliker.

![Partitioner och repliker inom en tjänst][cluster-application-instances]

> [!TIP]
> Du kan visa layouten för program i ett kluster med&lt;verktyget Service Fabric&gt;Explorer som finns på http:// yourclusteraddress :19080/Explorer. Mer information finns i [Visualisera klustret med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Nästa steg
- Läs mer om [programskalbarhet](service-fabric-concepts-scalability.md).
- Lär dig mer om [tjänsttillstånd,](service-fabric-concepts-state.md) [partitionering](service-fabric-concepts-partitioning.md)och [tillgänglighet](service-fabric-availability-services.md).
- Läs om hur program och tjänster definieras i [Program- och tjänstmanifest](service-fabric-application-and-service-manifests.md).
- [Programvärdmodeller](service-fabric-hosting-model.md) beskriver förhållandet mellan repliker (eller instanser) för en distribuerad tjänst och tjänstvärdprocess.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


