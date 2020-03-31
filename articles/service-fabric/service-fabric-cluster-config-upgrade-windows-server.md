---
title: Uppgradera konfigurationen av ett fristående kluster
description: Lär dig hur du uppgraderar konfigurationen som kör ett fristående Service Fabric-kluster.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610137"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Uppgradera konfigurationen av ett fristående kluster 

För alla moderna system, förmågan att uppgradera är nyckeln till långsiktig framgång för din produkt. Ett Azure Service Fabric-kluster är en resurs som du äger. I den här artikeln beskrivs hur du uppgraderar konfigurationsinställningarna för ditt fristående Service Fabric-kluster.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Anpassa klusterinställningar i filen ClusterConfig.json
Fristående kluster konfigureras via *filen ClusterConfig.json.* Mer information om de olika inställningarna finns i [Konfigurationsinställningar för ett fristående Windows-kluster](service-fabric-cluster-manifest.md).

Du kan lägga till, uppdatera `fabricSettings` eller ta bort inställningar i avsnittet under avsnittet [Klusteregenskaper](./service-fabric-cluster-manifest.md#cluster-properties) i *ClusterConfig.json*. 

Följande JSON lägger till till exempel en ny inställning *Till MaxDiskQuotaInMB* i avsnittet *Diagnostik* under: `fabricSettings`

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

När du har ändrat inställningarna i filen ClusterConfig.json [testar du klusterkonfigurationen](#test-the-cluster-configuration) och uppgraderar sedan [klusterkonfigurationen](#upgrade-the-cluster-configuration) för att tillämpa inställningarna i klustret. 

## <a name="test-the-cluster-configuration"></a>Testa klusterkonfigurationen
Innan du startar konfigurationsuppgraderingen kan du testa den nya klusterkonfigurationen JSON genom att köra följande PowerShell-skript i det fristående paketet:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Eller använd det här skriptet:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Vissa konfigurationer kan inte uppgraderas, till exempel slutpunkter, klusternamn, nod-IP osv. Den nya klusterkonfigurationen JSON testas mot den gamla och genererar fel i PowerShell-fönstret om det finns ett problem.

## <a name="upgrade-the-cluster-configuration"></a>Uppgradera klusterkonfigurationen
Om du vill uppgradera uppgraderingen av klusterkonfigurationen kör [du Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Konfigurationsuppgraderingen bearbetas uppgradera domänen efter uppgraderingsdomän.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Konfiguration av klustercertifikat
Ett klustercertifikat används för autentisering mellan klusternoder. Certifikatförvaltningen bör utföras med extra försiktighet eftersom fel blockerar kommunikationen mellan klusternoder.

Fyra alternativ stöds:  

* Enskild certifikatuppgradering: Uppgraderingssökvägen är Certifikat A (Primär) -> Certifikat B (Primär) -> Certifikat C (Primär) ->....

* Dubbel certifikatuppgradering: Uppgraderingssökvägen är Certifikat A (primär) -> certifikat A (primärt) och B (sekundärt) -> certifikat B (primärt) -> certifikat B (primärt) och C (sekundärt) -> certifikat C (primärt) ->....

* Uppgradering av certifikattyp: Tumavtrycksbaserad certifikatkonfiguration <-> CommonName-baserad certifikatkonfiguration. Till exempel certifikat tumavtryck A (primär) och tumavtryck B (sekundärt) -> Certifikat CommonName C.

* Uppgradering av certifikatutfärdarens tumavtryck: Uppgraderingsvägen är Certificate CN=A,IssuerThumbprint=IT1 (Primär) -> Certificate CN=A,IssuerThumbprint=IT1,IT2 (Primär) -> Certifikat CN=A,IssuerThumbprint=IT2 (Primär).


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar vissa [service fabric-klusterinställningar](service-fabric-cluster-fabric-settings.md).
* Lär dig hur du [skalar klustret in och ut](service-fabric-cluster-scale-up-down.md).
* Läs mer om [programuppgraderingar](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
