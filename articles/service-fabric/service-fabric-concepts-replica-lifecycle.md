---
title: Repliker och instanser i Azure Service Fabric
description: Lär dig mer om repliker och instanser i Service Fabric, inklusive en översikt över deras livscykler och funktioner.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258569"
---
# <a name="replicas-and-instances"></a>Repliker och instanser 
Den här artikeln ger en översikt över livscykeln för repliker av tillståndskänsliga tjänster och instanser av tillståndslösa tjänster.

## <a name="instances-of-stateless-services"></a>Instanser av statslösa tjänster
En instans av en tillståndslös tjänst är en kopia av tjänstlogiken som körs på en av noderna i klustret. En instans i en partition identifieras unikt av dess **InstanceId**. Livscykeln för en instans modelleras i följande diagram:

![Instanslivscykel](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
När Klusterresurshanteraren har fastställt en placering för instansen anges den i det här livscykeltillståndet. Instansen startas på noden. Programvärden startas, instansen skapas och öppnas sedan. När starten är klar övergår instansen till det färdiga tillståndet. 

Om programvärden eller noden för den här instansen kraschar övergår den till det tappade tillståndet.

### <a name="ready-rd"></a>Klar (RD)
I redo tillstånd är instansen igång på noden. Om den här instansen är en tillförlitlig tjänst har **RunAsync** anropats. 

Om programvärden eller noden för den här instansen kraschar övergår den till det tappade tillståndet.

### <a name="closing-cl"></a>Stängning (CL)
I stängningstillståndet håller Azure Service Fabric på att stänga av instansen på den här noden. Avstängningen kan bero på många orsaker , till exempel en programuppgradering, belastningsutjämning eller tjänsten som tas bort. När avstängningen är klar övergår den till det tappade tillståndet.

### <a name="dropped-dd"></a>Tappade (DD)
I det bortsläppade tillståndet körs instansen inte längre på noden. Vid denna punkt, Service Fabric underhåller metadata om den här instansen, som så småningom tas bort också.

> [!NOTE]
> Det är möjligt att övergå från alla tillstånd till det `Remove-ServiceFabricReplica`tappade tillståndet med alternativet **ForceRemove** på .
>

## <a name="replicas-of-stateful-services"></a>Kopior av tillståndskänsliga tjänster
En replik av en tillståndskänslig tjänst är en kopia av tjänstlogiken som körs på en av noderna i klustret. Dessutom behåller repliken en kopia av tjänstens tillstånd. Två relaterade begrepp beskriver livscykeln och beteendet hos tillståndskänsliga repliker:
- Repliklivscykeln
- Replikroll

Följande diskussion beskriver beständiga tillståndskänsliga tjänster. För flyktiga (eller i minnet) tillståndskänsliga tjänster är ned- och tappade tillstånd likvärdiga.

![Repliklivscykeln](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
En InBuild-replik är en replik som har skapats eller förberetts för att ansluta replikuppsättningen. Beroende på replikrollen har IB olika semantik. 

Om programvärden eller noden för en InBuild-replik kraschar övergår den till nedläget.

   - **Primära InBuild-repliker:** Primär InBuild är de första replikerna för en partition. Den här repliken händer vanligtvis när partitionen skapas. Primära InBuild-repliker uppstår också när alla repliker av en partition startas om eller tas bort.

   - **IdleSecondary InBuild-repliker**: Det här är antingen nya repliker som skapas av Cluster Resource Manager eller befintliga repliker som gick ned och måste läggas tillbaka till uppsättningen. Dessa repliker dirigeras eller skapas av den primära innan de kan ansluta till replikuppsättningen som ActiveSecondary och delta i kvorumbekräftelse av åtgärder.

   - **ActiveSecondary InBuild-repliker**: Det här tillståndet observeras i vissa frågor. Det är en optimering där replikuppsättningen inte ändras, men en replik måste byggas. Själva repliken följer normala tillståndsdatorövergångar (enligt beskrivningen i avsnittet om replikroller).

### <a name="ready-rd"></a>Klar (RD)
En ready-replik är en replik som deltar i replikering och kvorumbekräftelse av åtgärder. Det färdiga tillståndet gäller för primära och aktiva sekundära repliker.

Om programvärden eller noden för en färdig replik kraschar övergår den till nedläget.

### <a name="closing-cl"></a>Stängning (CL)
En replik går in i stängningstillståndet i följande scenarier:

- **Stänga av koden för repliken**: Service Fabric kan behöva stänga av koden som körs för en replik. Avstängningen kan vara av många skäl. Det kan till exempel inträffa på grund av ett program, en infrastruktur eller en infrastrukturuppgradering, eller på grund av ett fel som rapporterats av repliken. När replikstängningen är klar övergår repliken till nedläget. Det beständiga tillståndet som är associerat med den här repliken som lagras på disken rensas inte.

- **Ta bort repliken från klustret**: Service Fabric kan behöva ta bort det kvarstående tillståndet och stänga av koden som körs för en replik. Avstängningen kan vara av många skäl, till exempel belastningsutjämning.

### <a name="dropped-dd"></a>Tappade (DD)
I det bortsläppade tillståndet körs instansen inte längre på noden. Det finns inte heller något tillstånd kvar på noden. Vid denna punkt, Service Fabric underhåller metadata om den här instansen, som så småningom tas bort också.

### <a name="down-d"></a>Nedåt (D)
I ned-läget körs inte replikkoden, men det kvarstående tillståndet för repliken finns på noden. En replik kan vara nere av många skäl - till exempel noden är nere, en krasch i replikkoden, en programuppgradering eller replikfel.

En nedåtreplik öppnas av Service Fabric efter behov, till exempel när uppgraderingen är klar på noden.

Replikrollen är inte relevant i ned-läget.

### <a name="opening-op"></a>Öppning (OP)
En nedåtgående replik går in i öppningstillståndet när Service Fabric behöver föra repliken tillbaka igen. Det här tillståndet kan till exempel vara när en koduppgradering för programmet har slutförts på en nod. 

Om programvärden eller noden för en inledande replik kraschar övergår den till nedläget.

Replikrollen är inte relevant i öppningstillståndet.

### <a name="standby-sb"></a>StandBy (SB)
En StandBy-replik är en kopia av en beständig tjänst som gick ner och sedan öppnades. Den här repliken kan användas av Service Fabric om den behöver lägga till ytterligare en replik i replikuppsättningen (eftersom repliken redan har en del av tillståndet och byggprocessen är snabbare). När StandByReplicaKeepDuration har gått ut ignoreras reservrepliken.

Om programvärden eller noden för en reservreplik kraschar övergår den till nedläget.

Replikrollen är inte relevant i vänteläge.

> [!NOTE]
> Varje replik som inte är nere eller tappas anses vara *upp*.
>

> [!NOTE]
> Det är möjligt att övergå från alla tillstånd till det tappade `Remove-ServiceFabricReplica`tillståndet med alternativet **ForceRemove** på .
>

## <a name="replica-role"></a>Replikroll 
Replikens roll avgör dess funktion i replikuppsättningen:

- **Primär (P)**: Det finns en primär i replikuppsättningen som ansvarar för att utföra läs- och skrivåtgärder. 
- **ActiveSecondary (S):** Det här är repliker som tar emot tillståndsuppdateringar från den primära, tillämpar dem och skickar sedan tillbaka bekräftelser. Det finns flera aktiva sekundärer i replikuppsättningen. Antalet aktiva sekundärer bestämmer antalet fel som tjänsten kan hantera.
- **IdleSecondary (I)**: Dessa repliker byggs av den primära. De tar emot tillstånd från den primära innan de kan befordras till aktiv sekundär. 
- **Ingen (N)**: Dessa repliker har inget ansvar i replikuppsättningen.
- **Okänd (U)**: Detta är den första rollen för en replik innan den tar emot något **ChangeRole** API-anrop från Service Fabric.

Följande diagram illustrerar övergångarna för replikrollen och några exempelscenarier där de kan inträffa:

![Replikroll](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Skapande av en ny primär replik.
- U -> I: Skapande av en ny inaktiv replik.
- U -> N: Borttagning av en reservreplik.
- I -> S: Främjande av den sysslolös sekundärt till aktiv sekundär så att dess erkännanden bidrar till kvorum.
- I -> P: Främjande av tomgång sekundärt till primära. Detta kan inträffa under särskilda omkonfigurationer när den inaktiva sekundära är rätt kandidat att vara primär.
- I -> N: Borttagning av den inaktiva sekundära repliken.
- S -> P: Främjande av den aktiva sekundärt till primär. Detta kan bero på redundans av den primära eller en primär rörelse som initierats av Cluster Resource Manager. Det kan till exempel vara ett svar på en programuppgradering eller belastningsutjämning.
- S -> N: Borttagning av den aktiva sekundära repliken.
- P -> S: Degradering av den primära repliken. Detta kan bero på en primär förflyttning som initierats av Cluster Resource Manager. Det kan till exempel vara ett svar på en programuppgradering eller belastningsutjämning.
- P -> N: Borttagning av den primära repliken.

> [!NOTE]
> Programmeringsmodeller på högre nivå, till exempel [Reliable Actors](service-fabric-reliable-actors-introduction.md) och [Reliable Services,](service-fabric-reliable-services-introduction.md)döljer begreppet replikroller från utvecklaren. I Skådespelare är begreppet roll onödig. I Tjänster är det till stor del förenklat för de flesta scenarier.
>

## <a name="next-steps"></a>Nästa steg
Mer information om service fabric-begrepp finns i följande artikel:

[Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)

