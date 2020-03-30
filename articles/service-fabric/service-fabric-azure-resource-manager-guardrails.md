---
title: Service Fabric Azure Resource Manager-distributionsskydd
description: Den här artikeln innehåller en översikt över vanliga misstag som begåtts vid distribution av ett Service Fabric-kluster via Azure Resource Manager och hur du undviker dem.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368577"
---
# <a name="service-fabric-guardrails"></a>Service Tyg skyddsräcken 
När du distribuerar ett Service Fabric-kluster införs skyddsräcken, vilket misslyckas med en Azure Resource Manager-distribution vid ogiltig klusterkonfiguration. Följande avsnitt innehåller en översikt över vanliga problem med klusterkonfiguration och de steg som krävs för att minska dessa problem. 

## <a name="durability-mismatch"></a>Hållbarhet obalans
### <a name="overview"></a>Översikt
Hållbarhetsvärdet för en nodtyp för Tjänst fabric definieras i två olika avsnitt i en Azure Resource Manager-mall. Avsnittet Skala uppsättning för virtuell dator i skalauppsättningsresursen för den virtuella datorn och avsnittet Nodtyp i klusterresursen Service Fabric. Det är ett krav att hållbarhetsvärdet i dessa avsnitt matchar, annars misslyckas resursdistributionen.

Följande avsnitt innehåller ett exempel på en hållbarhetsfelmatchning mellan inställningen för förlängning av tilläggstillägg för virtuell dator och inställningen för serveringsnodnodtyp:  

**Hållbarhetsinställning för skalningsuppsättning för virtuell dator**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Hållbarhetsinställning för service fabric-nodtyp** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Felmeddelanden
* Hållbarhetsmatchning av prestanda för virtuell dators skalning matchar inte den aktuella hållbarhetsnivån för service fabric-nodtyp
* Hållbarhet för skalningsuppsättning för virtuell dator matchar inte hållbarhetsnivån för måltjänstinfrastrukturnodtyp
* Hållbarheten för skalningsuppsättning för virtuell dator matchar den aktuella hållbarhetsnivån för serviceinfrastruktur eller varaktighetsnivån för måltjänstinfrastrukturnodtyp 

### <a name="mitigation"></a>Åtgärd
Så här åtgärdar du en felmatchning av varaktigheten, vilket indikeras av något av ovanstående felmeddelanden:
1. Uppdatera hållbarhetsnivån i tillägget Skaladuppsättning för virtuell dator eller avsnittet Service Fabric Node Type i Azure Resource Manager-mallen för att säkerställa att värdena matchar.
2. Distribuera om Azure Resource Manager-mallen med de uppdaterade värdena.


## <a name="seed-node-deletion"></a>Borttagning av seed-nod 
### <a name="overview"></a>Översikt
Ett Service Fabric-kluster har en [egenskap för tillförlitlighetsnivå](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) som används för att fastställa antalet repliker av systemtjänster som körs på klustrets primära nodtyp. Antalet nödvändiga repliker avgör det minsta antalet noder som måste underhållas i klustrets primära nodtyp. Om antalet noder i den primära nodtypen går under det minimum som krävs för tillförlitlighetsnivån, blir klustret instabilt.  

### <a name="error-messages"></a>Felmeddelanden 
Borttagning av seed-nod har upptäckts och kommer att avvisas. 
* Den här åtgärden {0} skulle resultera i endast potentiella {1} frönoder att stanna kvar i klustret, medan det behövs som ett minimum.
* Om {0} du tar {1} bort seed-noder från skulle det leda till att klustret går nedåt på grund av förlust av kvorum för utsädesnoder. Maximalt antal frönoder som kan tas {2}bort åt gången är .
 
### <a name="mitigation"></a>Åtgärd 
Kontrollera att din primära nodtyp har tillräckligt med virtuella datorer för den tillförlitlighet som anges i klustret. Du kommer inte att kunna ta bort en virtuell dator om den kommer att föra den virtuella datorns skaluppsättning under det minsta antalet noder för den givna tillförlitlighetsnivån.
* Om tillförlitlighetsnivån har angetts korrekt kontrollerar du att du har tillräckligt med noder i den primära nodtypen som behövs för tillförlitlighetsnivån. 
* Om tillförlitlighetsnivån är felaktig initierar du en ändring på resursen Service Fabric för att sänka tillförlitlighetsnivån först innan du initierar några åtgärder för skalningsuppsättning för virtuella datorer och väntar på att den ska slutföras.
* Om tillförlitlighetsnivån är Brons följer du dessa [steg](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) för att skala ned klustret på ett smidigt sätt.

## <a name="next-steps"></a>Nästa steg
* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa service fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Felsöka service fabric: [felsökningsguider](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
