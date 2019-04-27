---
title: Service Fabric-Programuppgradering | Microsoft Docs
description: Den här artikeln ger en introduktion till uppgraderingen av ett Service Fabric-program, inklusive välja uppgradera lägen och utföra hälsokontroller.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e2b407733bcab7bc854e8e3703e53eb474f3425b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615076"
---
# <a name="service-fabric-application-upgrade"></a>Uppgradera Service Fabric-programmet
Ett Azure Service Fabric-program är en samling tjänster. Vid en uppgradering Service Fabric jämför den nya [programmanifestet](service-fabric-application-and-service-manifests.md) med den tidigare versionen och avgör vilka tjänster som programmet kräver uppdateringar. Service Fabric Jämför version siffror i service manifest med versionsnummer i den tidigare versionen. Om en tjänst inte har ändrats, uppgraderas inte tjänsten.

## <a name="rolling-upgrades-overview"></a>Löpande uppgraderingar översikt
I en löpande Programuppgradering utförs uppgraderingen i faser. Uppgraderingen används i varje steg på en del av noderna i klustret, kallas en uppdateringsdomän. Därför kan fortfarande programmet tillgängligt i hela uppgraderingen. Under uppgraderingen kan klustret innehåller en blandning av de gamla och nya versionerna.

Därför finns de två versionerna måste vara framåt och bakåt kompatibel. Om de inte är kompatibla ansvarar programadministratören för att mellanlagra en uppgradering av flera fas för att upprätthålla tillgänglighet. I en flera fas-uppgradering det första steget uppgradera till en mellanliggande version av programmet som är kompatibel med den tidigare versionen. Det andra steget är att uppgradera den slutliga versionen som skadar kompatibilitet med före uppdateringen version, men är kompatibel med den mellanliggande versionen.

Uppdateringsdomäner har angetts i klustermanifestet när du konfigurerar klustret. Uppdateringsdomäner får inte uppdateringar i en viss ordning. En uppdateringsdomän är en logisk enhet för distribution för ett program. Uppdateringsdomäner kan tjänsterna som är kvar på hög tillgänglighet under en uppgradering.

Icke-rullande uppgraderingar är möjliga om uppgraderingen tillämpas på alla noder i klustret, vilket är fallet när programmet har endast en uppdateringsdomän. Den här metoden rekommenderas inte eftersom tjänsten kraschar och inte är tillgänglig vid tidpunkten för uppgraderingen. Dessutom ger inte Azure inga garantier när ett kluster har konfigurerats med endast en uppdateringsdomän.

När uppgraderingen är klar, alla tjänster och replicas(instances) skulle stanna kvar i samma version – t.ex, om uppgraderingen lyckades visas kommer de att uppdateras till den nya versionen; Om uppgraderingen misslyckas och återställas, de skulle återställas till den gamla versionen.

## <a name="health-checks-during-upgrades"></a>Hälsokontroller under uppgraderingar
Hälsoprinciper måste anges för en uppgradering (eller standardvärden kan användas). Lyckad kallas för en uppgradering när alla uppdateringsdomäner uppgraderas inom de angivna timeout och när alla uppdateringsdomäner bedöms som felfri.  En felfri uppdateringsdomän innebär att uppdateringsdomänen skickas alla hälsokontroller som anges i principen hälsotillstånd. Till exempel en hälsoprincip kan Kräv att alla tjänster inom en programinstans måste vara *felfri*, som health definieras av Service Fabric.

Hälsoprinciper och kontroller vid uppgradering av Service Fabric är oberoende av tjänsten och program. Dvs, utförs inga tjänstspecifika tester.  Till exempel din tjänst kan ha ett dataflöde krav, men Service Fabric har inte informationen för att kontrollera dataflöde. Referera till den [hälsotillstånd artiklar](service-fabric-health-introduction.md) för de kontroller som utförs. Kontrollerar att inträffa under en uppgradering inkludera tester för om programpaketet har kopierats korrekt, oavsett om instansen har startats och så vidare.

Programmets hälsotillstånd är en sammansättning av underordnade entiteter av programmet. Kort sagt, utvärderar Service Fabric hälsotillståndet för programmet med hjälp av hälsotillstånd som har rapporterats för programmet. Det utvärderar även hälsotillståndet för alla tjänster för programmet på så sätt. Service Fabric ytterligare utvärderar hälsotillståndet för application services genom att sammanställa hälsotillståndet för deras underordnade objekt, till exempel tjänsterepliken. När programmets hälsoprincip är uppfyllt, fortsätta med uppgraderingen. Uppgradera programmet misslyckas om hälsoprincipen har överskridits.

## <a name="upgrade-modes"></a>Uppgradera lägen
Det läge som vi rekommenderar för uppgradering av programmet är övervakat läge, som är vanliga läge. Övervakade läge utför uppgraderingen på en uppdateringsdomän och om alla hälsokontroller pass (per principen anges), går till nästa uppdateringsdomän automatiskt.  Om hälsokontroller misslyckas och/eller tidsgränser uppnås, uppgraderingen återställs antingen för update-domän eller har ändrats till oövervakade manuell. Du kan konfigurera uppgraderingen för att välja någon av dessa två lägen för misslyckade uppgraderingar. 

Oövervakade manuellt läge måste manuella åtgärder efter varje uppgradering på en uppdateringsdomän inledningsanförande uppgraderingen på nästa uppdateringsdomän. Inga Service Fabric-hälsokontroller utförs. Administratören utför status eller hälsotillstånd kontrollerna innan du startar uppgraderingen i nästa uppdateringsdomän.

## <a name="upgrade-default-services"></a>Uppgradera standardtjänster
Vissa parametrar för standard-tjänsten som definierats i den [programmanifestet](service-fabric-application-and-service-manifests.md) kan också uppgraderas som en del av en uppgradering av programmet. Endast tjänstparametrar som stöder ändras via [uppdatering ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) kan ändras som en del av en uppgradering. Beteendet för att ändra standardtjänster under uppgradering av programmet är följande:

1. Standardtjänster i nya applikationsmanifestet som inte redan finns i klustret skapas.
2. Standardtjänster som finns i båda tidigare och nya programmanifesten uppdateras. Parametrarna för standardtjänsten i nya applikationsmanifestet över parametrarna för den befintliga tjänsten. Uppgradera programmet kommer återställning automatiskt om det inte går att uppdatera en standardtjänst.
3. Standardtjänster som inte finns i nya applikationsmanifestet tas bort om de finns i klustret. **Observera att ta bort en standardtjänst resulterar i att ta bort allt som tjänsten användarens tillstånd och kan inte ångras.**

När en uppgradering av programmet återställs, återställs standard tjänstparametrar tillbaka till sina gamla värden innan uppgraderingen igång men har tagits bort tjänster kan inte skapas på nytt med sina tidigare tillstånd.

> [!TIP]
> Ju [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) konfigurationsinställning klustret måste vara *SANT* att aktivera regler 2) och 3) ovan (standard tjänstuppdateringen och borttagning). Den här funktionen stöds från och med Service Fabric version 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Uppgradera flera program med HTTPS-slutpunkter
Du måste vara noga med att inte använda den **samma port** för olika instanser av samma program när du använder HTTP**S**. Anledningen är att Service Fabric inte kommer kunna uppgradera certifikat för någon av programinstanserna. Om exempelvis programmet båda 1 eller ett program 2 vill uppgradera sina certifikat 1 till cert 2. När uppgraderingen sker, kan Service Fabric har rensat certifikat 1 registreringen med http.sys även om det andra programmet fortfarande använder den. Om du vill förhindra detta identifierar Service Fabric som det finns redan en annan programinstans som är registrerade på porten med certifikat (på grund av http.sys) och misslyckas åtgärden.

Därför Service Fabric stöder inte uppgradering två olika tjänster med hjälp av **samma port** i olika programinstanser. Med andra ord kan du använda samma certifikat på olika tjänster på samma port. Om du måste ha ett delade certifikat på samma port, måste du se till att tjänsterna är placerade på olika datorer med placeringsbegränsningar. Eller Överväg att använda dynamiska portar för Service Fabric om möjligt för varje tjänst i varje programinstans. 

Om du ser en uppgradering misslyckas med https, ett fel, varning om att ”Windows HTTP-Server API inte stöder flera certifikat för program som delar en port”.

## <a name="application-upgrade-flowchart"></a>Flödesschema för uppgradering för programmet
Flödesschemat efter detta stycke kan hjälpa dig att förstå uppgraderingen av ett Service Fabric-program. I synnerhet flödet beskriver hur timeout, inklusive *HealthCheckStableDuration*, *HealthCheckRetryTimeout*, och *UpgradeHealthCheckInterval*, hjälp kontroll när uppgraderingen i en uppdateringsdomän anses vara en lyckats eller misslyckats.

![Uppgraderingen till Service Fabric-program][image]

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med Visual Studio.

[Uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibel genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [felsöka programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
