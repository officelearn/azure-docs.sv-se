---
title: Uppgradera en fristående Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig mer om hur du uppgraderar versionen eller konfiguration av ett fristående Azure Service Fabric-kluster.  T
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: ed50f49b110d910a298e608f3625a3bddfe8a0a8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963174"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Uppgradera och uppdatera fristående Service Fabric-kluster

Utformning av möjligheterna är avgörande för att uppnå långsiktig framgång av din produkt för alla moderna system. Ett fristående Azure Service Fabric-kluster är en resurs som du äger. Den här artikeln beskriver vad kan uppgraderas eller uppdateras.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kontrollera den fabric-versionen som körs på klustret
Kontrollera att klustret körs alltid en [Service Fabric-version som stöds](service-fabric-versions.md). När Microsoft presenterar lanseringen av en ny version av Service Fabric, markeras den tidigare versionen för support upphör när du har minst 60 dagar från datumet då meddelandet. Nya versioner tillkännages [på Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/). Den nya versionen är kan väljas vid den tidpunkten.

Du kan ställa in klustret för att ta emot automatiska infrastrukturuppgraderingar som Microsoft släpper eller manuellt kan du välja en stöds fabric-versionen som du vill att klustret ska vara på. Mer information finns i [uppgradera Service Fabric-versionen som körs på klustret](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Anpassa konfigurationsinställningar

Många olika [konfigurationsinställningar](service-fabric-cluster-manifest.md) kan anges i den *ClusterConfig.json* fil, t.ex tillförlitlighetsnivån för egenskaperna kluster och nod.  Mer information, [uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).  Många andra, mer avancerade inställningar kan också anpassas.  Mer information finns i [infrastrukturinställningarna för Service Fabric-kluster](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definiera nodegenskaper
Ibland kanske du vill se till att vissa arbetsbelastningar endast för vissa typer av noder i klustret. Exempelvis kan vissa arbetsbelastning kräva GPU: er eller SSD-enheter och andra inte. För varje nod i ett kluster, kan du lägga till anpassade nodegenskaper klusternoder. Placeringsbegränsningar är de instruktioner som är kopplade till enskilda tjänster väljer för en eller flera egenskaper för noden. Placeringsbegränsningar definiera där tjänsterna ska köras.

Mer information om användning av placeringsbegränsningar nodegenskaper och hur du definierar dem finns [nodegenskaper och placeringsbegränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Lägg till kapacitet
Du kan lägga till anpassade kapacitetsmått som du vill använda i dina program att rapportera belastning för var och en nodtyperna. Information om användning av kapacitet att rapportera belastning, i Service Fabric Cluster Resource Manager-dokument på [som beskriver ditt kluster](service-fabric-cluster-resource-manager-cluster-description.md) och [mått och Load](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Uppdatera Operativsystemet på klusternoderna
Patch orchestration application (POA) är ett Service Fabric-program som automatiserar operativsystemet uppdatering i Service Fabric-kluster utan avbrott. Den [Patch Orchestration Application för Windows](service-fabric-patch-orchestration-application.md) kan distribueras på klustret för att installera uppdateringar på ett dirigerat sätt samtidigt som tjänsterna som är tillgänglig hela tiden. 


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några av de [service fabric-kluster fabric-inställningar](service-fabric-cluster-fabric-settings.md)
* Lär dig hur du [skala ditt kluster in och ut](service-fabric-cluster-scale-up-down.md)
* Lär dig mer om [programuppgraderingar](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
