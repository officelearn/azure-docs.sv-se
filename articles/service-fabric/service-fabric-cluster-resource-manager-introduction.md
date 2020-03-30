---
title: Vi presenterar Resurshanteraren för service fabric-kluster
description: Lär dig mer om Service Fabric Cluster Resource Manager, ett sätt att hantera orkestrering av programmets tjänster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563334"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduktion till resurshanteraren för service fabric-kluster
Traditionellt hantera IT-system eller online-tjänster innebar att ägna specifika fysiska eller virtuella maskiner till dessa specifika tjänster eller system. Tjänster arkitekted som nivåer. Det skulle finnas en "web"-nivå och en "data" eller "lagring" nivå. Program skulle ha en meddelandenivå där begäranden flödade in och ut, samt en uppsättning datorer som är dedikerade till cachelagring. Varje nivå eller typ av arbetsbelastning hade specifika maskiner dedikerade till det: databasen fick ett par maskiner dedikerade till det, webbservrar några. Om en viss typ av arbetsbelastning orsakade att datorerna var på att köra för varmt, har du lagt till fler datorer med samma konfiguration på den nivån. Alla arbetsbelastningar kan dock inte skalas ut så lätt – särskilt inte med den datanivå som du vanligtvis ersätter datorer med större datorer. Enkelt. Om en dator misslyckades kördes den delen av det övergripande programmet med lägre kapacitet tills datorn kunde återställas. Fortfarande ganska lätt (om inte nödvändigtvis kul).

Nu har dock världen av service och mjukvaruarkitektur förändrats. Det är vanligare att program har antagit en skalningsdesign. Det är vanligt att bygga program med behållare eller mikrotjänster (eller båda). Nu, medan du kanske fortfarande har bara ett fåtal datorer, de kör inte bara en enda instans av en arbetsbelastning. De kan till och med köra flera olika arbetsbelastningar samtidigt. Du har nu dussintals olika typer av tjänster (ingen konsumerar en hel maskin värde av resurser), kanske hundratals olika fall av dessa tjänster. Varje namngiven instans har en eller flera instanser eller repliker för hög tillgänglighet (HA). Beroende på storleken på dessa arbetsbelastningar och hur upptagna de är kan du hitta dig själv med hundratals eller tusentals datorer. 

Plötsligt hantera din miljö är inte så enkelt som att hantera några datorer dedikerade till enskilda typer av arbetsbelastningar. Din servarna de/vi/du/ni är verklig och ingen längre har namnen ( du har kopplat tänkesätt från [sällskapsdjur till nötkreaturen](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) trots allt). Konfiguration handlar mindre om datorerna och mer om tjänsterna själva. Maskinvara som är dedikerad till en enda instans av en arbetsbelastning är till stor del ett tillhör det förflutna. Själva tjänsterna har blivit små distribuerade system som spänner över flera mindre delar av varuhårdvara.

Eftersom din app inte längre är en serie monoliter spridda över flera nivåer har du nu många fler kombinationer att hantera. Vem bestämmer vilka typer av arbetsbelastningar som kan köras på vilken maskinvara eller hur många? Vilka arbetsbelastningar fungerar bra på samma maskinvara och vilken konflikt? När en maskin går ner hur vet du vad som kördes där på den maskinen? Vem ansvarar för att se till att arbetsbelastningen börjar köras igen? Väntar du på att (virtuell?) datorn ska komma tillbaka eller misslyckas dina arbetsbelastningar automatiskt med andra datorer och fortsätter att köras? Krävs mänsklig inblandning? Hur är det med uppgraderingar i den här miljön?

Som utvecklare och operatörer som arbetar i den här miljön, vi kommer att vilja ha hjälp att hantera denna komplexitet. En hyra binge och försöker dölja komplexiteten med människor är förmodligen inte rätt svar, så vad gör vi?

## <a name="introducing-orchestrators"></a>Introduktion till orchestrators
En "Orchestrator" är den allmänna termen för en programvara som hjälper administratörer att hantera dessa typer av miljöer. Orchestrators är de komponenter som tar in förfrågningar som "Jag skulle vilja ha fem kopior av den här tjänsten som körs i min miljö." De försöker få miljön att matcha önskat tillstånd, oavsett vad som händer.

Orchestrators (inte människor) är vad vidta åtgärder när en dator misslyckas eller en arbetsbelastning avslutas av någon oväntad anledning. De flesta orchestrators gör mer än att bara ta itu med misslyckande. Andra funktioner som de har är att hantera nya distributioner, hantera uppgraderingar och hantera resursförbrukning och styrning. Alla orchestrators handlar i grunden om att upprätthålla vissa önskade tillstånd av konfiguration i miljön. Du vill kunna berätta för en orchestrator vad du vill och få den att göra tunga lyft. Aurora ovanpå Mesos, Docker Datacenter/Docker Swarm, Kubernetes och Service Fabric är alla exempel på orchestrators. Dessa orchestrators utvecklas aktivt för att möta behoven hos verkliga arbetsbelastningar i produktionsmiljöer. 

## <a name="orchestration-as-a-service"></a>Orkestrering som en tjänst
Klusterresurshanteraren är den systemkomponent som hanterar orkestrering i Service Fabric. Jobbet för klusterresurshanteraren är uppdelat i tre delar:

1. Tvingande regler
2. Optimera din miljö
3. Hjälpa till med andra processer

### <a name="what-it-isnt"></a>Vad det inte är
I traditionella N-nivåprogram finns det alltid en [belastningsutjämnare](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Vanligtvis var detta en NLB (Network Load Balancer) eller en Program load Balancer (ALB) beroende på var den satt i nätverksstapeln. Vissa belastningsutjämnare är maskinvarubaserade som F5:s BigIP-erbjudande, andra är programvarubaserade, till exempel Microsofts NLB. I andra miljöer kan du se något i stil med HAProxy, nginx, Istio eller Sändebud i den här rollen. I dessa arkitekturer är jobbet för belastningsutjämning att säkerställa att tillståndslösa arbetsbelastningar får (ungefär) samma mängd arbete. Strategier för balansering belastning varierade. Vissa balanserare skulle skicka varje annat samtal till en annan server. Andra som session nålning / klibbighet. Mer avancerade balanserare använder faktisk belastningsuppskattning eller rapportering för att dirigera ett samtal baserat på dess förväntade kostnad och aktuella maskinbelastning.

Nätverksbalanser eller meddelanderoutrar försökte se till att webb-/arbetarnivån förblev ungefär balanserad. Strategier för att balansera datanivån var olika och berodde på datalagringsmekanismen. Balansera datanivån förlitade sig på datasharding, cachelagring, hanterade vyer, lagrade procedurer och andra butiksspecifika mekanismer.

Vissa av dessa strategier är intressanta, men Service Fabric Cluster Resource Manager är inte alls som en nätverksbelastningsutjämnare eller en cache. En nätverksbelastningsbalanser balanserar frontends genom att sprida trafik över frontends. Service Fabric Cluster Resource Manager har en annan strategi. I grund och fall flyttar Service Fabric *tjänster* dit de är mest meningsfulla och förväntar sig att trafik eller belastning ska följa efter. Det kan till exempel flytta tjänster till noder som för närvarande är kalla eftersom de tjänster som finns där inte gör mycket arbete. Noderna kan vara kalla eftersom de tjänster som fanns togs bort eller flyttade någon annanstans. Som ett annat exempel kan Klusterresurshanteraren också flytta en tjänst bort från en dator. Kanske maskinen är på väg att uppgraderas, eller är överbelastad på grund av en ökning av förbrukningen av de tjänster som körs på den. Alternativt kan tjänstens resursbehov ha ökat. Därför finns det inte tillräckligt med resurser på den här datorn för att fortsätta köra den. 

Eftersom Cluster Resource Manager ansvarar för att flytta tjänster runt innehåller den en annan funktionsuppsättning jämfört med vad du skulle hitta i en nätverksbelastningsutjämnare. Detta beror på att nätverksbelastningsutjämnare levererar nätverkstrafik dit tjänster redan finns, även om den platsen inte är idealisk för att köra själva tjänsten. Service Fabric Cluster Resource Manager använder fundamentalt olika strategier för att säkerställa att resurserna i klustret används effektivt.

## <a name="next-steps"></a>Nästa steg
- Information om arkitektur och informationsflöde i Cluster Resource Manager finns i den [här artikeln](service-fabric-cluster-resource-manager-architecture.md)
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om mått kan du läsa den här artikeln om [hur du beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Mer information om hur du konfigurerar tjänster [finns i Läs mer om att konfigurera tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Mått är hur Service Fabric Cluster Resource Manger hanterar förbrukning och kapacitet i klustret. Om du vill veta mer om mått och hur du konfigurerar dem, kolla in [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
- Klusterresurshanteraren arbetar med Service Fabric hanteringsfunktioner. Om du vill veta mer om den integrationen läser du [den här artikeln](service-fabric-cluster-resource-manager-management-integration.md)
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastning i klustret kan du läsa artikeln om [utjämningsbelastning](service-fabric-cluster-resource-manager-balancing.md)
