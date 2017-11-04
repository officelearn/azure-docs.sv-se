---
title: Introduktion till Service Fabric klustret Resource Manager | Microsoft Docs
description: En introduktion till Service Fabric klustret Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3e8cd4dc8e960e38ba0e4a9a195b2f61d9ec1924
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduktion till resurshanteraren för Service Fabric-kluster
Traditionellt hantera IT-system eller onlinetjänster avsedda för dessa specifika tjänster eller system trafikklass särskilda fysiska eller virtuella datorer. Tjänster har konstruerad som nivåer. Det vore en nivå med ”web” och ”data” eller ”lagring”-nivån. Program skulle ha en meddelanden nivå där förfrågningar gått in och ut, samt en uppsättning datorer som är dedikerad till cachelagring. Varje nivå eller en typ av arbetsbelastning hade specifika datorer som är dedikerad till den: databasen får några datorer som är dedikerad till den, webbservrar ett fåtal. Om en viss typ av arbetsbelastning orsakade datorer som du ville på Kör för hot och du har lagt till flera datorer med samma konfigurationen nivån. Dock inte alla arbetsbelastningar kan inte skalas ut så enkelt - särskilt med datanivå du vanligtvis ersätta datorer med större datorer. Enkelt. Om en dator inte kördes som en del av det övergripande programmet på lägre kapacitet tills datorn kunde återställas. Fortfarande ganska enkelt (om det är inte nödvändigtvis roligt).

Nu men världen av arkitekturen i tjänsten och programvaran har ändrats. Nu är det numera vanligast att program har antagit en skalbar design. Det är vanligt att bygga program med behållare eller mikrotjänster (eller båda). Nu när du kanske endast några datorer, kör de inte en enda instans av en arbetsbelastning. De kan även köra flera olika arbetsbelastningar på samma gång. Nu har du mängder av olika typer av tjänster (ingen förbrukar en fullständig dator kan du se resurser) kanske hundratals olika instanser av tjänsterna. Varje namngiven instans har en eller flera instanser repliker för hög tillgänglighet. Beroende på storleken på dessa arbetsbelastningar och hur upptagen som de är, kan du själv med hundratals eller tusentals datorer. 

Plötsligt hantera din miljö är inte så enkelt som att hantera några datorer som är dedikerad för enkla typer av arbetsbelastningar. Dina servrar är virtuell och inte längre har namn (du har växlat mindsets från [husdjur till nötkreatur](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) när alla). Konfigurationen är mindre om datorerna och tjänster som sig själva. Maskinvara som är dedikerad till en enda instans av en arbetsbelastning är i stort sett en sak för den tidigare. Tjänsterna har blivit små distribuerade system som sträcker sig över flera mindre delar av vanlig hårdvara.

Eftersom din app inte längre är en serie monoliths fördelade på flera nivåer, nu har du många flera kombinationer för att hantera. Som bestämmer vilka typer av arbetsbelastningar kan köras på vilken maskinvara, eller hur många? Vilka arbetsbelastningar fungerar på samma maskinvara och som står i konflikt? Om en dator går ned gör du vet vad det har körts på den datorn? Vem är ansvarig för att se till att arbetsbelastningar börjar köras igen? Vänta för att gå tillbaka den (virtuella)? datorn eller dina arbetsbelastningar automatiskt växla över till andra datorer och Behåll kör? Är mänsklig inblandning krävs? Nyheter om uppgraderingar i den här miljön?

Som utvecklare och operatorer som rör i den här miljön, ska du vill ha hjälp att hantera den här komplexitet. Anställningsstrategin binge och försöker döljer komplexiteten med personer är förmodligen inte det är fel, så vad vi gör?

## <a name="introducing-orchestrators"></a>Introduktion till orchestrators
”Orchestrator” är den allmänna termen för programvara som hjälper administratörer att hantera dessa typer av miljöer. Orchestrators är komponenterna som utför i begäranden som ”jag vill fem kopior av den här tjänsten som körs i Min miljö”. De försöker gör miljön som matchar tillståndet, oavsett vad som händer.

Orchestrators (inte människor) är vad vidta åtgärder när en dator misslyckas eller en arbetsbelastning avslutas oväntat önskemål. De flesta orchestrators mer än bara hantera fel. Andra funktioner som de har hanterar nya distributioner, hanterar uppgraderingar och hantera resursförbrukning och styrning. Alla orchestrators är mycket om hur du underhåller vissa tillstånd för konfiguration i miljön. Du vill se en orchestrator vad du vill och har den göra frekventa lyft. Aurora ovanpå Mesos Docker Datacenter/Docker Swarm, Kubernetes och Service Fabric är alla exempel på orchestrators. Dessa orchestrators utvecklas aktivt för att möta behoven hos verkliga arbetsbelastningar i produktionsmiljöer. 

## <a name="orchestration-as-a-service"></a>Orchestration som en tjänst
Klustret Resource Manager är den komponent som hanterar orchestration i Service Fabric. Klustret Resource Manager-jobb är uppdelad i tre delar:

1. Tillämpa regler
2. Optimera din miljö
3. Hjälper till med andra processer

Titta på följande Microsoft Virtual Academy videoklipp om du vill se hur klustret Resource Manager fungerar:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Det finns inte
I traditionella N nivåer program finns alltid en [belastningsutjämnaren](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Vanligtvis har en belastningsutjämnare (Utjämning av nätverksbelastning) eller ett program belastningen belastningsutjämnare (ALB) beroende på var den lör i nätverksstacken. Vissa belastningsutjämnare är maskinvarubaserad som F5 BigIP erbjudande, andra är baserade på programvara, till exempel Microsoft har NLB. I andra miljöer, kan du se något som HAProxy, nginx, Istio eller Envoy i den här rollen. I dessa arkitekturer är jobbet för belastningsutjämning att säkerställa att tillståndslösa arbetsbelastningar (ungefär) får samma mängd arbete. Strategier för att balansera läsa in olika. Vissa belastningsutjämnare skulle skicka varje olika anrop till en annan server. Andra angetts fästning session/varaktighet. Mer avancerade belastningsutjämnare används faktiska belastningen uppskattning eller rapportering för att skicka ett samtal baserat på dess förväntade kostnaden och den aktuella belastningen på datorn.

Belastningsutjämning för nätverk eller meddelandet routrar försökte Kontrollera att nivån web/worker legat ungefär belastningsutjämnade. Strategier för att balansera datanivå har olika och beroende på mekanismen för lagring av data. NLB datanivå förlita sig på data horisontell partitionering cachelagring hanterade vyer, lagrade procedurer och andra store-specifika metoder.

Vissa av dessa strategier är intressant, är Service Fabric klustret Resource Manager inte något som en Utjämning av nätverksbelastning eller en cache. En Utjämning av nätverksbelastning balanserar frontends genom att sprida trafik över frontends. Resurshanteraren för Service Fabric kluster har en annan strategi. Grunden, Service Fabric flyttar *services* där de mest användbara, förväntas trafik eller läsa in om du vill följa. Det kan till exempel flytta tjänster till noder som är för närvarande cold eftersom de tjänster som är det inte gör mycket arbete. Noder kan vara kalla eftersom de tjänster som fanns har tagits bort eller flyttats någon annanstans. Ett annat exempel är kan kluster Resource Manager också flytta en tjänst från en dator. Kanske datorn håller på att uppgraderas, eller är överbelastad på grund av en topp i förbrukningen av de tjänster som körs på den. Alernatively, tjänstens resurskrav kan har ökat. Därför det inte finns tillräckligt med resurser på den här datorn för att köra den. 

Eftersom klustret Resource Manager ansvarar för att flytta tjänster, innehåller en uppsättning med olika funktioner jämfört med vad du hittar i en Utjämning av nätverksbelastning. Det beror på att nätverksbelastningsutjämnare leverera nätverkstrafik till där tjänster redan är, även om platsen inte är idealiskt för att köra själva tjänsten. Resurshanteraren för Service Fabric klustret använder helt olika strategier för att säkerställa att resurserna i klustret är effektiv.

## <a name="next-steps"></a>Nästa steg
- Information om arkitektur och information om flödet inom klustret Resource Manager kolla [den här artikeln](service-fabric-cluster-resource-manager-architecture.md)
- Klustret Resource Manager har många alternativ för att beskriva klustret. Kolla in den här artikeln om du vill veta mer om mått på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Mått är hur Service Fabric-kluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem checka ut [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
- Klustret Resource Manager fungerar med funktioner för hantering av Service Fabric. Om du vill veta mer om att integration läsa [i den här artikeln](service-fabric-cluster-resource-manager-management-integration.md)
- Om du vill veta mer om hur klustret Resource Manager hanterar och balanserar belastningen i klustret kan ta en titt i artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
