---
title: Uppgradera ett fristående Azure-tjänstkoncettkluster
description: Lär dig mer om hur du uppgraderar versionen eller konfigurationen av ett fristående Azure Service Fabric-kluster.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 6da9b4c6890895141ecc419382f05f667614fb31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451827"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Uppgradera och uppdatera ett fristående service fabric-kluster

För alla moderna system, design för uppgradering är nyckeln till att uppnå långsiktig framgång för din produkt. Ett fristående Azure Service Fabric-kluster är en resurs som du äger. I den här artikeln beskrivs vad som kan uppgraderas eller uppdateras.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Styra tygversionen som körs i klustret
Kontrollera att klustret alltid kör en [Service Fabric-version som stöds](service-fabric-versions.md). När Microsoft meddelar att en ny version av Service Fabric släpps är den tidigare versionen markerad för supportens efter minst 60 dagar från datumet för tillkännagivandet. Nya utgåvor tillkännages [på Service Fabric team blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är tillgänglig att välja vid den tidpunkten.

Du kan ange att klustret ska ta emot automatiska infrastrukturuppgraderingar när de släpps av Microsoft eller så kan du manuellt välja en tygversion som stöds som du vill att klustret ska vara på. Mer information finns [i Uppgradera service fabric-versionen som körs i klustret](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Anpassa konfigurationsinställningar

Många olika [konfigurationsinställningar](service-fabric-cluster-manifest.md) kan anges i *filen ClusterConfig.json,* till exempel tillförlitlighetsnivån för kluster- och nodegenskaperna.  Mer information finns [i Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).  Många andra, mer avancerade, inställningar kan också anpassas.  Mer information finns i [Inställningarna för Klusterinfrastruktur för Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definiera nodegenskaper
Ibland kanske du vill se till att vissa arbetsbelastningar körs endast på vissa typer av noder i klustret. Vissa arbetsbelastningar kan till exempel kräva GPU:er eller SSD-enheter medan andra kanske inte gör det. För var och en av nodtyperna i ett kluster kan du lägga till anpassade nodegenskaper i klusternoder. Placeringsbegränsningar är de uttryck som är kopplade till enskilda tjänster som väljer för en eller flera nodegenskaper. Placeringsbegränsningar definierar var tjänster ska köras.

Mer information om hur du använder placeringsbegränsningar, nodegenskaper och hur du definierar dem läser du [nodegenskaper och placeringsbegränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Lägga till kapacitetsmått
För var och en av nodtyperna kan du lägga till anpassade kapacitetsmått som du vill använda i dina program för att rapportera inläsning. Mer information om hur du använder kapacitetsmått för att rapportera inläsning finns i Service Fabric Cluster Resource Manager-dokumenten för [att beskriva ditt kluster](service-fabric-cluster-resource-manager-cluster-description.md) och mått och läs [in](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Korrigering av operativsystemet i klusternoderna
PA (Patch Orchestration Application) är ett Service Fabric-program som automatiserar operativsystemkorrigering på ett Service Fabric-kluster utan driftstopp. [Patch Orchestration Application for Windows](service-fabric-patch-orchestration-application.md) kan distribueras i klustret för att installera korrigeringar på ett orkestrerat sätt samtidigt som tjänsterna är tillgängliga hela tiden. 


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några av inställningarna för [klusterinfrastruktur för serviceinfrastruktur](service-fabric-cluster-fabric-settings.md)
* Lär dig hur du [skalar klustret in och ut](service-fabric-cluster-scale-up-down.md)
* Läs mer om [programuppgraderingar](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
