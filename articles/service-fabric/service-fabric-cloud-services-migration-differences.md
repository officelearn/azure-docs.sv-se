---
title: Skillnader mellan molntjänster och serviceinfrastruktur
description: En begreppsmässig översikt för att migrera program från Molntjänster till Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 283ad2c63bb59771dab7881522e737f773ab1705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463381"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Lär dig mer om skillnaderna mellan molntjänster och service fabric innan du migrerar program.
Microsoft Azure Service Fabric är nästa generations molnprogramplattform för mycket skalbara och mycket tillförlitliga distribuerade program. Det introducerar många nya funktioner för paketering, distribution, uppgradering och hantering av distribuerade molnprogram. 

Det här är en introduktionsguide för att migrera program från Molntjänster till Service Fabric. Det fokuserar främst på arkitektoniska och designskillnader mellan molntjänster och serviceinfrastruktur.

## <a name="applications-and-infrastructure"></a>Tillämpningar och infrastruktur
En grundläggande skillnad mellan Molntjänster och Service Fabric är förhållandet mellan virtuella datorer, arbetsbelastningar och program. En arbetsbelastning här definieras som den kod du skriver för att utföra en viss uppgift eller tillhandahålla en tjänst.

* **Cloud Services handlar om att distribuera program som virtuella datorer.** Koden du skriver är tätt kopplad till en VM-instans, till exempel en webb- eller arbetarroll. Att distribuera en arbetsbelastning i Molntjänster är att distribuera en eller flera VM-instanser som kör arbetsbelastningen. Det finns ingen separation av program och virtuella datorer, så det finns ingen formell definition av ett program. Ett program kan ses som en uppsättning webb- eller arbetarrollsinstanser i en Cloud Services-distribution eller som en hel Cloud Services-distribution. I det här exemplet visas ett program som en uppsättning rollinstanser.

![Cloud Services-program och topologi][1]

* **Service Fabric handlar om att distribuera program till befintliga virtuella datorer eller datorer som kör Service Fabric på Windows eller Linux.** De tjänster du skriver är helt frikopplade från den underliggande infrastrukturen, som abstraplas bort av programplattformen Service Fabric, så att ett program kan distribueras till flera miljöer. En arbetsbelastning i Service Fabric kallas "tjänst" och en eller flera tjänster grupperas i ett formellt definierat program som körs på programplattformen Service Fabric. Flera program kan distribueras till ett enda Service Fabric-kluster.

![Service Fabric-program och topologi][2]

Service Fabric i sig är ett programplattformslag som körs på Windows eller Linux, medan Cloud Services är ett system för distribution av Azure-hanterade virtuella datorer med anslutna arbetsbelastningar.
Programmodellen Service Fabric har ett antal fördelar:

* Snabba driftsättningstider. Det kan vara tidskrävande att skapa VM-instanser. I Service Fabric distribueras virtuella datorer bara en gång för att skapa ett kluster som är värd för programplattformen Service Fabric. Från och med då kan programpaket distribueras till klustret mycket snabbt.
* Hög densitet hosting. I Molntjänster är en vm-värd för en arbetsroll en arbetsbelastning. I Service Fabric är program separata från de virtuella datorer som kör dem, vilket innebär att du kan distribuera ett stort antal program till ett litet antal virtuella datorer, vilket kan sänka den totala kostnaden för större distributioner.
* Service Fabric-plattformen kan köras var som helst med Windows Server- eller Linux-datorer, oavsett om det är Azure eller lokalt. Plattformen tillhandahåller ett abstraktionslager över den underliggande infrastrukturen så att ditt program kan köras i olika miljöer. 
* Distribuerad programhantering. Service Fabric är en plattform som inte bara är värd för distribuerade program, utan också hjälper till att hantera deras livscykel oberoende av den virtuella datorn eller maskinens livscykel.

## <a name="application-architecture"></a>Programarkitektur
Arkitekturen för ett Cloud Services-program innehåller vanligtvis många externa tjänstberoenden, till exempel Service Bus, Azure Table och Blob Storage, SQL, Redis och andra för att hantera tillståndet och data för ett program och kommunikation mellan webb och kommunikation mellan webb och Arbetarroller i en Molntjänstdistribution. Ett exempel på ett komplett Cloud Services-program kan se ut så här:  

![Molntjänstarkitektur][9]

Service Fabric-program kan också välja att använda samma externa tjänster i ett komplett program. Med det här exemplet Cloud Services-arkitekturen är den enklaste migreringsvägen från Molntjänster till Service Fabric att ersätta endast Cloud Services-distributionen med ett Service Fabric-program, vilket gör den övergripande arkitekturen densamma. Webb- och arbetarrollerna kan portas till service fabric-tillståndslösa tjänster med minimala kodändringar.

![Service Fabric arkitektur efter enkel migrering][10]

I detta skede bör systemet fortsätta att fungera på samma sätt som tidigare. Om du drar nytta av Service Fabrics tillståndskänsliga funktioner kan externa statliga butiker internaliseras som tillståndskänsliga tjänster där så är tillämpligt. Detta är mer involverat än en enkel migrering av webb- och arbetsroller till tjänst fabric-tillståndslösa tjänster, eftersom det kräver att du skriver anpassade tjänster som tillhandahåller motsvarande funktioner för ditt program som de externa tjänsterna gjorde tidigare. Fördelarna med att göra detta är: 

* Ta bort externa beroenden 
* Ena distributions-, hanterings- och uppgraderingsmodellerna. 

Ett exempel på den arkitektur som resulterar i internalisering av dessa tjänster kan se ut så här:

![Service Fabric-arkitektur efter fullständig migrering][11]

## <a name="communication-and-workflow"></a>Kommunikation och arbetsflöde
De flesta Molntjänstprogram består av mer än en nivå. På samma sätt består ett Service Fabric-program av mer än en tjänst (vanligtvis många tjänster). Två vanliga kommunikationsmodeller är direktkommunikation och via en extern hållbar lagring.

### <a name="direct-communication"></a>Direkt kommunikation
Med direkt kommunikation kan nivåer kommunicera direkt via slutpunkt som exponeras av varje nivå. I tillståndslösa miljöer som Molntjänster innebär det att välja en instans av en VM-roll, antingen slumpmässigt eller round-robin för att balansera belastningen och ansluta till slutpunkten direkt.

![Direktkommunikation mellan molntjänster][5]

 Direktkommunikation är en vanlig kommunikationsmodell i Service Fabric. Den viktigaste skillnaden mellan Service Fabric och Cloud Services är att du i Molntjänster ansluter till en virtuell dator, medan du i Service Fabric ansluter till en tjänst. Detta är en viktig skillnad för ett par skäl:

* Tjänster i Service Fabric är inte bundna till de virtuella datorer som är värdar för dem. tjänster kan flytta runt i klustret och i själva verket förväntas flytta runt av olika skäl: Resursbalansering, redundans, program- och infrastrukturuppgraderingar samt placerings- eller belastningsbegränsningar. Det innebär att en tjänstinstans adress kan ändras när som helst. 
* En virtuell dator i Service Fabric kan vara värd för flera tjänster, var och en med unika slutpunkter.

Service Fabric tillhandahåller en tjänstidentifieringsmekanism, kallad namngivningstjänsten, som kan användas för att matcha slutpunktsadresser för tjänster. 

![Direktkommunikation med Service Fabric][6]

### <a name="queues"></a>Köer
En gemensam kommunikationsmekanism mellan nivåer i tillståndslösa miljöer som Molntjänster är att använda en extern lagringskö för att lagra arbetsuppgifter på ett durably från en nivå till en annan. Ett vanligt scenario är en webbnivå som skickar jobb till en Azure Queue eller Service Bus där worker role-instanser kan dequeue och bearbeta jobben.

![Kökommunikation för Molntjänster][7]

Samma kommunikationsmodell kan användas i Service Fabric. Detta kan vara användbart när du migrerar ett befintligt Cloud Services-program till Service Fabric. 

![Direktkommunikation med Service Fabric][8]

## <a name="parity"></a>Paritet
[Cloud Services liknar Service Fabric i grad av kontroll kontra användarvänlighet, men det är nu en äldre tjänst och Service Fabric rekommenderas för ny utveckling;](https://docs.microsoft.com/azure/app-service/overview-compare) Följande är en API-jämförelse:


| **API för molntjänst** | **API för tjänstinfrastruktur** | **Obs!** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId eller . NodNamn | ID är en egenskap för NodeName |
| RollInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtrera på nodnamn och använda FD-egenskap |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtrera på nodenamn och använda egenskapen Uppgradera |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext eller Namngivning (ResolveService) | CodePackageActivationContext som tillhandahålls både av FabricRuntime.GetActivationContext och inom replikerna via ServiceInitializationParameters.CodePackageActivationContext som tillhandahålls under . Initiera |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Om du vill göra samma typ av filtrering efter typ kan du få listan över nodtyper från klustermanifestet via FabricClient.ClusterManager.GetClusterManifest och ta tag i roll-/nodtyperna därifrån. |
| RoleEnvironment.getIsAvailable RollEnvironment.GetIsAvailable RollEnvironment.GetIsAvailable RollEn | Anslut-WindowsFabricCluster eller skapa en FabricRuntime pekade på en viss nod | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Arbete | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Arbete | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Arbete | * |
| Roll.GetInstances | FabricClient.QueryManager.GetNodeList eller ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext eller Namngivning (ResolveService) | * |

## <a name="next-steps"></a>Efterföljande moment
Den enklaste migreringsvägen från Molntjänster till Service Fabric är att endast ersätta Cloud Services-distributionen med ett Service Fabric-program, vilket håller den övergripande arkitekturen för ditt program ungefär densamma. Följande artikel innehåller en guide för att konvertera en webb- eller arbetarroll till en tjänst som är tillståndslös.

* [Enkel migrering: konvertera en webb- eller arbetarroll till en tillståndslös tjänst för service fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
