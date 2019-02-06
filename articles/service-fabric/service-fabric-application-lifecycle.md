---
title: Programmets livscykel i Service Fabric | Microsoft Docs
description: Beskriver utveckla, distribuera, testa, uppgradera, underhålla och ta bort Service Fabric-program.
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
ms.openlocfilehash: cb44311ecdf6a2c9284b14884184863237422f96
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754548"
---
# <a name="service-fabric-application-lifecycle"></a>Livscykeln för Service Fabric
Som med andra plattformar, ett program på Azure Service Fabric vanligtvis går igenom följande faser: design, utveckling, testning, distribution, uppgradera, underhållet och borttagningen. Service Fabric erbjuder förstklassig support för hela programlivscykeln för molnprogram, från utveckling till distribution, daglig hantering och underhåll till eventuell inaktivering. Tjänstmodellen gör det möjligt för flera olika roller att delta oberoende av varandra i programmets hela livscykel. Den här artikeln innehåller en översikt över API: er och hur de används av de olika rollerna i hela faserna i livscykeln för Service Fabric-program.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

Följande Microsoft Virtual Academy videoklipp beskriver hur du hanterar livscykeln för din: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Service model-roller
Service model-roller är:

* **Tjänsten developer**: Utvecklar modulära och allmänna tjänster som kan vara dedikerat igen och används i flera program av samma typ eller olika typer. Till exempel kan en kö-tjänst användas för att skapa ett loggnings-program (supportavdelning) eller ett e-handelsprogram (kundvagn).
* **Programutvecklare**: Skapar program genom att integrera en samling tjänster att uppfylla vissa särskilda krav eller scenarier. Webbplatser för e-handel kan till exempel integreras ”JSON tillståndslösa Front-End Service”, ”auktion tillståndskänslig tjänst” och ”kö tillståndskänslig tjänst” för att skapa en lösning för auctioning.
* **Programadministratör**: Fattar beslut på programkonfiguration (fylla i mallparametrarna configuration), distribution (mappning till tillgängliga resurser) och tjänsternas kvalitet. Till exempel beslutar programadministratör språkinställningen (på engelska för USA) eller japanska för Japan, till exempel av programmet. Ett annat distribuerade program kan ha olika inställningar.
* **Operatorn**: Distribuerar programmen baserat på programmets konfiguration och krav som anges av administratören för programmet. Exempelvis kan en operatör etablerar och distribuerar programmet och ser till att den körs i Azure. Operatörer övervaka hälsotillstånd och prestanda programinformation och underhålla den fysiska infrastrukturen efter behov.

## <a name="develop"></a>Utveckla
1. En *tjänsten developer* utvecklar olika typer av tjänster med hjälp av den [Reliable Actors](service-fabric-reliable-actors-introduction.md) eller [Reliable Services](service-fabric-reliable-services-introduction.md) programmeringsmodell.
2. En *tjänsten developer* deklarativt beskriver utvecklade tjänsttyper i tjänstmanifestfilen som består av ett eller flera paket för kod, konfiguration och data.
3. En *programutvecklare* skapar ett program med olika tjänsttyper.
4. En *programutvecklare* deklarativt beskriver vilken typ av program i ett programmanifest genom att referera till tjänstmanifest av dessa tjänster och på rätt sätt åsidosätta och Parameterisera olika inställningar för konfiguration och distribution av dessa tjänster.

Se [Kom igång med Reliable Actors](service-fabric-reliable-actors-get-started.md) och [Kom igång med Reliable Services](service-fabric-reliable-services-quick-start.md) exempel.

## <a name="deploy"></a>Distribuera
1. En *programadministratör* efter vilken typ av program till ett visst program som ska distribueras till ett Service Fabric-kluster genom att ange lämpliga parametrar för den **ApplicationType** element i manifestet.
2. En *operatorn* överför programpaketet till klustrets avbildningsarkiv med hjälp av den [ **CopyApplicationPackage** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [  **Kopiera ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Programpaketet innehåller programmanifestet och insamling av paket. Service Fabric distribuerar program från det programpaket som lagras i avbildningsarkivet, som kan vara en Azure-blobblagring eller Service Fabric-systemtjänsten.
3. Den *operatorn* etablerar programtyp i målklustret från den uppladdade programpaket med den [ **ProvisionApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient),  [ **Registrera ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), eller [ **etablera ett program** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. När du har etablerat programmet, en *operatorn* startar programmet med parametrar som tillhandahålls av den *programadministratör* med hjälp av den [  **CreateApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **New-ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication), eller [ **skapa program**  REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. När programmet har distribuerats, en *operatorn* använder den [ **CreateServiceAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **New-ServiceFabricService**  cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice), eller [ **skapa Service** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) att skapa nya instanser av tjänsten för appen baserat på tillgängliga tjänsttyper.
6. Programmet körs nu i Service Fabric-klustret.

Se [distribuera ett program](service-fabric-deploy-remove-applications.md) exempel.

## <a name="test"></a>Testa
1. När du har distribuerat till det lokala utvecklingsklustret eller ett testkluster en *tjänsten developer* körs Testscenario inbyggd växling vid fel med hjälp av den [ **FailoverTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) och [ **FailoverTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) klasser, eller [ **Invoke-ServiceFabricFailoverTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Testa redundansscenario körs en angiven tjänst via viktiga övergångar och redundans så att den är fortfarande tillgängliga och fungerar.
2. Den *tjänsten developer* kör sedan inbyggda chaos test scenariot med den [ **ChaosTestScenarioParameters** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) och [  **ChaosTestScenario** ](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) klasser, eller [ **Invoke-ServiceFabricChaosTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Testscenario chaos startar slumpmässigt flera noden kodpaketet och repliken fel i klustret.
3. Den *tjänsten developer* [testar tjänst-till-tjänst-kommunikation](service-fabric-testability-scenarios-service-communication.md) genom att redigera test-scenarier som flyttar primära repliker runt i klustret.

Se [introduktion till Fault Analysis Service](service-fabric-testability-overview.md) för mer information.

## <a name="upgrade"></a>Uppgradera
1. En *tjänsten developer* uppdaterar dessa tjänster för skapade instanser programmet och/eller åtgärdar buggar och innehåller en ny version av tjänstmanifestet.
2. En *programutvecklare* åsidosätter och funktionsfråga och inställningarna för de enhetliga tjänsterna och innehåller en ny version av manifestet. Programutvecklare sedan inkluderar de nya versionerna av tjänstmanifest i programmet och innehåller en ny version av programtyp i programpaket uppdaterade.
3. En *programadministratör* inkluderar den nya versionen av programtypen i målprogrammet genom att uppdatera lämpliga parametrar.
4. En *operatorn* överför det uppdaterade programpaketet till klustret bild store med den [ **CopyApplicationPackage** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [ **Kopiera ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Programpaketet innehåller programmanifestet och insamling av paket.
5. En *operatorn* etablerar den nya versionen av programmet i målklustret med hjälp av den [ **ProvisionApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Registrera ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype), eller [ **etablera ett program** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. En *operatorn* uppgraderar målprogrammet till den nya versionen med den [ **UpgradeApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Start-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade), eller [ **uppgradera ett program** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. En *operatorn* kontrollerar förloppet för att uppgradera med hjälp av den [ **GetApplicationUpgradeProgressAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Get-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade), eller [ **hämta programmet uppgradera förloppet** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Om det behövs, de *operatorn* ändrar och återställer parametrarna för det aktuella programmet uppgradera med den [ **UpdateApplicationUpgradeAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Uppdatering ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade), eller [ **Update Application uppgradera** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. Om det behövs, de *operatorn* återställer det aktuella programmet uppgradera med den [ **RollbackApplicationUpgradeAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Start ServiceFabricApplicationRollback** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback), eller [ **återställning programmet uppgradera** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric uppgraderar målprogrammet körs i klustret utan att förlora tillgängligheten för någon av dess dessa tjänster.

Se den [självstudier för uppgradering av programmet](service-fabric-application-upgrade-tutorial.md) exempel.

## <a name="maintain"></a>Underhåll
1. För uppgradering av operativsystemet och korrigeringar, Service Fabric-gränssnitt med Azure-infrastrukturen att garantera tillgängligheten för alla program som körs i klustret.
2. För uppgraderingar och korrigeringar för Service Fabric-plattformen uppgraderar Service Fabric själva utan att förlora tillgängligheten för någon av de program som körs i klustret.
3. En *programadministratör* godkänner tillägg och borttagning av noder från ett kluster när du har analyserat historiska kapacitet användningsdata och beräknade framtida efterfrågan.
4. En *operatorn* lägger till och tar bort noder som anges av den *programadministratör*.
5. När nya noder läggs till eller tas bort från klustret befintliga noderna, Service Fabric automatiskt belastningsutjämnar program som körs på alla noder i klustret för att uppnå optimala prestanda.

## <a name="remove"></a>Ta bort
1. En *operatorn* kan ta bort en specifik instans av en pågående tjänst i klustret utan att ta bort hela programmet med den [ **DeleteServiceAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [ **Remove-ServiceFabricService** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice), eller [ **ta bort tjänst** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. En *operatorn* kan också ta bort en programinstans och alla dess tjänster med hjälp av den [ **DeleteApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [  **Ta bort ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication), eller [ **ta bort programmet** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. När de program och tjänster har stoppats medför den *operatorn* kan avetablera typen programmet med den [ **UnprovisionApplicationAsync** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [ **Avregistrera ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype), eller [ **avetablera ett program** REST-åtgärden](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Avetableringen av programtypen tar inte bort programpaketet från ImageStore. Du måste manuellt ta bort programpaketet.
4. En *operatorn* tar bort programpaketet från ImageStore med hjälp av den [ **RemoveApplicationPackage** metoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) eller [  **Ta bort ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Se [distribuera ett program](service-fabric-deploy-remove-applications.md) exempel.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utvecklar, se testa och hantera Service Fabric-program och tjänster:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)
* [Programuppgradering](service-fabric-application-upgrade.md)
* [Översikt över testning](service-fabric-testability-overview.md)
