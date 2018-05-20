---
title: Programmet livscykel i Service Fabric | Microsoft Docs
description: Beskriver utveckla, distribuera, testa, uppgradera, underhålla och tar bort Service Fabric-program.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: ryanwi
ms.openlocfilehash: e7dddfca3640615cb851fb6dce9eaa80260ccbf6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-lifecycle"></a>Livscykel för Service Fabric-program
Som med andra plattformar, ett program på Azure Service Fabric vanligtvis går igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. Service Fabric har förstklassigt stöd för fullständiga programmet livscykeln för molnprogram, från utveckling till distribution, dagliga hantering och underhåll för inaktivering av eventuell. Tjänstmodellen gör det möjligt för flera olika roller separat delta i programmet livscykel. Den här artikeln innehåller en översikt över de API: er och hur de används av olika roller i faserna i livscykeln för Service Fabric-programmet.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

Följande Microsoft Virtual Academy videon beskriver hur du hanterar livscykeln för programmet: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Service model-roller
Service model-rollerna är:

* **Tjänsten developer**: utvecklar modulära och allmänna tjänster som kan nytt säker och används i flera program av samma typ eller olika typer. Till exempel kan en kö-tjänst användas för att skapa ett loggnings-program (supportavdelning) eller ett program för e-handel (kundvagn).
* **Programutvecklaren**: skapar program genom att integrera en samling tjänster att uppfylla vissa särskilda krav eller scenarier. Till exempel webbplatser för e-handel kan integrera ”JSON tillståndslös frontend-tjänst”, ”auktionen Stateful tjänst” och ”Stateful Kötjänsten” för att skapa en lösning för auctioning.
* **Programadministratör**: fattar beslut om programkonfigurationen (fylla i mallparametrarna configuration), distribution (mappning till de tillgängliga resurserna) och tjänsternas kvalitet. Till exempel beslutar programadministratör språket (på engelska för USA) eller japanska för Japan, till exempel för programmet. Ett annat distribuerat program kan ha olika inställningar.
* **Operatorn**: distribuerar programmen baserat på programmets konfiguration och krav som anges av administratören för programmet. Till exempel en operator etablerar och distribuerar programmet och ser till att den körs i Azure. Operatörer övervaka hälsotillstånd och prestanda programinformation och underhålla den fysiska infrastrukturen efter behov.

## <a name="develop"></a>Utveckla
1. En *tjänsten developer* utvecklar olika typer av tjänster med hjälp av den [Reliable Actors](service-fabric-reliable-actors-introduction.md) eller [Reliable Services](service-fabric-reliable-services-introduction.md) programmeringsmodell.
2. En *tjänsten developer* deklarativt beskriver utvecklade tjänsttyper i en manifestfil på tjänsten som består av ett eller flera paket för koden, konfiguration och data.
3. En *programutvecklaren* skapar ett program med hjälp av olika typer.
4. En *programutvecklaren* deklarativt beskriver programtyp i ett programmanifest genom att referera till service manifest dessa tjänster och korrekt åsidosätter och Parameterisera olika inställningar för konfiguration och distribution av dessa tjänster.

Se [Kom igång med Reliable Actors](service-fabric-reliable-actors-get-started.md) och [komma igång med tillförlitlig Services](service-fabric-reliable-services-quick-start.md) exempel.

## <a name="deploy"></a>Distribuera
1. En *programadministratör* anpassar programtyp till ett visst program som ska distribueras till ett Service Fabric-kluster genom att ange lämpliga parametrar för den **ApplicationType** element i programmanifestet.
2. En *operatorn* överför programpaketet till klustret image store med hjälp av den [ **CopyApplicationPackage** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) eller [  **Kopiera ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Programpaketet innehåller programmanifestet och insamling av servicepaket. Service Fabric distribuerar program från programpaket som lagras i image store, som kan vara en Azure blob-butik eller tjänsten Service Fabric-system.
3. Den *operatorn* etablerar programtyp i målkluster från den överförda programpaket med den [ **ProvisionApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_),  [ **Registrera ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), eller [ **etablera ett program** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. När du har etablerat program, en *operatorn* startar programmet med de parametrar som tillhandahålls av den *programadministratör* med hjälp av den [  **CreateApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **ny ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication), eller [ **skapa program**  REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. När programmet har distribuerats, en *operatorn* använder den [ **CreateServiceAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **ny ServiceFabricService**  cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice), eller [ **skapa tjänst** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) att skapa nya instanser av tjänsten för programmet baserat på tillgängliga typer.
6. Programmet körs nu i Service Fabric-klustret.

Se [distribuera ett program](service-fabric-deploy-remove-applications.md) exempel.

## <a name="test"></a>Testa
1. När du har distribuerat för lokal utveckling klustret eller ett testkluster en *tjänsten developer* körs Testscenario inbyggd redundans med hjälp av den [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) och [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) klasser, eller [ **Invoke-ServiceFabricFailoverTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Testscenario redundans kör en angiven tjänst via viktiga övergångar och redundans så att den är fortfarande tillgängliga och fungera.
2. Den *tjänsten developer* sedan körs den inbyggda chaos Testa scenariot med hjälp av den [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) och [  **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) klasser, eller [ **Invoke-ServiceFabricChaosTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Testscenario chaos startar slumpmässigt flera nod kodpaketet och repliken fel i klustret.
3. Den *tjänsten developer* [testar service-to-service-kommunikation](service-fabric-testability-scenarios-service-communication.md) genom att skriva testscenarier som flyttar primära repliker runt klustret.

Se [introduktion till fel Analysis Service](service-fabric-testability-overview.md) för mer information.

## <a name="upgrade"></a>Uppgradera
1. En *tjänsten developer* uppdaterar instansierad programmet tjänsterna och/eller åtgärdar buggar och innehåller en ny version av service manifest.
2. En *programutvecklaren* åsidosätter parameterizes inställningarna konfiguration och distribution för konsekvent tjänster och innehåller en ny version av programmanifestet. Programutvecklaren sedan inkluderar nya versioner av service manifest i programmet och innehåller en ny version av programtyp i en uppdaterad programpaketet.
3. En *programadministratör* inkluderar den nya versionen av programtypen i målprogrammet genom att uppdatera lämpliga parametrar.
4. En *operatorn* överför det uppdaterade programpaketet till i klustret image store med hjälp av [ **CopyApplicationPackage** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) eller [ **Kopiera ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Programpaketet innehåller programmanifestet och insamling av servicepaket.
5. En *operatorn* etablerar den nya versionen av programmet i målklustret med hjälp av den [ **ProvisionApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), [  **Registrera ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype), eller [ **etablera ett program** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. En *operatorn* uppgraderar målprogrammet till den nya versionen med hjälp av den [ **UpgradeApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), [  **Start-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade), eller [ **uppgradera ett program** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. En *operatorn* kontrollerar förloppet för att uppgradera med hjälp av den [ **GetApplicationUpgradeProgressAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), [  **Get-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade), eller [ **hämta programmet uppgradering pågår** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Om det behövs det *operatorn* ändrar och återställer parametrarna för det aktuella programmet uppgradera med hjälp av den [ **UpdateApplicationUpgradeAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **Uppdatering ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade), eller [ **uppdatering programmet uppgradera** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Om det behövs det *operatorn* återställer det aktuella programmet uppgradera med hjälp av den [ **RollbackApplicationUpgradeAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), [ **Start ServiceFabricApplicationRollback** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback), eller [ **återställning programmet uppgradera** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric uppgraderar målprogrammet körs i klustret utan att förlora tillgängligheten för någon av dessa tjänster.

Finns det [uppgradera självstudien](service-fabric-application-upgrade-tutorial.md) exempel.

## <a name="maintain"></a>Underhåll
1. För uppgradering av operativsystemet och korrigeringar, Service Fabric-gränssnitt med Azure-infrastrukturen att garantera tillgängligheten för alla program som körs i klustret.
2. För uppgraderingar och korrigeringsfiler för Service Fabric-plattformen uppgraderar Service Fabric själva utan att förlora tillgänglighet för någon av de program som körs i klustret.
3. En *programadministratör* godkänner tillägg eller borttagning av noder från ett kluster när du har analyserat historiska kapacitet användningsdata och planerade framtida behov.
4. En *operatorn* lägger till och tar bort noder som anges av den *programadministratör*.
5. När nya noder läggs till eller befintliga noderna tas bort från klustret, Service Fabric automatiskt belastningsutjämnar program som körs på alla noder i klustret för att uppnå bästa prestanda.

## <a name="remove"></a>Ta bort
1. En *operatorn* kan ta bort en specifik instans av en tjänst som körs i klustret utan att ta bort hela programmet med hjälp av den [ **DeleteServiceAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), [ **Ta bort ServiceFabricService** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice), eller [ **ta bort tjänst** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. En *operatorn* kan också ta bort en instans av programmet och alla dess tjänster med hjälp av den [ **DeleteApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), [  **Ta bort ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication), eller [ **ta bort programmet** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. När program och tjänster har stoppats kan den *operatorn* kan avetablera den typ som använder den [ **UnprovisionApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), [ **Unregister-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype), eller [ **avetablera ett program** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Avetablering programtypen tas inte bort programpaketet från ImageStore. Du måste manuellt ta bort programpaketet.
4. En *operatorn* tar bort programpaketet från Avbildningsarkiv med hjälp av den [ **RemoveApplicationPackage** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) eller [  **Ta bort ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Se [distribuera ett program](service-fabric-deploy-remove-applications.md) exempel.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar Se testning och hantera Service Fabric-program och tjänster:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)
* [Programuppgradering](service-fabric-application-upgrade.md)
* [Möjlighet att testa översikt](service-fabric-testability-overview.md)
