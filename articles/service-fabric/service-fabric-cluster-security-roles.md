---
title: "Säkerhet för Service Fabric-kluster: klienten roller | Microsoft Docs"
description: "Den här artikeln beskrivs två klienten roller och behörigheter till rollerna."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: 
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 85935e60bba4b27972282700e2e9c9a22b403bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Rollbaserad åtkomstkontroll för Service Fabric-klienter
Azure Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster: administratörs- och. Åtkomstkontroll kan Klusteradministratören att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare, vilket gör att klustret säkrare.  

**Administratörer** ha fullständig åtkomst till funktioner för hantering (inklusive funktioner för läsning och skrivning). Som standard **användare** bara har läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.

Du kan ange två klienten roller (administratör och klient) när klustret skapas genom att tillhandahålla olika certifikat för varje. Se [Service Fabric-Klustersäkerhet](service-fabric-cluster-security.md) mer information om hur du konfigurerar en säker Service Fabric-klustret.

## <a name="default-access-control-settings"></a>Inställningar för åtkomstkontroll som standard
Kontroller av typen administratör åtkomst har fullständig åtkomst till alla FabricClient APIs. Den kan utföra alla läsning och skrivning på Service Fabric-klustret, inklusive följande åtgärder:

### <a name="application-and-service-operations"></a>Program- och tjänståtgärder
* **CreateService**: skapar en tjänst                             
* **CreateServiceFromTemplate**: tjänsten skapa från mall                             
* **UpdateService**: hantera uppdateringar                             
* **DeleteService**: Tjänsten tas bort                             
* **ProvisionApplicationType**: programmet typen etablering                             
* **CreateApplication**: skapa program                               
* **DeleteApplication**: ta bort program                             
* **UpgradeApplication**: startar eller störa programuppgraderingar                             
* **UnprovisionApplicationType**: programmet typen avetablering                             
* **MoveNextUpgradeDomain**: återupptar programuppgraderingar med en explicit uppdateringsdomän                             
* **ReportUpgradeHealth**: återupptar programuppgraderingar med den aktuella Uppgraderingsförlopp                             
* **ReportHealth**: reporting hälsa                             
* **PredeployPackageToNode**: hjälp av noggrann API                            
* **CodePackageControl**: starta om koden paket                             
* **RecoverPartition**: återställning av en partition                             
* **RecoverPartitions**: återställning av partitioner                             
* **RecoverServicePartitions**: återställa tjänsten partitioner                             
* **RecoverSystemPartitions**: återställa tjänsten systempartitionerna                             

### <a name="cluster-operations"></a>Klusteråtgärder
* **ProvisionFabric**: MSI och/eller klusternamnresursen manifest etablering                             
* **UpgradeFabric**: starta klusteruppgradering                             
* **UnprovisionFabric**: MSI och/eller klusternamnresursen manifest avetablering                         
* **MoveNextFabricUpgradeDomain**: återupptar klusteruppgradering med en explicit uppdateringsdomän                             
* **ReportFabricUpgradeHealth**: återupptar klusteruppgradering med den aktuella Uppgraderingsförlopp                             
* **StartInfrastructureTask**: från infrastrukturen                             
* **FinishInfrastructureTask**: Slutför infrastrukturen                             
* **InvokeInfrastructureCommand**: kommandon för hantering av infrastruktur-aktivitet                              
* **ActivateNode**: aktivera en nod                             
* **DeactivateNode**: inaktivera en nod                             
* **DeactivateNodesBatch**: inaktivera flera noder                             
* **RemoveNodeDeactivations**: Återför inaktiveringen på flera noder                             
* **GetNodeDeactivationStatus**: Kontrollera status för inaktivering                             
* **NodeStateRemoved**: reporting nodens tillstånd tas bort                             
* **ReportFault**: fault-rapportering                             
* **FileContent**: image store-klienten filöverföring (extern till kluster)                             
* **FileDownload**: image store klienten filen download inledande (extern till kluster)                             
* **InternalList**: image store-klienten listan filåtgärd (internt)                             
* **Ta bort**: image store ta bort klientåtgärden                              
* **Överför**: image store Överföringsåtgärden för klienten                             
* **NodeControl**: starta, stoppa och starta om noder                             
* **MoveReplicaControl**: flytta repliker från en nod till en annan                             

### <a name="miscellaneous-operations"></a>Diverse åtgärder
* **Ping**: klienten ping                             
* **Frågan**: alla frågor som tillåts
* **NameExists**: namnge URI finns kontroller                             

Typ av användaren åtkomstkontroll är som standard begränsad till följande åtgärder: 

* **EnumerateSubnames**: namnge URI uppräkning                             
* **EnumerateProperties**: namnge egenskapen uppräkning                             
* **PropertyReadBatch**: namnge egenskapen läsåtgärder                             
* **GetServiceDescription**: long avsökning tjänstmeddelanden och läsa tjänsten beskrivningar                             
* **ResolveService**: klagomål-baserad tjänst-lösning                             
* **ResolveNameOwner**: lösa namngivning URI-ägare                             
* **ResolvePartition**: lösa systemtjänster                             
* **ServiceNotifications**: händelsebaserat tjänstmeddelanden                             
* **GetUpgradeStatus**: avsökning uppgraderingsstatus för programmet                             
* **GetFabricUpgradeStatus**: avsökning uppgraderingsstatus för kluster                             
* **InvokeInfrastructureQuery**: frågar infrastrukturen                             
* **Listan**: image store klientåtgärden filen lista                             
* **ResetPartitionLoad**: återställa belastningen för en enhet för växling vid fel                             
* **ToggleVerboseServicePlacementHealthReporting**: växla utförlig service placering hälsa reporting                             

Åtkomstkontroll admin har också åtkomst till föregående operationer.

## <a name="changing-default-settings-for-client-roles"></a>Ändra standardinställningarna för klienten roller
I manifestfilen klustret, kan du ange admin funktioner till klienten om det behövs. Du kan ändra standardvärdena genom att gå till den **Infrastrukturinställningarna** alternativ under [Skapa kluster](service-fabric-cluster-creation-via-portal.md), och ge föregående inställningarna i den **namn**,  **Admin**, **användare**, och **värdet** fält.

## <a name="next-steps"></a>Nästa steg
[Säkerhet för Service Fabric-kluster](service-fabric-cluster-security.md)

[Service Fabric-kluster](service-fabric-cluster-creation-via-portal.md)

