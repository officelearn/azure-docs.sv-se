---
title: Lägga till eller ta bort noder till ett fristående Service Fabric-kluster | Microsoft Docs
description: Lär dig mer om att lägga till eller ta bort noder till ett Azure Service Fabric-kluster på en fysisk eller virtuell dator som kör Windows Server, som kan vara lokala eller i ett moln.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 69680331bdad0faa36cb3df6117baf8b358da132
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251027"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Lägga till eller ta bort noder till fristående Service Fabric-kluster som körs på Windows Server
När du har [skapade ditt fristående Service Fabric-kluster på Windows Server-datorer](service-fabric-cluster-creation-for-windows-server.md), behoven (företag) kan ändras och du måste lägga till eller ta bort noder i klustret. Den här artikeln innehåller detaljerade anvisningar för att uppnå detta. Observera att lägga till/ta bort noden funktionen inte stöds i kluster för lokal utveckling.

## <a name="add-nodes-to-your-cluster"></a>Lägga till noder i klustret

1. Förbered virtuell dator/dator som du vill lägga till i ditt kluster genom att följa anvisningarna i [planera och förbereda distributionen av Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)
2. Identifiera vilka feldomän och uppgraderingsdomän som du ska lägga till den virtuella datorn/datorn
3. Fjärrskrivbord (RDP) till den virtuella datorn/datorn som du vill lägga till i klustret
4. Kopiera eller [ladda ned det fristående paketet för Service Fabric för Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) till den virtuella datorn/datorn och packa upp paketet
5. Kör Powershell med förhöjd behörighet och gå till platsen för paketets uppzippade
6. Kör den *AddNode.ps1* skript med parametrar som beskriver den nya noden att lägga till. Exemplet nedan lägger till en ny nod kallas VM5, med typen NodeType0 och IP-182.17.34.52, i UD1 och fd: / dc1/r0. Den *ExistingClusterConnectionEndPoint* är en anslutningsslutpunkt för en nod redan i det befintliga klustret, vilket kan vara IP-adressen för *alla* nod i klustret.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    När skriptet är klar kan du kontrollera om den nya noden har lagts till genom att köra den [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet.

7. Om du vill garantera konsekvensen mellan olika noder i klustret, måste du påbörja en uppgradering av konfiguration. Kör [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) få den senaste konfigurationsfilen och lägga till den nyligen tillagda noden till ”noder”-avsnitt. Vi rekommenderar också att alltid har den senaste klusterkonfigurationen om att du behöver redploy ett kluster med samma konfiguration.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Kör [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) så påbörjas uppgraderingen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Du kan också köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Lägga till noder i kluster som har konfigurerats med Windows-säkerhet som använder gMSA
För kluster som har konfigurerats med gruppen Managed Service Account(gMSA) (https://technet.microsoft.com/library/hh831782.aspx), en ny nod kan läggas till med hjälp av en uppgradering av konfiguration:
1. Kör [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) på någon av de befintliga noderna få den senaste konfigurationsfilen och lägga till information om den nya noden som du vill lägga till i avsnittet ”noder”. Kontrollera att den nya noden är en del av samma hanteras gruppkonto. Det här kontot ska vara administratör på alla datorer.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Kör [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) så påbörjas uppgraderingen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Du kan också köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Lägg till nodtyperna i klustret
Ändra din konfiguration för att inkludera den nya nodtypen i ”NodeTypes” avsnittet under ”egenskaper” och börja en konfiguration för att lägga till en ny nodtyp, uppgradera med [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). När uppgraderingen är klar kan du lägga till nya noder i klustret med den här nodtypen.

## <a name="remove-nodes-from-your-cluster"></a>Ta bort noder från klustret
En nod tas bort från ett kluster med en konfiguration uppgradering på följande sätt:

1. Kör [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) att hämta den senaste konfigurationsfilen och *ta bort* noden från ”noder”-avsnittet.
Lägga till parametern ”NodesToBeRemoved” till ”Setup”-avsnitt inuti avsnittet ”FabricSettings”. ”Value” ska vara en kommaavgränsad lista över nodnamn noder som måste tas bort.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Kör [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) så påbörjas uppgraderingen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Du kan också köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Borttagning av noder kan starta flera uppgraderingar. Vissa noder har markerats med `IsSeedNode=”true”` tagga och kan identifieras genom att fråga klustret manifest med `Get-ServiceFabricClusterManifest`. Borttagning av dessa noder kan ta längre tid än andra eftersom startvärdesnoder måste flyttas i sådana scenarier. Klustret måste ha minst 3 noder för typ av primära noden.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Ta bort noden från klustret
Innan du tar bort en nodtyp, dubbelkolla om det finns några noder som refererar till nodtyp. Ta bort dessa noder innan du tar bort motsvarande nodtyp. När alla motsvarande noder har tagits bort kan du ta bort NodeType från klusterkonfigurationen och börja en konfiguration uppgradera med [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Ersätt primära noder i klustret
Ersättning av primära noder måste vara utförs en nod efter en annan, i stället för att ta bort och sedan lägga till i batchar.


## <a name="next-steps"></a>Nästa steg
* [Konfigurationsinställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md)
* [Skydda ett fristående kluster i Windows med hjälp av X509 certifikat](service-fabric-windows-cluster-x509-security.md)
* [Skapa ett fristående Service Fabric-kluster med Azure virtuella datorer som kör Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

