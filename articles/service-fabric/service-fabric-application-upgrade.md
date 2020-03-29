---
title: Uppgradering av Service Fabric-programmet
description: Den här artikeln innehåller en introduktion till uppgradering av ett Service Fabric-program, inklusive att välja uppgraderingslägen och utföra hälsokontroller.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 2dc484b49c5250510e5f018cbbc2da107573d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259050"
---
# <a name="service-fabric-application-upgrade"></a>Uppgradering av Service Fabric-programmet
Ett Azure Service Fabric-program är en samling tjänster. Under en uppgradering jämför Service Fabric det nya [programmanifestet](service-fabric-application-and-service-manifests.md) med den tidigare versionen och avgör vilka tjänster i programmet som kräver uppdateringar. Service Fabric jämför versionsnumren i tjänstmanifesten med versionsnumren i den tidigare versionen. Om en tjänst inte har ändrats uppgraderas inte den tjänsten.

## <a name="rolling-upgrades-overview"></a>Översikt över rullande uppgraderingar
I en rullande programuppgradering utförs uppgraderingen i etapper. I varje steg tillämpas uppgraderingen på en delmängd noder i klustret, en så kallad uppdateringsdomän. Därför är programmet tillgängligt under hela uppgraderingen. Under uppgraderingen kan klustret innehålla en blandning av gamla och nya versioner.

Därför måste de två versionerna vara framåt- och bakåtkompatibla. Om de inte är kompatibla ansvarar programadministratören för att mellanlagring av en uppgradering i flera faser för att upprätthålla tillgängligheten. I en uppgradering i flera faser uppgraderas det första steget till en mellanliggande version av programmet som är kompatibelt med den tidigare versionen. Det andra steget är att uppgradera den slutliga versionen som bryter kompatibiliteten med förhandsversionen, men är kompatibel med den mellanliggande versionen.

Uppdateringsdomäner anges i klustermanifestet när du konfigurerar klustret. Uppdatera domäner får inga uppdateringar i en viss ordning. En uppdateringsdomän är en logisk distributionsenhet för ett program. Uppdateringsdomäner gör att tjänsterna kan vara högtillgänglighet under en uppgradering.

Icke-rullande uppgraderingar är möjliga om uppgraderingen tillämpas på alla noder i klustret, vilket är fallet när programmet bara har en uppdateringsdomän. Den här metoden rekommenderas inte, eftersom tjänsten går ner och inte är tillgänglig vid tidpunkten för uppgraderingen. Dessutom ger Azure inga garantier när ett kluster har konfigurerats med endast en uppdateringsdomän.

När uppgraderingen är klar, alla tjänster och repliker (instanser) skulle stanna i samma version, dvs om uppgraderingen lyckas, kommer de att uppdateras till den nya versionen; Om uppgraderingen misslyckas och återställs, skulle de återställas till den gamla versionen.

## <a name="health-checks-during-upgrades"></a>Hälsokontroller under uppgraderingar
För en uppgradering måste hälsoprinciper anges (eller standardvärden användas). En uppgradering kallas framgångsrik när alla uppdateringsdomäner uppgraderas inom de angivna tidsbegränsningarna och när alla uppdateringsdomäner anses vara felfria.  En felfritt uppdateringsdomän innebär att uppdateringsdomänen har skickat alla hälsokontroller som anges i hälsoprincipen. En hälsoprincip kan till exempel föreskriva att alla tjänster i en programinstans måste vara *felfria*, eftersom hälsotillstånd definieras av Service Fabric.

Hälsoprinciper och kontroller under uppgradering av Service Fabric är service- och programoberoende. Det vill än, inga servicespecifika tester görs.  Tjänsten kan till exempel ha ett dataflödeskrav, men Service Fabric har inte informationen för att kontrollera dataflödet. Se [hälsoartiklarna](service-fabric-health-introduction.md) för de kontroller som utförs. De kontroller som sker under en uppgradering inkluderar tester för om programpaketet har kopierats korrekt, om instansen startades och så vidare.

Programmets hälsotillstånd är en aggregering av de underordnade entiteterna i programmet. Kort sagt, Service Fabric utvärderar hälsotillståndet för programmet genom den hälsa som rapporteras på ansökan. Den utvärderar också hälsan hos alla tjänster för programmet på detta sätt. Service Fabric utvärderar ytterligare hälsotillståndet för programtjänsterna genom att samla hälsotillståndet för sina underordnade, till exempel tjänstrepliken. När programmets hälsoprincip är nöjd kan uppgraderingen fortsätta. Om hälsoprincipen överträds misslyckas programuppgraderingen.

## <a name="upgrade-modes"></a>Uppgraderingslägen
Det läge som vi rekommenderar för uppgradering av programmet är det övervakade läget, som är det vanliga läget. Övervakat läge utför uppgraderingen på en uppdateringsdomän, och om alla hälsokontroller passerar (enligt den angivna principen) går vidare till nästa uppdateringsdomän automatiskt.  Om hälsokontroller misslyckas och/eller time-outs uppnås återställs uppgraderingen antingen för uppdateringsdomänen eller så ändras läget till oövervakad handbok. Du kan konfigurera uppgraderingen så att den väljer ett av dessa två lägen för misslyckade uppgraderingar. 

Oövervakad manuellt läge behöver manuell åtgärd efter varje uppgradering på en uppdateringsdomän för att starta uppgraderingen på nästa uppdateringsdomän. Hälsokontroller av service fabric utförs inte. Administratören utför hälso- eller statuskontrollerna innan uppgraderingen startas i nästa uppdateringsdomän.

## <a name="upgrade-default-services"></a>Uppgradera standardtjänster
Vissa standardtjänstparametrar som definierats i [programmanifestet](service-fabric-application-and-service-manifests.md) kan också uppgraderas som en del av en programuppgradering. Endast de tjänstparametrar som stöder ändras via [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) kan ändras som en del av en uppgradering. Hur standardtjänster ändras under programuppgraderingen är följande:

1. Standardtjänster i det nya programmanifestet som inte redan finns i klustret skapas.
2. Standardtjänster som finns i både tidigare och nya programmanifest uppdateras. Parametrarna för standardtjänsten i det nya programmanifestet skriver över parametrarna för den befintliga tjänsten. Programuppgraderingen återställs automatiskt om uppdateringen av en standardtjänst misslyckas.
3. Standardtjänster som inte finns i det nya programmanifestet tas bort om de finns i klustret. **Observera att om du tar bort en standardtjänst tas alla tjänstens tillstånd bort och inte kan ångras.**

När en programuppgradering återställs återställs standardtjänstparametrarna till sina gamla värden innan uppgraderingen startade, men borttagna tjänster kan inte återskapas med sitt gamla tillstånd.

> [!TIP]
> [Inställningen EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) kluster config måste vara *sant* för att aktivera regler 2) och 3) ovan (standardserviceuppdatering och borttagning). Den här funktionen stöds med början i Service Fabric version 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Uppgradera flera program med HTTPS-slutpunkter
Du måste vara noga med att inte använda **samma port** för olika instanser av samma program när du använder HTTP**S**. Anledningen är att Service Fabric inte kan uppgradera certifikatet för en av programinstanserna. Till exempel om program 1 eller program 2 båda vill uppgradera sina cert 1 till cert 2. När uppgraderingen sker kan Service Fabric ha rensat registreringen cert 1 med http.sys även om det andra programmet fortfarande använder den. För att förhindra detta upptäcker Service Fabric att det redan finns en annan programinstans som registrerats på porten med certifikatet (på grund av http.sys) och misslyckas med åtgärden.

Därför stöder Service Fabric inte uppgradering av två olika tjänster med **samma port** i olika programinstanser. Med andra ord kan du inte använda samma certifikat på olika tjänster på samma port. Om du behöver ha ett delat certifikat på samma port måste du se till att tjänsterna placeras på olika datorer med placeringsbegränsningar. Eller överväg att använda dynamiska portar för Service Fabric om möjligt för varje tjänst i varje programinstans. 

Om en uppgradering misslyckas med https, en felmeddelande som säger "Windows HTTP Server API stöder inte flera certifikat för program som delar en port."

## <a name="application-upgrade-flowchart"></a>Flödesschema för uppgradering av program
Flödesschemat som följer det här stycket kan hjälpa dig att förstå uppgraderingsprocessen för ett Service Fabric-program. I synnerhet beskriver flödet hur time-outs, inklusive *HealthCheckStableDuration*, *HealthCheckRetryTimeout*och *UpgradeHealthCheckInterval*, hjälper till att kontrollera när uppgraderingen i en uppdateringsdomän anses vara en framgång eller ett fel.

![Uppgraderingsprocessen för ett service fabric-program][image]

## <a name="next-steps"></a>Nästa steg
[Om du uppgraderar programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en programuppgradering med Visual Studio.

[Om du uppgraderar programmet med PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) får du en programuppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör dina programuppgraderingar kompatibla genom att lära dig hur du använder [data serialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar programmet genom att referera till [Avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [Felsökning av programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
