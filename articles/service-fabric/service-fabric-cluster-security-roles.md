---
title: 'Säkerhet för Service Fabric-kluster: klientroller | Microsoft Docs'
description: Den här artikeln beskrivs två klientroller och de behörigheter som finns i rollerna.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: ''
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ed000dc4be1ae45382d688d4a596ec745c69d0bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711168"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Rollbaserad åtkomstkontroll för Service Fabric-klienter
Azure Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Åtkomstkontroll kan du Klusteradministratören att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare, vilket gör att klustret säkrare.  

**Administratörer** har fullständig åtkomst till funktioner för hantering (inklusive Läs-och skrivbehörighet). Som standard **användare** bara har läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.

Du kan ange två klientroller (administratör och klienten) vid tidpunkten för klustret har skapats genom att tillhandahålla separata certifikat för varje. Se [Service Fabric-kluster-säkerhet](service-fabric-cluster-security.md) mer information om hur du konfigurerar ett säkert Service Fabric-kluster.

## <a name="default-access-control-settings"></a>Inställningar för åtkomstkontroll som standard
Kontrolltypen administratör åtkomst har fullständig åtkomst till alla FabricClient APIs. Den kan utföra alla läsningar och skrivningar på Service Fabric-klustret, inklusive följande åtgärder:

### <a name="application-and-service-operations"></a>Program- och åtgärder
* **CreateService**: du skapar en tjänst                             
* **CreateServiceFromTemplate**: du skapar från mallen en tjänst                             
* **UpdateService**: uppdateringar av tjänsten                             
* **DeleteService**: Tjänsten tas bort                             
* **ProvisionApplicationType**: typ av programetablering                             
* **CreateApplication**: skapa program                               
* **DeleteApplication**: ta bort program                             
* **UpgradeApplication**: startar eller att avbryta programuppgraderingar                             
* **UnprovisionApplicationType**: programmet avetableringen av typen                             
* **MoveNextUpgradeDomain**: återupptar programuppgraderingar med en explicit uppdateringsdomän                             
* **ReportUpgradeHealth**: återupptar programuppgraderingar med aktuella uppgraderingsförloppet                             
* **ReportHealth**: reporting hälsotillstånd                             
* **PredeployPackageToNode**: fördistribution API                            
* **CodePackageControl**: starta om kodpaket                             
* **RecoverPartition**: återställa en partition                             
* **RecoverPartitions**: återställa partitioner                             
* **RecoverServicePartitions**: återställa tjänstpartitioner                             
* **RecoverSystemPartitions**: återställa systempartitioner för tjänsten                             

### <a name="cluster-operations"></a>Klusteråtgärder
* **ProvisionFabric**: MSI och/eller kluster manifest etablering                             
* **UpgradeFabric**: startar klusteruppgradering                             
* **UnprovisionFabric**: MSI och/eller kluster manifest avetableringen av                         
* **MoveNextFabricUpgradeDomain**: återupptar klusteruppgradering med en explicit uppdateringsdomän                             
* **ReportFabricUpgradeHealth**: återupptar klusteruppgradering med aktuella uppgraderingsförloppet                             
* **StartInfrastructureTask**: infrastruktur uppgifter                             
* **FinishInfrastructureTask**: Slutför infrastrukturen                             
* **InvokeInfrastructureCommand**: kommandon för hantering av infrastruktur-aktivitet                              
* **ActivateNode**: aktivera en nod                             
* **DeactivateNode**: inaktivera en nod                             
* **DeactivateNodesBatch**: inaktivera flera noder                             
* **RemoveNodeDeactivations**: återställer inaktivering på flera noder                             
* **GetNodeDeactivationStatus**: Kontrollera inaktiveringsstatus                             
* **NodeStateRemoved**: reporting Nodtillstånd tas bort                             
* **ReportFault**: rapporterar fel                             
* **FileContent**: bild Filöverföring för store-klienten (extern till kluster)                             
* **FileDownload**: bild store klienten file download initiation (extern till kluster)                             
* **InternalList**: bild store fil lista klientåtgärden (internt)                             
* **Ta bort**: bild store ta bort klientåtgärden                              
* **Ladda upp**: bild överföringen för store-klienten                             
* **NodeControl**: starta, stoppa och starta om noderna                             
* **MoveReplicaControl**: flytta repliker från en nod till en annan                             

### <a name="miscellaneous-operations"></a>Diverse åtgärder
* **Ping**: klienten ping                             
* **Fråga**: alla frågor som tillåts
* **NameExists**: naming URI förekomst kontroller                             

Användartyp åtkomstkontroll är som standard begränsad till följande åtgärder: 

* **EnumerateSubnames**: namngivning av URI-uppräkning                             
* **EnumerateProperties**: naming-uppräkning                             
* **PropertyReadBatch**: namngivning av egenskapen läsåtgärder                             
* **GetServiceDescription**: long-avsökning tjänstmeddelanden och läsa tjänsten beskrivningar                             
* **ResolveService**: klagomål-baserad tjänst-lösning                             
* **ResolveNameOwner**: lösa namngivning URI-ägare                             
* **ResolvePartition**: hur du löser systemtjänster                             
* **ServiceNotifications**: händelsebaserad tjänstmeddelanden                             
* **GetUpgradeStatus**: avsökningsintervall uppgradera programstatus                             
* **GetFabricUpgradeStatus**: avsökningsintervall uppgradera klusterstatus                             
* **InvokeInfrastructureQuery**: fråga infrastrukturen                             
* **Lista**: bild store klientåtgärden fil lista                             
* **ResetPartitionLoad**: återställer belastning för en redundansenheten                             
* **ToggleVerboseServicePlacementHealthReporting**: växla utförlig service placering tillståndsrapportering                             

Administratörskontroll för åtkomst har också åtkomst till de föregående åtgärderna.

## <a name="changing-default-settings-for-client-roles"></a>Ändra standardinställningar för klientroller
I manifestfilen för klustret, kan du ange administratörsfunktioner till klienten om det behövs. Du kan ändra standardinställningarna genom att gå till den **Infrastrukturinställningarna** alternativet under [Skapa kluster](service-fabric-cluster-creation-via-portal.md), och lämna de föregående inställningarna i den **namn**,  **administratören**, **användaren**, och **värdet** fält.

## <a name="next-steps"></a>Nästa steg
[Service Fabric-kluster-säkerhet](service-fabric-cluster-security.md)

[Skapa för Service Fabric-kluster](service-fabric-cluster-creation-via-portal.md)

