---
title: Service Fabric Azure Resource Manager distribution guardrails | Microsoft Docs
description: Den här artikeln innehåller en översikt över vanliga fel som görs när du distribuerar ett Service Fabric kluster via Azure Resource Manager och hur du undviker dem.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: gamonroy
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: 3ea6f850685a695644cfc3073fc939a58901658c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828635"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
När du distribuerar ett Service Fabric-kluster placeras guardrails på plats, vilket kommer att Miss förAzure Resource Manager distribution om en ogiltig kluster konfiguration används. I följande avsnitt får du en översikt över vanliga problem med kluster konfiguration och de steg som krävs för att minimera problemen. 

## <a name="durability-mismatch"></a>Hållbarhets konflikt
### <a name="overview"></a>Översikt
Hållbarhets värdet för en Service Fabric nodtyp definieras i två olika avsnitt i en Azure Resource Manager mall. Avsnittet tillägg för skalnings uppsättning för virtuell dator i den virtuella datorns skalnings uppsättnings resurs och i avsnittet nodtyp i Service Fabric kluster resursen. Det är ett krav att hållbarhets värdet i dessa avsnitt matchar, annars kommer resurs distributionen att Miss förväntas.

Följande avsnitt innehåller ett exempel på en hållbarhets konflikt mellan inställningen hållbarhet för skalnings uppsättning för virtuell dator och inställningen för hållbarhets inställningen Service Fabric nodtyp:  

**Hållbarhets inställning för skalnings uppsättning för virtuell dator**
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

**Inställning av hållbarhet för Service Fabric nodtyp** 
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
* Den virtuella datorns skalnings uppsättnings matchnings fel stämmer inte överens med den aktuella Service Fabrics hållbarhets nivå
* Hållbarhet för skalnings uppsättningen för den virtuella datorn matchar inte mål Service Fabrics hållbarhets nivå
* Hållbarhet för skalnings uppsättning för virtuell dator matchar den aktuella Service Fabric hållbarhets nivå eller mål Service Fabrics hållbarhets nivå 


### <a name="mitigation"></a>Åtgärd
Så här åtgärdar du en hållbarhets konflikt som anges av ovanstående fel meddelanden:
1. Uppdatera hållbarhets nivån i den virtuella datorns skal uppsättnings tillägg eller Service Fabric nodtyp i Azure Resource Manager-mallen för att säkerställa att värdena stämmer överens.
2. Distribuera om Azure Resource Manager-mallen med de uppdaterade värdena.

## <a name="next-steps"></a>Nästa steg
* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Felsöka Service Fabric: [fel söknings guider](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
