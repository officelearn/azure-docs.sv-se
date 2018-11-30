---
title: Introduktion till Service Fabric Cluster Resource Manager | Microsoft Docs
description: En introduktion till Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 319bae3025741d6a3130c92d876ae38fcbcdf11e
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333946"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduktion till Service Fabric cluster resource manager
Traditionellt hanterar IT-system eller onlinetjänster avsedda tilldela specifika fysiska eller virtuella datorer till de specifika tjänster eller system. Tjänster har byggts som nivåer. Det skulle vara en ”web” och en ”data” eller ”storage” nivå. Program behöver messaging-nivån där förfrågningar federationssida in och ut, samt en uppsättning datorer som är dedikerad till cachelagring. Varje nivå eller typ av arbetsbelastning hade specifika datorer som är dedikerad till den: databasen har några datorer som är dedikerad till den, webbservrar ett fåtal. Om en viss typ av arbetsbelastning orsakade de datorer som den var på att köra för varmt och sedan du har lagt till fler datorer med samma konfigurationen till den nivån. Men inte alla arbetsbelastningar kan skaländras ut så enkelt – särskilt med datanivå skulle du normalt ersätta datorer med större datorer. Enkelt. Om en dator inte kördes som en del av det övergripande programmet vid lägre kapacitet tills datorn kunde återställas. Fortfarande ganska enkelt (om det är inte nödvändigtvis roliga).

Nu men en värld av arkitekturen för tjänsten och har ändrats. Det är vanligare att program har valt en design för skalbarhet. Det är vanligt att bygga program med behållare eller mikrotjänster (eller båda). Nu när du kanske endast några få datorer, körs de inte bara en enda instans av en arbetsbelastning. De kan även köra flera olika arbetsbelastningar på samma gång. Nu har du flera olika typer av tjänster (ingen förbrukar resurser tillhandahåller en fullständig dator) kanske hundratals olika instanser av dessa tjänster. Varje namngiven instans har en eller flera instanser eller repliker för hög tillgänglighet (HA). Beroende på storleken på dessa arbetsbelastningar och hur upptagen som de är, kan du hitta själv med hundratals eller tusentals datorer. 

Plötsligt hantera miljön är inte så enkelt som att hantera några datorer som är dedikerad för enkla typer av arbetsbelastningar. Dina servrar är virtuella och inte längre har namn (du har växlat mindsets från [husdjur till boskap](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) när alla). Konfigurationen är mindre på datorerna och tjänster som sig själva. Maskinvara som är dedikerad till en enda instans av en arbetsbelastning är i stort sett en sak i tjänsten tidigare. Tjänsterna har blivit små distribuerade system som sträcker sig över flera mindre delar av vanlig maskinvara.

Eftersom din app inte längre är en serie monoliter bryts fördelade på flera nivåer, nu har du många flera kombinationer för att hantera. Som avgör vilka typer av arbetsbelastningar kan köras på vilken maskinvara, eller hur många? Vilka arbetsbelastningar fungera på samma maskinvara och som står i konflikt? När en dator går ned gör vet du vad det var körs på den datorn? Vem är ansvarig för att se till att arbetsbelastningar börjar köras igen? Vänta för den (virtuella?) datorn ska komma tillbaka eller dina arbetsbelastningar automatiskt växla över till andra datorer och hålla kör? Är mänsklig inblandning krävs? Vad gäller uppgraderingar i den här miljön?

Som utvecklare och operatörer som hanterar i den här miljön, ska vi vill ha hjälp med att hantera den här komplexiteten. En anställningsstrategi binge och försök att dölja komplexiteten med personer är förmodligen inte rätt svar, så vad vi gör?

## <a name="introducing-orchestrators"></a>Introduktion till initierare
En ”Orchestrator” är den allmänna termen för en del av programvaran som hjälper administratörer att hantera dessa typer av miljöer. Initierare är komponenterna som utför i begäranden som ”jag vill ha fem kopior av den här tjänsten som körs i Min miljö”. De försöker gör miljön som matchar det önskade tillståndet, oavsett vad som händer.

Initierare (inte mänsklig) är vad vidta åtgärder när en dator misslyckas eller en arbetsbelastning avslutas oväntat önskemål. De flesta initierare mer än att bara hantera fel. Andra funktioner som de har hanterar nya distributioner, hantering av uppgraderingar och ta itu med resursförbrukning och styrning. Alla initierare är mycket om hur du underhåller vissa önskat tillstånd för konfiguration i miljön. Du vill att kunna se en initierare vad du vill ha och den göra grovjobbet. Aurora ovanpå Mesos, Docker Datacenter/Docker Swarm, Kubernetes och Service Fabric är alla exempel på initierare. Dessa initierare utvecklas aktivt för att uppfylla behoven hos riktiga arbetsbelastningar i produktionsmiljöer. 

## <a name="orchestration-as-a-service"></a>Orchestration som en tjänst
Klusterresurshanteraren är systemkomponenter som hanterar orkestrering i Service Fabric. Cluster Resource Manager-jobb är uppdelad i tre delar:

1. Tillämpa regler
2. Optimera din miljö
3. Hjälper till med andra processer

Titta på följande Microsoft Virtual Academy-video om du vill se hur Cluster Resource Manager fungerar: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Vad den stöds inte
I traditionella program för N-nivå, finns alltid en [belastningsutjämnaren](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Detta var vanligtvis en utjämning belastningsutjämnare (NLB) eller ett program Load Balancer (ALB) beroende på var den sat i nätverksstacken. Vissa belastningsutjämnare är maskinvarubaserad som F5: s BigIP erbjudande, andra är baserade på programvara, till exempel Microsoft användarens Utjämning av nätverksbelastning. I andra miljöer, kan du se något som HAProxy, nginx, Istio eller Envoy i den här rollen. I dessa arkitekturer är jobbet för Utjämning av nätverksbelastning att säkerställa att tillståndslösa arbetsbelastningar (ungefär) får samma mängd arbete. Strategier för att balansera läsa in olika. Vissa belastningsutjämnare skickar varje olika anrop till en annan server. Andra tillhandahålls fästa/sessionsvaraktighet. Mer avancerade belastningsutjämnare används faktiska belastningen uppskattning eller rapporterar för att skicka ett samtal baserat på dess förväntade kostnad och den aktuella datorn belastningen.

Belastningsutjämnare för nätverk eller meddelande routrar försökt att se till att web/worker-nivå som finns kvar ungefär belastningsutjämnade. Strategier för att balansera datanivån har olika och beroende på Lagringsmekanismen data. Belastningsutjämning datanivå förlitade sig tidigare på horisontell Datapartitionering, cachelagring, hanterade vyer, lagrade procedurer och andra store-specifika metoder.

Vissa av dessa strategier är intressant, är Service Fabric Cluster Resource Manager inte något som Utjämning av nätverksbelastning eller ett cacheminne. Utjämning av nätverksbelastning balanserar klienter genom att sprida trafiken mellan klienter. Service Fabric Cluster Resource Manager har en egen strategi. Grunden, Service Fabric flyttar *services* där de göra passar bäst, förväntas trafik eller läsa in om du vill följa. Det kan till exempel flytta tjänster till noder som är för närvarande kalla eftersom de tjänster som finns det inte gör mycket arbete. Noderna kan vara kalla eftersom de tjänster som fanns har tagits bort eller flyttats någon annanstans. Ett annat exempel är kan Cluster Resource Manager också flytta en tjänst från en dator. Kanske datorn håller på att uppgraderas, eller är överbelastad på grund av en topp i förbrukning av de tjänster som körs på den. Alernatively, tjänstens resurskrav kan ha ökat. Därför det inte finns tillräckligt med resurser på den här datorn fortsätter att använda den. 

Eftersom Cluster Resource Manager är ansvarig för att flytta tjänster runt, innehåller en uppsättning med olika funktioner jämfört med vad du hittar i Utjämning av nätverksbelastning. Det beror på att belastningsutjämnare för nätverk leverera nätverkstrafik till var services redan är, även om platsen inte är idealiskt för att köra själva tjänsten. Service Fabric Cluster Resource Manager använder helt olika strategier för att säkerställa att resurserna i klustret används effektivt.

## <a name="next-steps"></a>Nästa steg
- Kolla in information om arkitektur- och informationsflödet i Cluster Resource Manager [den här artikeln ](service-fabric-cluster-resource-manager-architecture.md)
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om mätvärden, Kolla in den här artikeln på [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Mått är hur Service Fabric-kluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem Kolla in [i den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager fungerar med funktioner för hantering av Service Fabric. Om du vill veta mer om att integration, läsa [i den här artikeln](service-fabric-cluster-resource-manager-management-integration.md)
- Om du vill veta mer om hur Cluster Resource Manager hanterar och balanserar belastningen i klustret kan du läsa artikeln på [belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md)
