---
title: Programmets livscykel i Service Fabric
description: Beskriver utveckling, distribution, testning, uppgradering, underhåll och borttagning av Service Fabric-program.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: beeb1f1512cf94582dd561fa768f2e8e6649d686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378012"
---
# <a name="service-fabric-application-lifecycle"></a>Livscykeln för Service Fabric-program
Precis som med andra plattformar går ett program på Azure Service Fabric vanligtvis igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. Service Fabric ger förstklassigt stöd för hela programlivscykeln för molnprogram, från utveckling till distribution, daglig hantering och underhåll till eventuell avveckling. Tjänstmodellen gör det möjligt för flera olika roller att delta oberoende av dem i programmets livscykel. Den här artikeln innehåller en översikt över API:erna och hur de används av de olika rollerna under faserna i programlivscykeln för Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Roller för tjänstmodell
Tjänstmodellrollerna är:

* **Tjänstutvecklare**: Utvecklar modulära och generiska tjänster som kan återanvändas och användas i flera program av samma typ eller olika typer. En kötjänst kan till exempel användas för att skapa ett biljettprogram (helpdesk) eller ett e-handelsprogram (kundvagn).
* **Programutvecklare**: Skapar program genom att integrera en samling tjänster för att uppfylla vissa specifika krav eller scenarier. En e-handelswebbplats kan till exempel integrera "JSON Stateless Front-End Service", "Auction Stateful Service" och "Queue Stateful Service" för att skapa en auktionslösning.
* **Programadministratör**: Fattar beslut om programkonfigurationen (fyller i konfigurationsmallsparametrarna), distribution (mappning till tillgängliga resurser) och tjänstens kvalitet. En programadministratör bestämmer till exempel språket (engelska för exempelvis USA eller japan för Japan) för programmet. Ett annat distribuerat program kan ha olika inställningar.
* **Operatör**: Distribuerar program baserat på programkonfigurationen och kraven som anges av programadministratören. Till exempel en operatör avsättningar och distribuerar programmet och säkerställer att det körs i Azure. Operatörer övervakar programhälso- och prestandainformation och underhåller den fysiska infrastrukturen efter behov.

## <a name="develop"></a>Utveckla
1. En *tjänstutvecklare* utvecklar olika typer av tjänster med hjälp av programmeringsmodellen [Reliable Actors](service-fabric-reliable-actors-introduction.md) eller Reliable [Services.](service-fabric-reliable-services-introduction.md)
2. En *tjänstutvecklare* beskriver de utvecklade tjänsttyperna i en tjänstmanifestfil som består av en eller flera kod-, konfigurations- och datapaket.
3. En *programutvecklare* skapar sedan ett program med olika tjänsttyper.
4. En *programutvecklare* beskriver deklarativt programtypen i ett programmanifest genom att referera till tjänstmanifesten för de ingående tjänsterna och på lämpligt sätt åsidosätta och parameterisera olika konfigurations- och distributionsinställningar för de ingående tjänsterna.

Se [Komma igång med Reliable Actors](service-fabric-reliable-actors-get-started.md) och Kom igång med Reliable [Services](service-fabric-reliable-services-quick-start.md) för exempel.

## <a name="deploy"></a>Distribuera
1. En *programadministratör* anpassar programtypen till ett visst program som ska distribueras till ett Service Fabric-kluster genom att ange lämpliga parametrar för **ApplicationType-elementet** i programmanifestet.
2. En *operatör* överför programpaketet till klusteravbildningsarkivet med hjälp av metoden [ **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [ **copy-serviceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Programpaketet innehåller programmanifestet och insamlingen av servicepaket. Service Fabric distribuerar program från programpaketet som lagras i avbildningsarkivet, vilket kan vara en Azure blob-butik eller servicetjänsten Service Fabric.
3. *Operatorn* etablerar sedan programtypen i målklustret från det uppladdade programpaketet med metoden [ **ProvisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **cmdlet register-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)eller [ **etableringen en REST-åtgärd** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)för program .
4. När programmet har etablerats startar en *operatör* programmet med de parametrar som tillhandahålls av *programadministratören* med metoden [ **CreateApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **cmdlet för Ny-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)eller åtgärden [ **Skapa programÅTER.** ](https://docs.microsoft.com/rest/api/servicefabric/create-an-application)
5. När programmet har distribuerats använder en *operatör* metoden [ **CreateServiceAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **cmdlet New-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)eller åtgärden [ **Skapa service-REST** ](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) för att skapa nya tjänstinstanser för programmet baserat på tillgängliga tjänsttyper.
6. Programmet körs nu i Service Fabric-klustret.

Se [Distribuera ett program](service-fabric-deploy-remove-applications.md) för exempel.

## <a name="test"></a>Testa
1. När du har distribuerat till det lokala utvecklingsklustret eller ett testkluster kör en *tjänstutvecklare* det inbyggda redundanstestscenariot med hjälp av [**klasserna RedundanstestscenarioParametrar**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) och [**RedundansTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) eller [i cmdleten **Invoke-ServiceFabricFailoverTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Redundanstestscenariot kör en angiven tjänst genom viktiga övergångar och redundans för att säkerställa att den fortfarande är tillgänglig och fungerar.
2. *Tjänstutvecklaren* kör sedan det inbyggda kaostestscenariot med [**klasserna ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) och [**ChaosTestScenario,**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) eller [ **cmdleten Invoke-ServiceFabricChaosTestScenario** ](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Kaostestscenariot inducerar slumpmässigt flera nod-, kodpaket och replikfel i klustret.
3. *Tjänstutvecklaren* [testar service-to-service-kommunikation](service-fabric-testability-scenarios-service-communication.md) genom att skapa testscenarier som flyttar primära repliker runt klustret.

Mer information [finns i Introduktion till felanalystjänsten.](service-fabric-testability-overview.md)

## <a name="upgrade"></a>Uppgradera
1. En *tjänstutvecklare* uppdaterar de ingående tjänsterna i det instansierade programmet och/eller åtgärdar buggar och tillhandahåller en ny version av tjänstmanifestet.
2. En *programutvecklare* åsidosätter och parameteriserar konfigurations- och distributionsinställningarna för de konsekventa tjänsterna och tillhandahåller en ny version av programmanifestet. Programutvecklaren införlivar sedan de nya versionerna av tjänstmanifesten i programmet och tillhandahåller en ny version av programtypen i ett uppdaterat programpaket.
3. En *programadministratör* införlivar den nya versionen av programtypen i målprogrammet genom att uppdatera lämpliga parametrar.
4. En *operatör* överför det uppdaterade programpaketet till klusteravbildningsarkivet med metoden [ **CopyApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [ **copy-serviceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Programpaketet innehåller programmanifestet och insamlingen av servicepaket.
5. En *operatör* etablerar den nya versionen av programmet i målklustret med hjälp av metoden [ **ProvisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **cmdlet Register-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)eller [ **etableringen av en REST-åtgärd** ](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)för program .
6. En *operatör* uppgraderar målprogrammet till den nya versionen med metoden [ **UpgradeApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **start-serviceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)eller [ **Upgrade an Application** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. En *operatör* kontrollerar förloppet för uppgraderingen med metoden [ **GetApplicationUpgradeProgressAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **get-serviceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)eller [REST-åtgärden **Hämta programuppgraderingsförlopp** ](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Om det behövs ändrar och återställer *operatören* parametrarna för den aktuella programuppgraderingen med metoden [ **UpdateApplicationUpgradeAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **update-serviceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)eller åtgärden [ **Update Application Upgrade** REST](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Om det behövs återställer *operatören* den aktuella programuppgraderingen med metoden [ **RollbackApplicationUpgradeAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Start-ServiceFabricApplicationRollback** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)eller [ **återställningsprogrammet Uppgradera** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric uppgraderar målprogrammet som körs i klustret utan att förlora tillgängligheten för någon av dess ingående tjänster.

Mer om [programuppgraderingsguiden](service-fabric-application-upgrade-tutorial.md) finns i självstudien för uppgradering av programmet.

## <a name="maintain"></a>Underhåll
1. För uppgraderingar och korrigeringar av operativsystem, gränssnitt service fabric med Azure-infrastrukturen för att garantera tillgänglighet för alla program som körs i klustret.
2. För uppgraderingar och korrigeringar till Service Fabric-plattformen uppgraderar Service Fabric sig själv utan att förlora tillgängligheten för något av de program som körs i klustret.
3. En *programadministratör* godkänner tillägg eller borttagning av noder från ett kluster efter att ha analyserat historiska kapacitetsutnyttjandedata och beräknat framtida behov.
4. En *operator* lägger till och tar bort noder som anges av *programadministratören*.
5. När nya noder läggs till eller befintliga noder tas bort från klustret, laddar Service Fabric automatiskt in programmen som körs över alla noder i klustret för att uppnå optimal prestanda.

## <a name="remove"></a>Ta bort
1. En *operatör* kan ta bort en viss instans av en tjänst som körs i klustret utan att ta bort hela programmet med metoden [ **DeleteServiceAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **cmdlet remove-ServiceFabricService** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)eller [rest-åtgärden **Delete Service** ](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. En *operatör* kan också ta bort en programinstans och alla dess tjänster med metoden [ **DeleteApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **cmdlet remove-ServiceFabricApplication** ](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)eller åtgärden [ **Delete Application** REST](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. När programmet och tjänsterna har stoppats kan *operatören* avetablera programtypen med metoden [ **UnprovisionApplicationAsync** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **cmdlet Unregister-ServiceFabricApplicationType** ](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype)eller [ **Oetablering av en REST-åtgärd** ](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application)för program . Om du tar bort etableringen av programtypen tas inte programpaketet bort från ImageStore. Du måste ta bort programpaketet manuellt.
4. En *operatör* tar bort programpaketet från ImageStore med metoden [ **RemoveApplicationPackage** ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [ **cmdlet Remove-ServiceFabricApplicationPackage** ](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Se [Distribuera ett program](service-fabric-deploy-remove-applications.md) för exempel.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar, testar och hanterar serviceinfrastrukturprogram och tjänster finns i:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)
* [Uppgradering av programmet](service-fabric-application-upgrade.md)
* [Översikt över testbarhet](service-fabric-testability-overview.md)
