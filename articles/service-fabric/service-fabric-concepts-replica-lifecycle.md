---
title: Repliker och instanser i Azure Service Fabric | Microsoft Docs
description: "Förstå replikeringar och instanser--deras funktion och applivscykler"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.openlocfilehash: b4a01752cf2658bcc8dea663462336ca5c610d70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="replicas-and-instances"></a>Repliker och instanser 
Den här artikeln ger en översikt över livscykeln för repliker av tillståndskänsliga tjänster och instanser av tillståndslösa tjänster.

## <a name="instances-of-stateless-services"></a>Instanser av tillståndslösa tjänster
En instans av en tillståndslös service är en kopia av tjänstelogiken som körs på en av noderna i klustret. En instans i en partition identifieras unikt med dess **InstanceId**. Livscykeln för en instans modelleras i följande diagram:

![Instansen livscykel](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
När klustret Resource Manager avgör en placering för instansen, anger denna status. Instansen har startats på noden. Programvärdnamnet har startats, instansen som sedan öppnas. När startade slutar övergår instansen till tillståndet redo. 

Om programvärd eller nod för den här instansen krascher, övergår det borttagna tillstånd.

### <a name="ready-rd"></a>Klar (RD)
Instansen är i tillståndet ready igång på noden. Om den här instansen är en tillförlitlig tjänst **RunAsync** har anropats. 

Om programvärd eller nod för den här instansen krascher, övergår det borttagna tillstånd.

### <a name="closing-cl"></a>Avslutande (CL)
Azure Service Fabric är i tillståndet avslutande håller på att avslutas instansen på den här noden. Avslutet kan bero på flera orsaker, till exempel en uppgradering av programmet, belastningsutjämning eller tjänsten tas bort. När avstängning har slutförts går över till läget ignorerade.

### <a name="dropped-dd"></a>Avbrutna (DD)
I läget ignorerade körs inte längre instansen på noden. Service Fabric upprätthåller nu metadata om den här instansen, som är slutligen också bort.

> [!NOTE]
> Det är möjligt att övergå från någon status till släppt tillstånd med hjälp av den **ForceRemove** alternativ på `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Repliker av tillståndskänsliga tjänster
En replik av en tillståndskänslig service är en kopia av tjänstelogiken som körs på en av noderna i klustret. Dessutom upprätthåller repliken en kopia av tillståndet för tjänsten. Två relaterade begrepp beskrivs livscykel och beteendet för tillståndskänsliga repliker:
- Replik livscykel
- Replikroll

Följande information beskriver beständiga tillståndskänsliga tjänster. För temporär (eller i minnet) tillståndskänsliga tjänster är nedåt och ignorerade tillstånd likvärdiga.

![Replik livscykel](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
En InBuild-replik är en replik som har skapats eller förberedd för att ansluta till replikuppsättningen. Beroende på replikroll innehåller i IB olika semantik. 

Om programvärden eller nod för en InBuild replik krascher, går över till läget ner.

   - **Primär InBuild-repliker**: primära InBuild är de första replikerna för en partition. Replikeringen sker vanligtvis när partitionen skapas. Primära InBuild-repliker uppstår också när alla repliker för en partition startar om eller har släppts.

   - **IdleSecondary InBuild-repliker**: dessa är antingen nya repliker som skapas av klustret Resource Manager eller befintliga repliker som gått ned och måste läggas tillbaka till uppsättningen. De här replikeringarna dirigeras eller skapat av primärt innan de kan ansluta till replikuppsättningen som ActiveSecondary och delta i kvorum bekräftelse av åtgärder.

   - **ActiveSecondary InBuild-repliker**: det här tillståndet observeras i några frågor. Det är en optimering där replikuppsättningen ändras inte, men en replik måste skapas. Repliken själva följer de normala tillståndsövergångar för datorn (enligt beskrivningen i avsnittet på replik roller).

### <a name="ready-rd"></a>Klar (RD)
En klar replik är en replik som deltar i replikeringen och kvorum bekräftelse av åtgärder. I tillståndet ready gäller primära och aktiva sekundära repliker.

Om programvärden eller nod för en klar replik krascher, går över till läget ner.

### <a name="closing-cl"></a>Avslutande (CL)
En replik övergår tillståndet avslutande i följande scenarier:

- **Stänger av koden för repliken**: Service Fabric kan behöva stänga av köra kod för en replik. Den här avstängning kanske av flera orsaker. Det kan till exempel inträffa på grund av ett program, fabric eller infrastrukturuppgradering eller på grund av ett fel som rapporterats av repliken. När repliken stänger har avslutats, övergår repliken till läget ner. Det beständiga tillståndet som är associerade med den här repliken som lagras på disk rensas inte.

- **Ta bort replikeringen från klustret**: Service Fabric kan behöva ta bort det beständiga tillståndet och Stäng köra kod för en replik. Den här avstängning kanske av flera orsaker, till exempel belastningsutjämning.

### <a name="dropped-dd"></a>Avbrutna (DD)
I läget ignorerade körs inte längre instansen på noden. Det finns inget tillstånd lämnas på noden. Service Fabric upprätthåller nu metadata om den här instansen, som är slutligen också bort.

### <a name="down-d"></a>Ned (D)
Replik koden körs inte i läget ner, men det beständiga tillståndet för den repliken finns på noden. En replik kan ligga nere många skäl--exempelvis noden håller ned en krasch i koden replik, en uppgradering av programmet eller replik-fel.

En inaktiv replik har öppnats av Service Fabric som krävs, till exempel när uppgraderingen har slutförts på noden.

Rollen repliken är inte relevant i läget ner.

### <a name="opening-op"></a>Öppna (OP)
En inaktiv replik försätts i tillståndet öppnas när Service Fabric behöver hämtas repliken säkerhetskopiera igen. Det här tillståndet kanske till exempel efter en uppgradering av koden för programmet har slutförts på en nod. 

Om programvärden eller nod för en inledande replikering krascher, går över till läget ner.

Rollen repliken är inte relevant i tillståndet öppnas.

### <a name="standby-sb"></a>Vänteläge (SB)
En StandBy-replik är en replik av en beständig tjänst som avslutades och sedan har öppnats. Den här repliken kanske används av Service Fabric om behöver lägga till en annan replik i repliken (eftersom repliken redan har en del av status och build-processen är snabbare). När StandByReplicaKeepDuration upphör att gälla tas vänteläge repliken bort.

Om programvärden eller nod för en vänteläge replik krascher, går över till läget ner.

Rollen repliken är inte relevant i tillståndet vänteläge.

> [!NOTE]
> Alla repliker som inte är nere eller släppts anses vara *in*.
>

> [!NOTE]
> Det är möjligt att övergå från någon status till släppt tillstånd med hjälp av den **ForceRemove** alternativ på `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Replikroll 
Rollen för repliken bestämmer dess funktion i replikuppsättningen:

- **Primär (P)**: det finns en primär repliker som ansvarar för att utföra läsningar och skrivningar. 
- **ActiveSecondary (S)**: dessa är repliker som får tillståndet uppdateringar från primärt, använda dem och skickar sedan tillbaka bekräftelser. Det finns flera aktiva sekundära repliker. Antalet dessa active sekundärservrar anger antalet fel som kan hantera tjänsten.
- **IdleSecondary (I)**: de här replikeringarna skapas av den primära servern. De får tillstånd från den primära servern innan de kan befordras till aktiva sekundära. 
- **Ingen (N)**: de här replikeringarna inte har ett ansvar i uppsättningen.
- **Okänd (U)**: det är den första rollen för en replik innan den tar emot någon **ChangeRole** API-anrop från Service Fabric.

Följande diagram illustrerar replik rollen övergångar och vissa scenarier där de kan inträffa:

![Replikroll](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U->-P: skapa en ny primär replik.
- U -> I: skapa en ny inaktiv replik.
- U -> N: borttagning av en replik som vänteläge.
- S: befordran av inaktiv sekundär till aktiva sekundära -> så att dess bekräftelser bidrar till kvorum.
- Jag->-P: befordran av inaktiv sekundär till primär. Detta kan inträffa under särskilda reconfigurations när inaktiv sekundär är rätt kandidat vara primär.
- Jag -> N: borttagning av inaktiv sekundär replik.
- S->-P: befordran av den aktiva sekundärt primär. Detta kan bero på redundans för primärt eller en primär flyttning som initieras av klustret Resource Manager. Det kan vara som svar på en uppgradering av programmet eller belastningsutjämning.
- S -> N: borttagning av den aktiva sekundära repliken.
- P -> S: degraderingen av den primära repliken. Detta kan bero på en primär transport som initieras av klustret Resource Manager. Det kan vara som svar på en uppgradering av programmet eller belastningsutjämning.
- P -> N: borttagning av den primära repliken.

> [!NOTE]
> Överordnad programmering modeller som [Reliable Actors](service-fabric-reliable-actors-introduction.md) och [Reliable Services](service-fabric-reliable-services-introduction.md), dölja begreppet replik roller från utvecklaren. I aktörer är om en roll onödiga. I tjänster förenklas det i hög grad för de flesta scenarier.
>

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artikel:

[Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)

