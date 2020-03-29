---
title: Lägga till eller ta bort noder i ett fristående Service Fabric-kluster
description: Lär dig hur du lägger till eller tar bort noder i ett Azure Service Fabric-kluster på en fysisk eller virtuell dator som kör Windows Server, som kan vara lokalt eller i vilket moln som helst.
author: dkkapur
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: f9bee35ee8e82070b4cf601139b471562ba5e10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934210"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Lägga till eller ta bort noder i ett fristående Service Fabric-kluster som körs på Windows Server
När du har [skapat ditt fristående Service Fabric-kluster på Windows Server-datorer](service-fabric-cluster-creation-for-windows-server.md)kan dina (affärsbehov) ändras och du måste lägga till eller ta bort noder i klustret. Den här artikeln innehåller detaljerade steg för att uppnå detta. Observera att funktionerna för tillägg/ta bort nod inte stöds i lokala utvecklingskluster.

## <a name="add-nodes-to-your-cluster"></a>Lägga till noder i klustret

1. Förbered den virtuella dator/dator som du vill lägga till i klustret genom att följa stegen i [Planera och förbereda klusterdistributionen för Service Fabric.](service-fabric-cluster-standalone-deployment-preparation.md)

2. Identifiera vilken feldomän och uppgraderingsdomän som du ska lägga till den här virtuella datorn/datorn i.

   Om du använder certifikat för att skydda klustret förväntas certifikat installeras i de lokala certifikatarkiv som förberedelse för att noden ska ansluta till klustret. Analogen gäller när du använder andra former av säkerhet.

3. Fjärrskrivbord (RDP) till den virtuella dator/dator som du vill lägga till i klustret.

4. Kopiera eller [hämta det fristående paketet för Service Fabric för Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) till den virtuella datorn/datorn och packa upp paketet.

5. Kör PowerShell med förhöjda privilegier och gå till platsen för det uppackade paketet.

6. Kör *addNode.ps1-skriptet* med de parametrar som beskriver den nya noden att lägga till. I följande exempel läggs en ny nod med namnet VM5 till UD1 och fd:/dc1/r0. `ExistingClusterConnectionEndPoint`är en anslutningsslutpunkt för en nod som redan finns i det befintliga klustret, vilket kan vara IP-adressen *för en* nod i klustret. 

   Osäker (prototyper):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Säker (certifikatbaserad):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   När skriptet körs kan du kontrollera om den nya noden har lagts till genom att köra cmdleten [Get-ServiceFabricNode.](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps)

7. Om du vill säkerställa konsekvens mellan olika noder i klustret måste du initiera en konfigurationsuppgradering. Kör [Get-ServiceFabricClusterKonfigurering](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) för att hämta den senaste konfigurationsfilen och lägg till den nyligen tillagda noden i avsnittet "Noder". Vi rekommenderar också att alltid ha den senaste klusterkonfigurationen tillgänglig om du behöver distribuera om ett kluster som har samma konfiguration.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Kör [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) för att påbörja uppgraderingen.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Alternativt kan du köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Lägga till noder i kluster som konfigurerats med Windows Security med gMSA
För kluster som konfigurerats med Grupphanterathttps://technet.microsoft.com/library/hh831782.aspx)tjänstkonto(gMSA)( kan en ny nod läggas till med hjälp av en konfigurationsuppgradering:
1. Kör [Get-ServiceFabricClusterKonfigurering](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) på någon av de befintliga noderna för att hämta den senaste konfigurationsfilen och lägga till information om den nya noden som du vill lägga till i avsnittet "Noder". Kontrollera att den nya noden ingår i samma grupphanterade konto. Det här kontot bör vara administratör på alla datorer.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Kör [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) för att påbörja uppgraderingen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Alternativt kan du köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Lägga till nodtyper i klustret
Om du vill lägga till en ny nodtyp ändrar du konfigurationen så att den nya nodtypen inkluderas i avsnittet "NodeTypes" under "Egenskaper" och påbörja en konfigurationsuppgradering med [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). När uppgraderingen är klar kan du lägga till nya noder i klustret med den här nodtypen.

## <a name="remove-nodes-from-your-cluster"></a>Ta bort noder från klustret
En nod kan tas bort från ett kluster med hjälp av en konfigurationsuppgradering på följande sätt:

1. Kör [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) för att hämta den senaste konfigurationsfilen och *ta bort* noden från avsnittet "Noder".
Lägg till parametern "NodesToRemoved" i avsnittet "Setup" i avsnittet "FabricSettings". "Värdet" ska vara en kommaavgränsad lista med nodnamn på noder som måste tas bort.

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
2. Kör [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) för att påbörja uppgraderingen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer. Alternativt kan du köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

> [!NOTE]
> Borttagning av noder kan initiera flera uppgraderingar. Vissa noder markeras med `IsSeedNode=”true”` taggen och kan identifieras `Get-ServiceFabricClusterManifest`genom att fråga klustermanifestet med . Det kan ta längre tid än andra att ta bort sådana noder, eftersom utsädesnoderna måste flyttas i sådana scenarier. Klustret måste underhålla minst 3 primära nodtypnoder.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Ta bort nodtyper från klustret
Innan du tar bort en nodtyp dubbelkolla om det finns några noder som refererar till nodtypen. Ta bort dessa noder innan du tar bort motsvarande nodtyp. När alla motsvarande noder har tagits bort kan du ta bort NodeType från klusterkonfigurationen och påbörja en konfigurationsuppgradering med [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Ersätta primära noder i klustret
Byte av primära noder bör utföras en nod efter den andra, i stället för att ta bort och sedan lägga till i batchar.


## <a name="next-steps"></a>Nästa steg
* [Konfigurationsinställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md)
* [Skydda ett fristående kluster i Windows med X509-certifikat](service-fabric-windows-cluster-x509-security.md)
* [Skapa ett fristående Service Fabric-kluster med virtuella Azure-datorer som kör Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

