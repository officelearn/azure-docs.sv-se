---
title: 'Service Fabric kluster säkerhet: klientroller'
description: I den här artikeln beskrivs de två klientrollerna och behörigheterna för rollerna.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451901"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Rollbaserad åtkomstkontroll för Service Fabric-klienter
Azure Service Fabric stöder två olika åtkomstkontrolltyper för klienter som är anslutna till ett Service Fabric-kluster: administratör och användare. Med åtkomstkontroll kan klusteradministratören begränsa åtkomsten till vissa klusteråtgärder för olika användargrupper, vilket gör klustret säkrare.  

**Administratörer** har full tillgång till hanteringsfunktioner (inklusive läs-/skrivfunktioner). Som standard har **användarna** bara läsbehörighet till hanteringsfunktioner (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.

Du anger de två klientrollerna (administratör och klient) när klustret skapas genom att tillhandahålla separata certifikat för varje. Mer information om hur du konfigurerar ett säkert Service Fabric-kluster finns i [service fabric-kluster.](service-fabric-cluster-security.md)

## <a name="default-access-control-settings"></a>Standardinställningar för åtkomstkontroll
Administratörsåtkomstkontrolltypen har fullständig åtkomst till alla FabricClient-API:er. Den kan utföra alla läsningar och skrivningar i Service Fabric-klustret, inklusive följande åtgärder:

### <a name="application-and-service-operations"></a>Program- och serviceåtgärder
* **CreateService**: skapa tjänsten                             
* **CreateServiceFromTemplate**: skapa tjänsten från mall                             
* **UpdateService**: tjänstuppdateringar                             
* **DeleteService**: borttagning av tjänsten                             
* **ProvisionApplicationType**: etablering av programtyp                             
* **CreateApplication**: skapande av program                               
* **DeleteApplication**: borttagning av program                             
* **UpgradeApplication**: starta eller avbryta programuppgraderingar                             
* **UnprovisionApplicationType**: programtyp avetablering                             
* **MoveNextUpgradeDomain**: återuppta programuppgraderingar med en explicit uppdateringsdomän                             
* **ReportUpgradeHealth**: återuppta programuppgraderingar med den aktuella uppgraderingen                             
* **ReportHealth**: rapportering hälsa                             
* **PredeployPackageToNode**: API för fördistribution                            
* **CodePackageControl**: starta om kodpaket                             
* **RecoverPartition**: återställa en partition                             
* **RecoverPartitions**: återställa partitioner                             
* **RecoverServicePartitions**: återställa tjänstpartitioner                             
* **RecoverSystemPartitioner**: återställa systemtjänstpartitioner                             

### <a name="cluster-operations"></a>Klusteråtgärder
* **ProvisionFabric**: MSI och/eller klustermanifestetablering                             
* **UpgradeFabric**: starta klusteruppgraderingar                             
* **UnprovisionFabric**: MSI och/eller klustermanifest som inte avetableras                         
* **MoveNextFabricUpgradeDomain**: återuppta klusteruppgraderingar med en explicit uppdateringsdomän                             
* **ReportFabricUpgradeHealth**: återuppta klusteruppgraderingar med den aktuella uppgraderingen                             
* **StartInfrastructureTask:** starta infrastrukturuppgifter                             
* **FinishInfrastructureTask**: slutföra infrastrukturuppgifter                             
* **InvokeInfrastructureCommand**: kommandon för hantering av infrastrukturuppgiftsfunktioner                              
* **ActivateNode**: aktivera en nod                             
* **Inaktiveranod :** inaktivera en nod                             
* **InaktiveraNodesBatch**: inaktivera flera noder                             
* **RemoveNodeDeactivations**: återställa inaktivering på flera noder                             
* **GetNodeActivationStatus**: kontrollera inaktiverande status                             
* **NodeStateRemoved**: rapportnodtillstånd har tagits bort                             
* **ReportFault**: rapportering fel                             
* **FileContent**: image store klientfilöverföring (extern till kluster)                             
* **FileDownload**: image store klientfil nedladdning inledande (extern till kluster)                             
* **InternalList**: åtgärd för klientfilslist för bildarkiv (intern)                             
* **Ta bort**: bildlagringsklientborttagningsåtgärd                              
* **Ladda upp**: bild store klientuppladdning                             
* **NodeControl**: starta, stoppa och starta om noder                             
* **MoveReplicaControl**: flytta repliker från en nod till en annan                             

### <a name="miscellaneous-operations"></a>Diverse operationer
* **Ping:** klient pingar                             
* **Fråga:** alla tillåtna frågor
* **NameExists**: namnge URI existenskontroller                             

Kontrolltypen för användaråtkomst är som standard begränsad till följande åtgärder: 

* **Räkna uppSubnamn**: namnge URI-uppräkning                             
* **UppräkningEgenskaper**: namngivning av egenskapsuppräkning                             
* **PropertyReadBatch**: namngivningsegenskapen läser operationer                             
* **GetServiceDescription**: tjänstmeddelanden för lång omröstning och lästjänstbeskrivningar                             
* **ResolveService**: klagomålsbaserad tjänstlösning                             
* **ResolveNameOwner**: lösa namngivning av URI-ägare                             
* **ResolvePartition**: lösa systemtjänster                             
* **ServiceAnmälningar**: händelsebaserade servicemeddelanden                             
* **GetUpgradeStatus**: uppgraderingsstatus för avsökningsprogram                             
* **GetFabricUpgradeStatus**: avsökningsklusteruppgraderingsstatus                             
* **InvokeInfrastructureQuery**: fråga infrastrukturaktiviteter                             
* **Lista**: åtgärd för klientfilslista för bildarkiv                             
* **ResetPartitionLoad**: återställa inläsningen för en redundansenhet                             
* **ToggleVerboseServicePlacementHealthReporting**: växla utförlig tjänst placering hälsorapportering                             

Administratörsåtkomstkontrollen har också åtkomst till föregående åtgärder.

## <a name="changing-default-settings-for-client-roles"></a>Ändra standardinställningar för klientroller
I klustermanifestfilen kan du ange administratörsfunktioner till klienten om det behövs. Du kan ändra standardinställningarna genom att gå till alternativet **Inställningar för infrastruktur** när [klustret skapas](service-fabric-cluster-creation-via-portal.md)och ange föregående inställningar i fälten **namn,** **administratör,** **användare**och **värde.**

## <a name="next-steps"></a>Nästa steg
[Säkerhet för klustersäkerhet för serviceinfrastruktur](service-fabric-cluster-security.md)

[Skapa service fabric-kluster](service-fabric-cluster-creation-via-portal.md)

