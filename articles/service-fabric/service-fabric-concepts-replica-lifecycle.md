---
title: Repliker och instanser i Azure Service Fabric | Microsoft Docs
description: Förstå repliker och instanser--deras funktion och applivscykler
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 7f8638365b40395a5dd82457c40e5c15209ba1a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882426"
---
# <a name="replicas-and-instances"></a>Repliker och instanser 
Den här artikeln ger en översikt över livscykeln för repliker av tillståndskänsliga tjänster och instanser av tillståndslösa tjänster.

## <a name="instances-of-stateless-services"></a>Instanser av tillståndslösa tjänster
En instans av en tillståndslös tjänst är en kopia av den logik som tjänsten som körs på en av noderna i klustret. En instans inom en partition identifieras unikt genom dess **InstanceId**. Livscykeln för en instans modelleras i följande diagram:

![Instans-livscykel](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
När Cluster Resource Manager anger en placering för instansen, anger det här livscykeltillståndet. Instansen har startats på noden. Programvärden startas, instansen skapas och sedan öppnas. När startminnet är klar övergår instansen till tillståndet klar. 

Om programvärd eller nod för den här instansen kraschar, övergår den till tillståndet avbrutna.

### <a name="ready-rd"></a>Klart (RD)
Statusen klar är instansen igång och körs på noden. Om den här instansen är en tillförlitlig tjänst **RunAsync** har anropats. 

Om programvärd eller nod för den här instansen kraschar, övergår den till tillståndet avbrutna.

### <a name="closing-cl"></a>Avslutande (CL)
Tillståndet avslutande är Azure Service Fabric håller på att avslutas instansen på den här noden. Denna avstängning kan bero på flera orsaker, till exempel en uppgradering av programmet, belastningsutjämning eller tjänsten tas bort. När det är klar att Stäng av, övergår den till tillståndet avbrutna.

### <a name="dropped-dd"></a>Utelämnade (DD)
Tillståndet släppta instansen inte längre som körs på noden. Service Fabric har nu metadata om den här instansen, som så småningom är också bort.

> [!NOTE]
> Det är möjligt att övergå från alla tillstånd till utelämnade tillstånd med hjälp av den **ForceRemove** alternativet på `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Repliker av tillståndskänsliga tjänster
En replik av en tillståndskänslig tjänst är en kopia av tjänstelogiken som körs på en av noderna i klustret. Repliken har dessutom en kopia av tillståndet för den tjänsten. Två relaterade begrepp beskrivs livscykel och beteendet för tillståndskänsliga repliker:
- Livscykel för replik
- Replikeringsroll

Följande information beskriver beständiga tillståndskänsliga tjänster. För föränderliga (eller i minnet) tillståndskänsliga tjänster är nedåt och utelämnade tillstånden likvärdiga.

![Livscykel för replik](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
En InBuild-replik är en replik som har skapats eller förberedd för att ansluta till replikuppsättningen. Beroende på replikroll har i IB olika semantik. 

Om programvärden eller nod för en InBuild-replik kraschar, övergår det till ned-läge.

   - **Primär InBuild-repliker**: Primär InBuild är de första replikerna för en partition. Den här repliken sker vanligtvis när partitionen skapas. Primär InBuild-repliker uppstår också när alla repliker för en partition startar om eller är släppts.

   - **IdleSecondary InBuild-repliker**: Här är antingen nya repliker som skapas av Cluster Resource Manager eller befintliga replikeringar som skickades av och behöver läggas tillbaka i uppsättningen. De här replikeringarna dirigeras eller skapats av primärt innan de kan ansluta till replikuppsättningen som ActiveSecondary och delta i kvorum bekräftelse av åtgärder.

   - **ActiveSecondary InBuild-repliker**: Det här tillståndet observeras i några frågor. Det är en optimering där replikuppsättning inte ändras, men en replik måste skapas. Repliken själva följer de normala tillståndsövergångar för datorn (som beskrivs i avsnittet på replik-roller).

### <a name="ready-rd"></a>Klart (RD)
En klar replik är en replik som deltar i replikeringen och kvorum bekräftelse av åtgärder. Färdigt tillstånd kan användas för primär och aktiva sekundära repliker.

Om programvärden eller nod för en klar replik kraschar, övergår det till ned-läge.

### <a name="closing-cl"></a>Avslutande (CL)
En replik får avslutande status i följande scenarier:

- **Stänger av koden för repliken**: Service Fabric kan behöva stänga köra kod för en replik. Denna avstängning kanske av flera orsaker. Exempelvis kan det inträffa på grund av ett program, infrastruktur eller infrastrukturuppgradering eller på grund av ett fel som rapporterats av repliken. När repliken stänger har slutförts, övergår replikeringen till ned-läge. Det beständiga tillståndet som är associerade med den här repliken som lagras på disk har inte rensats.

- **Ta bort repliken från klustret**: Service Fabric kan behöva ta bort det beständiga tillståndet och stänga av köra kod för en replik. Denna avstängning kanske av flera orsaker, till exempel belastningsutjämning.

### <a name="dropped-dd"></a>Utelämnade (DD)
Tillståndet släppta instansen inte längre som körs på noden. Det finns inga tillstånd på noden. Service Fabric har nu metadata om den här instansen, som så småningom är också bort.

### <a name="down-d"></a>Ned (D)
Repliken koden körs inte i ned-läge, men det beständiga tillståndet för den repliken finns på noden. En replik kan vara otillgängliga av flera orsaker, till exempel noden håller ned, en krasch i replik-kod eller en uppgradering av programmet repliken fel.

En inaktiv replik öppnas av Service Fabric som krävs, till exempel när uppgraderingen har slutförts på noden.

Rollen repliken är inte relevant i ned-läge.

### <a name="opening-op"></a>Öppna (OP)
En inaktiv replik försätts i tillståndet öppnas när Service Fabric behöver tar repliken säkerhetskopiera igen. Det här tillståndet kanske till exempel efter en uppgradering av koden för programmet har slutförts på en nod. 

Om programvärden eller nod för en inledande replik kraschar, övergår det till ned-läge.

Rollen repliken är inte relevant i inledande tillstånd.

### <a name="standby-sb"></a>Vänteläge (SB)
En StandBy-replik är en replik av en bestående tjänst som har gått och sedan har öppnats. Den här repliken kanske används av Service Fabric om det behöver lägga till en annan replik till repliken (eftersom repliken redan har en del av tillståndet och även utvecklingsprocessen är snabbare). StandByReplicaKeepDuration förnyas och tas vänteläge repliken bort.

Om programvärden eller nod för en standby replik kraschar, övergår det till ned-läge.

Rollen repliken är inte relevant i tillståndet vänteläge.

> [!NOTE]
> Alla repliker som inte är nere eller släppts anses vara *upp*.
>

> [!NOTE]
> Det är möjligt att övergå från alla tillstånd till utelämnade tillstånd med hjälp av den **ForceRemove** alternativet på `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Replikeringsroll 
Rollen för repliken avgör dess funktion i replikuppsättningen:

- **Primära (P)**: Det finns en primär i uppsättningen som ansvarar för att utföra läsningar och skrivningar. 
- **ActiveSecondary (S)**: Det här är repliker som tar emot tillstånd uppdateringar från primärt, tillämpa dem och sedan skicka tillbaka bekräftelser. Det finns flera aktiva sekundära databaser i uppsättningen. Antalet dessa active sekundärservrar anger antalet fel som kan hantera tjänsten.
- **IdleSecondary (I)**: De här replikeringarna skapas av primärt. De får tillstånd från primärt innan de kan höjas upp till aktiv sekundär. 
- **Ingen (N)**: De här replikeringarna har inte ett ansvar i uppsättningen.
- **Okänd (U)**: Det är den första rollen av en replik som innan den tar emot eventuella **ChangeRole** API-anrop från Service Fabric.

Följande diagram illustrerar repliken rollen övergångar och vissa scenarier där de inträffar:

![Replikeringsroll](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Skapa en ny primär replik.
- U -> I: Skapa en ny inaktiv replik.
- U -> N: Borttagning av en replik som vänteläge.
- I -> S: Befordran av den inaktiv sekundär till aktiva sekundära så att dess bekräftelser bidrar till kvorum.
- I -> P: Befordran av inaktiv sekundär till primär. Detta kan inträffa under särskilda reconfigurations när inaktiv sekundär är rätt kandidat är primär.
- I -> N: Borttagning av inaktiv sekundär replik.
- S -> P: Befordran av den aktiva sekundärt till den primära servern. Detta kan bero på redundans för primärt eller en primär flyttning som initieras av Cluster Resource Manager. Det kan till exempel vara som svar på en uppgradering av programmet eller belastningsutjämning.
- S -> N: Borttagning av den aktiva sekundära repliken.
- P -> S: Degraderingen av den primära repliken. Detta kan bero på en primär transport som initieras av Cluster Resource Manager. Det kan till exempel vara som svar på en uppgradering av programmet eller belastningsutjämning.
- P -> N: Borttagning av den primära repliken.

> [!NOTE]
> På högre nivå programmering modeller, till exempel [Reliable Actors](service-fabric-reliable-actors-introduction.md) och [Reliable Services](service-fabric-reliable-services-introduction.md), dölja begreppet repliken roller från utvecklaren. I Actors är begreppet en roll inte nödvändigt. I tjänster, är det hög grad förenklad för de flesta scenarier.
>

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artikel:

[Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)

