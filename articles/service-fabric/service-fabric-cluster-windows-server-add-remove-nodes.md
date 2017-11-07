---
title: "Lägg till eller ta bort noder i ett fristående Service Fabric-kluster | Microsoft Docs"
description: "Lär dig mer om att lägga till eller ta bort noder till ett Azure Service Fabric-kluster på en fysisk eller virtuell dator som kör Windows Server, som kan vara lokalt eller i något moln."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 252dcdf0ff9e1fecd6665808bfe7978a4417018b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Lägg till eller ta bort noder i ett fristående Service Fabric-kluster som körs på Windows Server
När du har [skapa fristående Service Fabric-klustret på Windows Server-datorer](service-fabric-cluster-creation-for-windows-server.md), () affärsbehoven ändras och du behöver lägga till eller ta bort noder i klustret. Den här artikeln innehåller detaljerade anvisningar för att åstadkomma detta. Observera att lägga till/ta bort noden funktionen inte stöds i kluster för lokal utveckling.

## <a name="add-nodes-to-your-cluster"></a>Lägg till noder i klustret

1. Förbered den VM/datorn som du vill lägga till i klustret genom att följa anvisningarna i [planera och förbereda distributionen av Service Fabric-kluster](service-fabric-cluster-creation-for-windows-server.md)
2. Identifiera vilka feldomän och du ska lägga till den Virtuella/datorn till domänen
3. Fjärrskrivbord (RDP) till den Virtuella/datorn som du vill lägga till i klustret
4. Kopiera eller [hämta fristående paketet för Service Fabric för Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) till VM/datorn och packa upp paketet
5. Kör Powershell med förhöjd behörighet och gå till platsen för paketets uppackade
6. Kör den *AddNode.ps1* skript med de parametrar som beskriver den nya noden för att lägga till. Exemplet nedan lägger till en ny nod med namnet VM5, med typen NodeType0 och IP-adress 182.17.34.52, i UD1 och fd: / dc1/r0. Den *ExistingClusterConnectionEndPoint* är en Anslutningens slutpunkt för en nod som redan finns i det befintliga klustret som kan vara IP-adressen för *alla* nod i klustret.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    När skriptet är klar kan du kontrollera om den nya noden har lagts till genom att köra den [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet.

7. För att säkerställa konsekvens mellan olika noder i klustret måste du påbörja en uppgradering av konfiguration. Kör [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) Hämta senaste konfigurationsfilen och lägga till den nya noden i avsnittet ”noder”. Det rekommenderas också att alltid har den senaste klusterkonfigurationen i det fallet behöver du redploy ett kluster med samma konfiguration.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Kör [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) att påbörja uppgraderingen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Du kan också köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Lägg till noder i kluster som har konfigurerats med Windows-säkerhet som använder gMSA
För kluster som har konfigurerats med gruppen hanteras Service Account(gMSA) (https://technet.microsoft.com/library/hh831782.aspx), kan en ny nod läggas till med hjälp av en uppgradering av konfiguration:
1. Kör [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) på någon av de befintliga noderna få den senaste konfigurationsfilen och lägga till information om den nya noden som du vill lägga till i avsnittet ”noder”. Kontrollera att den nya noden är en del av samma hanteras gruppkontot. Det här kontot måste vara administratör på alla datorer.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Kör [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) att påbörja uppgraderingen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Du kan också köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Lägg till nodtyper i klustret
Ändra konfigurationen för att inkludera den nya nodtypen i ”nodetypes får” avsnittet under ”egenskaper” och börja en konfiguration för att lägga till en ny nod, uppgradera med hjälp av [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). När uppgraderingen är klar kan du lägga till nya noder i klustret med den här nodtypen.

## <a name="remove-nodes-from-your-cluster"></a>Ta bort noder från klustret
En nod kan tas bort från ett kluster med en konfiguration uppgradering på följande sätt:

1. Kör [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) att hämta den senaste konfigurationsfilen och *ta bort* noden från ”noder” i avsnittet.
Lägga till parametern ”NodesToBeRemoved” till ”inställningar” avsnitt i avsnittet ”FabricSettings”. ”Värde” ska vara en kommaavgränsad lista över nodnamn med noder som måste tas bort.

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
2. Kör [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) att påbörja uppgraderingen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Du kan också köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Borttagning av noder kan starta flera uppgraderingar. Vissa noder har markerats med `IsSeedNode=”true”` tagga och kan identifieras genom att fråga klustret manifest med `Get-ServiceFabricClusterManifest`. Borttagning av dessa noder kan ta längre tid än andra eftersom seed-noder måste flyttas i dessa scenarier. Klustret måste upprätthålla minst 3 primära noden typen noder.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Ta bort nod från klustret
Innan du tar bort en nodtyp, kontrollera om det finns några noder som refererar till nodtypen. Ta bort dessa noder innan du tar bort motsvarande nodtypen. När alla motsvarande noder har tagits bort kan du ta bort NodeType från klusterkonfigurationen och börja en konfiguration uppgradera med hjälp av [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Ersätt primära noder i klustret
Ersättning av primära noder måste vara utförs en nod efter en annan, i stället för att ta bort och sedan lägga till i batchar.


## <a name="next-steps"></a>Nästa steg
* [Konfigurationsinställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md)
* [Skydda ett fristående kluster på Windows med X509 certifikat](service-fabric-windows-cluster-x509-security.md)
* [Skapa ett fristående Service Fabric-kluster med Azure virtuella datorer som kör Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

