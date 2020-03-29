---
title: Ta bort en nodtyp i Azure Service Fabric | Microsoft-dokument
description: Lär dig hur du tar bort en nodtyp från ett Service Fabric-kluster som körs i Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969408"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Ta bort en nodtyp för Service Fabric
I den här artikeln beskrivs hur du skalar ett Azure Service Fabric-kluster genom att ta bort en befintlig nodtyp från ett kluster. Ett Service Fabric-kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras till. En dator eller virtuell dator som ingår i ett kluster kallas en nod. Skala uppsättningar för virtuella datorer är en Azure-beräkningsresurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster [ställs in som en separat skalningsuppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala ett kluster vågrätt genom att ta bort en nodtyp (skaluppsättning för virtuell dator) och alla dess noder.  Du kan skala klustret när som helst, även när arbetsbelastningar körs i klustret.  När klustret skalas skalas även dina program automatiskt.

> [!WARNING]
> Det rekommenderas inte att använda den här metoden för att ta bort en nodtyp från ett produktionskluster. Det är ett farligt kommando eftersom det tar bort den virtuella datorn skala uppsättning resurs bakom nodtypen. 

## <a name="durability-characteristics"></a>Hållbarhet egenskaper
Säkerhet prioriteras framför hastighet när du använder Remove-AzServiceFabricNodeType. Nodtypen måste vara [hållbarhetsnivå](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)för silver eller guld , eftersom:
- Brons ger dig inga garantier för att spara tillståndsinformation.
- Silver- och guldhållbarhet fångar alla ändringar i skalningsuppsättningen.
- Guld ger dig också kontroll över Azure-uppdateringar under skalningsuppsättningen.

Service Fabric "orkestrerar" underliggande ändringar och uppdateringar så att data inte går förlorade. Men när du tar bort en nodtyp med brons hållbarhet, kan du förlora tillståndsinformation. Om du tar bort en primär nodtyp och ditt program är tillståndslöst är Brons acceptabelt. När du kör tillståndskänsliga arbetsbelastningar i produktionen bör den minsta konfigurationen vara Silver. På samma sätt bör den primära nodtypen alltid vara Silver eller Guld för produktionsscenarier.

### <a name="more-about-bronze-durability"></a>Mer om Brons hållbarhet

När du tar bort en nodtyp som är Brons skrivs alla noder i nodtypen ned omedelbart. Service Fabric inte fånga några bronsnoder skala uppsättning uppdateringar, så alla virtuella datorer gå ner omedelbart. Om du hade något tillståndskänsligt på dessa noder, går data förlorade. Nu, även om du var tillståndslös, alla noder i Service Fabric delta i ringen, så en hel stadsdel kan gå förlorade, vilket kan destabilisera klustret själv.

## <a name="remove-a-node-type"></a>Ta bort en nodtyp

1. Ta hand om denna förutsättning innan du påbörjar processen.

    - Klustret är felfritt.
    - Det kommer fortfarande att finnas tillräcklig kapacitet efter nodtypen tas bort, t.ex. antal noder för att placera obligatoriskt replikantal.

2. Flytta alla tjänster som har placeringsbegränsningar för att använda nodtypen från nodtypen.

    - Ändra program-/tjänstmanifest om du inte längre refererar till nodtypen.
    - Distribuera ändringen.

    Sedan validera att:
    - Alla tjänster som har ändrats ovan körs inte längre på noden som tillhör nodtypen.
    - Alla tjänster är friska.

3. Avmarkera nodtypen som icke-primär (Hoppa över för icke-primära nodtyper)

    - Leta reda på Azure Resource Manager-mallen som används för distribution.
    - Leta reda på avsnittet som är relaterat till nodtypen i avsnittet Service fabric.
    - Ändra isPrimary egenskap till falskt. ** Ta inte bort avsnittet som är relaterat till nodtypen i den här aktiviteten.
    - Distribuera den ändrade Azure Resource Manager-mallen. ** Beroende på klusterkonfigurationen kan det här steget ta ett tag.
    
    Sedan validera att:
    - Service Fabric-sektionen i Portalen anger att klustret är klart.
    - Klustret är felfritt.
    - Ingen av noderna som tillhör nodtypen är markerad som seed-nod.

4. Inaktivera data för nodtypen.

    Anslut till klustret med PowerShell och kör sedan följande steg.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - För brons hållbarhet, vänta på alla noder för att komma till funktionshindrade tillstånd
    - För silver och guld hållbarhet, kommer vissa noder gå in till funktionshindrade och resten kommer att vara i invalidiserande tillstånd. Kontrollera informationsfliken för noderna i inaktiverat tillstånd, om de alla har fastnat på att säkerställa kvorum för infrastrukturtjänstpartitioner, är det säkert att fortsätta.

5. Stoppa data för nodtypen.

    Anslut till klustret med PowerShell och kör sedan följande steg.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Vänta tills alla noder för nodtyp är markerade Ned.
    
6. Ta bort data för nodtypen.

    Anslut till klustret med PowerShell och kör sedan följande steg.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Vänta tills alla noder tas bort från klustret. Noderna ska inte visas i SFX.

7. Ta bort nodtyp från avsnittet Service Fabric.

    - Leta reda på Azure Resource Manager-mallen som används för distribution.
    - Leta reda på avsnittet som är relaterat till nodtypen i avsnittet Service fabric.
    - Ta bort avsnittet som motsvarar nodtypen.
    - Endast för Silver och kluster med högre hållbarhet, uppdatera klusterresursen i mallen och konfigurera `applicationDeltaHealthPolicies` hälsoprinciper `properties` för att ignorera fabric:/System-programmets hälsotillstånd genom att lägga till under klusterresurs enligt nedan. Nedanstående princip bör ignorera befintliga fel men inte tillåta nya hälsofel. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Distribuera den ändrade Azure Resource Manager-mallen. ** Det här steget tar ett tag, vanligtvis upp till två timmar. Den här uppgraderingen ändrar inställningarna till InfrastructureService, därför behövs en omstart av nod. I det `forceRestart` här fallet ignoreras. 
    Parametern `upgradeReplicaSetCheckTimeout` anger den maximala tid som Service Fabric väntar på att en partition ska vara i ett säkert tillstånd, om inte redan i ett säkert tillstånd. När säkerhetskontrollerna har godkänts för alla partitioner på en nod fortsätter Service Fabric med uppgraderingen på den noden.
    Värdet för parametern `upgradeTimeout` kan minskas till 6 timmar, men för maximal säkerhet bör 12 timmar användas.

    Sedan validera att:
    - Service Fabric Resource i portalen visar klar.

8. Ta bort alla referenser till resurserna som är relaterade till nodtypen.

    - Leta reda på Azure Resource Manager-mallen som används för distribution.
    - Ta bort skaluppsättningen för den virtuella datorn och andra resurser som är relaterade till nodtypen från mallen.
    - Distribuera ändringarna.

    Sedan:
    - Vänta tills distributionen är klar.

## <a name="next-steps"></a>Nästa steg
- Läs mer om egenskaper [för klusterhållbarhet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Läs mer om [nodtyper och skalningsuppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md).
- Läs mer om [skalning av Klusterskalning för Service Fabric](service-fabric-cluster-scaling.md).
