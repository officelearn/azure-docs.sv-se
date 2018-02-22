---
title: Uppgradering av Service Fabric programmet | Microsoft Docs
description: "Den här artikeln innehåller en introduktion till uppgraderar ett Service Fabric-program, inklusive att välja uppgradera lägen och utför hälsokontroller."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/13/2018
ms.author: subramar
ms.openlocfilehash: cdad0617c59fd5881c3857388809fac2186b36d8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="service-fabric-application-upgrade"></a>Uppgradera Service Fabric-programmet
Ett program med Azure Service Fabric är en samling tjänster. Under en uppgradering, Service Fabric jämför de nya [programmanifestet](service-fabric-application-and-service-manifests.md) med den tidigare versionen och avgör vilka tjänster i kräver programuppdateringar. Service Fabric Jämför version siffror i tjänsten visar med versionsnummer i den tidigare versionen. Om en tjänst inte har ändrats, uppgraderas att tjänsten inte.

## <a name="rolling-upgrades-overview"></a>Rullande uppgraderingar översikt
I en uppgradering av programmet utförs uppgraderingen i etapper. Uppgraderingen tillämpas på en delmängd av noderna i klustret, kallas en uppdateringsdomän i varje steg. Därför programmet finns kvar under uppgraderingen. Klustret kan innehålla en blandning av de gamla och nya versionerna under uppgraderingen.

Därför måste de två versionerna vara framåt och bakåt kompatibel. Om de inte är kompatibla ansvarar programadministratören för mellanlagring av en uppgradering av flera fasen för att upprätthålla tillgänglighet. Det första steget uppgraderas till en mellanliggande version av programmet som är kompatibel med den tidigare versionen i en flera-fas i uppgraderingen. Det andra steget är att uppgradera den slutliga versionen som bryter kompatibilitet med före uppdateringen version, men som är kompatibel med den mellanliggande versionen.

Uppdatera domäner har angetts i klustermanifestet när du konfigurerar klustret. Uppdatera domäner får inte uppdateringar i en viss ordning. En uppdateringsdomän är en logisk enhet för distribution för ett program. Uppdatera domäner kan tjänsterna som är kvar på hög tillgänglighet under en uppgradering.

Icke-rullande uppgraderingar kan utföras om uppgraderingen tillämpas på alla noder i klustret, vilket är fallet när programmet har bara en uppdateringsdomän. Den här metoden rekommenderas inte eftersom tjänsten kraschar och inte är tillgängligt vid tidpunkten för uppgraderingen. Dessutom tillhandahåller inte Azure garantier när ett kluster har konfigurerats med endast en uppdateringsdomän.

När uppgraderingen är klar, alla tjänster och replicas(instances) skulle vara i samma version-d.v.s. om uppgraderingen lyckas, kommer de att uppdateras till den nya versionen; Om uppgraderingen misslyckas och återställas, de skulle återställas till den gamla versionen.

## <a name="health-checks-during-upgrades"></a>Hälsokontroller under uppgradering
Hälsoprinciper måste anges för en uppgradering (eller standardvärdena kan användas för). En uppgradering benämns lyckas när alla update domäner uppgraderas inom de angivna timeout och när alla update domäner bedöms felfritt.  En felfri uppdateringsdomän innebär att domänen uppdatering skickas alla hälsokontroller som anges i hälsoprincipen. Till exempel en hälsoprincip kan behovet av att alla tjänster i en programinstans måste vara *felfri*, som health definieras av Service Fabric.

Hälsoprinciper och kontrollerar under uppgradering av Service Fabric är oberoende av tjänster och program. Det vill säga är inga tjänstspecifika tester klar.  Till exempel din tjänst kan ha ett genomflöde krav, men Service Fabric har inte informationen för att kontrollera genomflöde. Referera till den [hälsa artiklar](service-fabric-health-introduction.md) för de kontroller som utförs. Kontrollerar att inträffar under en uppgradering inkludera tester för om programpaketet har kopierats korrekt om instansen har startats och så vidare.

Hälsotillståndet för programmet är en sammanställning av underordnade entiteter av programmet. Kort sagt, utvärderar Service Fabric hälsotillståndet för programmet via hälsotillståndet som rapporteras till programmet. Det utvärderar även hälsotillståndet för alla tjänster för programmet det här sättet. Service Fabric ytterligare utvärderar hälsotillstånd programtjänsterna genom insamling av hälsotillståndet för deras underordnade, till exempel tjänsten repliken. När programmets hälsoprincip är uppfyllt, kan fortsätta med uppgraderingen. Om hälsoprincipen överskrids, misslyckas uppgraderingen av applikationen.

## <a name="upgrade-modes"></a>Uppgradera lägen
Det läge som vi rekommenderar för uppgradering av programmet är övervakat läge, som är vanliga läge. Övervakat läge utför uppgraderingen på en uppdateringsdomän och om hälsotillstånd kontrollerar pass (per principen anges), flyttar till nästa uppdatering domänen automatiskt.  Om hälsokontroller misslyckas och/eller timeout har uppnåtts, uppgraderingen återställs antingen för update-domän eller läget ändras till oövervakade manuell. Du kan konfigurera uppgraderingen för att välja ett av dessa två lägen för misslyckad uppgradering. 

Oövervakade manuellt läge måste manuell åtgärd efter varje uppgradering på en update-domän, startar uppgraderingen på domänen nästa uppdatering. Ingen Service Fabric-hälsokontroller utförs. Administratören utför hälsa och status kontroller innan du startar uppgraderingen i domänen nästa uppdatering.

## <a name="upgrade-default-services"></a>Uppgradera standardtjänster
Vissa standardparametrar för tjänsten som definierats i den [programmanifestet](service-fabric-application-and-service-manifests.md) kan också uppgraderas som en del av en uppgradering av programmet. Endast tjänstparametrar som stöder ändras via [uppdatering ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) kan ändras som en del av en uppgradering. Beteende för justerbara standardtjänster under programuppdateringen är följande:

1. Standardtjänster i nya applikationsmanifestet som inte redan finns i klustret skapas.
2. Standardtjänster som finns i båda tidigare och nya applikationsmanifest uppdateras. Parametrarna för standardtjänsten för nya programmanifestet över parametrarna för den befintliga tjänsten. Programmet uppgraderingen kommer återställningen automatiskt om det inte går att uppdatera en standardtjänst.
3. Standardtjänster som inte finns i nya applikationsmanifestet tas bort om de finns i klustret. **Observera att om du tar bort en standardtjänst resulterar i att ta bort allt som tjänsten datorns tillstånd och går inte att ångra.**

När en uppgradering av programmet återställs, återställs standardparametrar för tjänsten tillbaka till sina gamla värden innan uppgraderingen startas men borttagna services kan inte återskapas med det tidigare tillståndet.

> [!TIP]
> Den [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) Konfigurationsinställningen för klustret måste vara *SANT* att aktivera regler 2) och 3) ovan (standard service uppdatering och borttagning). Den här funktionen stöds från och med Service Fabric version 5.5.

## <a name="application-upgrade-flowchart"></a>Uppgradera flödesschema för programmet
Flödesschemat följande stycke kan hjälpa dig att förstå uppgraderingen av ett Service Fabric-program. I synnerhet flödet beskrivs hur timeout, inklusive *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, och *UpgradeHealthCheckInterval*, hjälper dig att kontrollera när uppgraderingen i en uppdateringsdomän betraktas som en lyckats eller misslyckats.

![Uppgraderingsprocessen för ett Service Fabric-program][image]

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med hjälp av Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet ska uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibla genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Lösa vanliga problem i programuppgraderingar genom att referera till stegen i [felsökning programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
