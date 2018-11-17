---
title: Uppgradera konfigurationen av ett fristående Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig hur du uppgraderar den konfiguration som kör ett fristående Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 88846845f1f8ffc71fb193e134a18ec38f619141
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855184"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Uppgradera konfigurationen av ett fristående kluster 

Möjlighet att uppgradera är nyckeln till din produkt långsiktig framgång för alla moderna system. Ett Azure Service Fabric-kluster är en resurs som du äger. Den här artikeln beskrivs hur du uppgraderar konfigurationsinställningarna för fristående Service Fabric-klustret.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Anpassa inställningar för klustret i filen ClusterConfig.json
Fristående kluster konfigureras via den *ClusterConfig.json* fil. Läs mer om de olika inställningarna i [konfigurationsinställningarna för ett fristående Windows-kluster](service-fabric-cluster-manifest.md).

Du kan lägga till, uppdatera eller ta bort inställningarna i den `fabricSettings` avsnittet den [Klusteregenskaper](./service-fabric-cluster-manifest.md#cluster-properties) i avsnittet *ClusterConfig.json*. 

Till exempel följande JSON lägger till en ny inställning *MaxDiskQuotaInMB* till den *diagnostik* avsnittet `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

När du har ändrat inställningarna i filen ClusterConfig.json [testa klusterkonfigurationen](#test-the-cluster-configuration) och sedan [uppgradera klusterkonfigurationen](#upgrade-the-cluster-configuration) att tillämpa inställningarna i klustret. 

## <a name="test-the-cluster-configuration"></a>Testa klusterkonfigurationen
Innan du startar uppgraderingen konfiguration kan du testa nya klusterkonfigurationen JSON genom att köra följande PowerShell-skript i det fristående paketet:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Eller Använd det här skriptet:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Vissa konfigurationer kan inte uppgraderas, till exempel slutpunkter, klusternamnet och nod-IP, osv. Den nya klusterkonfigurationen JSON har testats mot den gamla servern och genererar fel i PowerShell-fönstret om det uppstår problem.

## <a name="upgrade-the-cluster-configuration"></a>Uppgradera klusterkonfigurationen
Om du vill uppgradera configuration klusteruppgraderingen kör [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Konfiguration av uppgraderingen är bearbetade uppgraderingsdomän per uppgraderingsdomän.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Uppgradera kluster Certifikatkonfiguration
Ett klustercertifikat används för autentisering mellan klusternoderna. Förnya certifikatet ska utföras med extra försiktig eftersom fel blockerar kommunikation mellan klusternoder.

Fyra alternativen stöds:  

* Enkel certifikatuppgraderingen: sökvägen för uppgraderingen är certifikat (primär) -> certifikatet B (primär) -> certifikatet C (primär) ->...

* Dubbla certifikatuppgraderingen: sökvägen för uppgraderingen är certifikat (primär) -> certifikat (primär) och B (sekundär) -> certifikatet B (primär) > certifikatet B (primär) och C (sekundär) -> certifikatet C (primär) ->...

* Av Typuppgradering av certifikat: tumavtryck för certifikat configuration <>--Vanligtnamn-baserade Certifikatkonfiguration. Till exempel certifikatets tumavtryck (primär) och tumavtryck B (sekundär) -> certifikatet CommonName C.

* Utfärdaren tumavtryck för uppgradering av certifikat: sökvägen för uppgraderingen är certifikat-CN = A, IssuerThumbprint = IT1 (primär) certifikat-CN -> = A, IssuerThumbprint = IT1 IT2 (primär) certifikat-CN -> = A, IssuerThumbprint = IT2 (primär).


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några [inställningar för Service Fabric-klustret](service-fabric-cluster-fabric-settings.md).
* Lär dig hur du [skala ditt kluster in och ut](service-fabric-cluster-scale-up-down.md).
* Lär dig mer om [programuppgraderingar](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
