---
title: Azure Service Fabric-nodtyper och VM-skalningsuppsättningar | Microsoft Docs
description: Lär dig hur Azure Service Fabric-noden typer som är relaterade till VM-skalningsuppsättning anger och hur du ansluter via en fjärranslutning till en skala ange instans eller en klusternod.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: a5f8735df2b230de2b0ddcdcccff09430bada9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684680"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric-nodtyper och VM-skalningsuppsättningar
[VM-skalningsuppsättningar](/azure/virtual-machine-scale-sets) är en Azure-beräkningsresurs. Du kan använda skalningsuppsättningar för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster ställer in en separat skala.  Service Fabric-körningen installerad på varje virtuell dator i skalningsuppsättningen med tillägget för Microsoft.Azure.ServiceFabric virtuell dator. Du kan oberoende skala varje nodtyp upp eller ned, ändra den operativsystem-SKU som körs på varje nod i klustret, ha olika portar öppna och använda olika kapacitet.

Följande bild visar ett kluster med två nodtyper med namnet FrontEnd och BackEnd. Varje nodtyp har fem noder.

![Ett kluster med två nodtyper][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mappa VM-skalningsuppsättningsinstanser till noder
I bilden ovan visas skalningsuppsättningsinstanser startar instansen 0 och sedan öka med 1. Numreringen återspeglas i nod-namn. Noden BackEnd_0 är till exempel instansen 0 av skalningsuppsättningen serverdel. Den här specifika skalningsuppsättningen har fem instanser, med namnet BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 och BackEnd_4.

När du skalar upp en skalningsuppsättning skapas en ny instans. Ny scale set-instansnamnet är vanligtvis skalningsuppsättningen namn samt nästa instansnummer. I vårt exempel är det BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mappa scale set-belastningsutjämnare till nodtyper och skalningsuppsättningar
Om du har distribuerat ditt kluster i Azure-portalen eller använt exempelmallen för Azure Resource Manager, visas alla resurser under en resursgrupp. Du kan se belastningsutjämnare för varje scale set eller noden. Namnet på belastningsutjämnaren använder följande format: **LB -&lt;nodtypnamn&gt;**. Ett exempel är LB-sfcluster4doc-0, enligt följande bild:

![Resurser][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric VM-tillägget
Service Fabric VM-tillägget används för att starta Service Fabric till Azure Virtual Machines och konfigurera nod-säkerhet.

Följande är ett kodfragment i Service Fabric VM-tillägg:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Här följer Egenskapsbeskrivningar:

| **Namn** | **Tillåtna värden** | ** --- ** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| namn | string | --- | unikt namn för tillägg |
| typ | ”ServiceFabricLinuxNode” eller ”ServiceFabricWindowsNode | --- | Identifierar OS Service Fabric bootstrapping till |
| autoUpgradeMinorVersion | SANT eller FALSKT | --- | Aktivera automatisk uppgradering av SF Runtime delversioner |
| utgivare | Microsoft.Azure.ServiceFabric | --- | Service Fabric-tillägget utgivarens namn |
| clusterEndpont | string | --- | URI:port till hanteringsslutpunkten |
| nodeTypeRef | string | --- | namnet på nodeType |
| durabilityLevel | Brons, silver, guld, Platina | --- | tiden som tillåts att pausa oföränderlig infrastruktur i Azure |
| enableParallelJobs | SANT eller FALSKT | --- | Aktivera Compute ParallelJobs som att ta bort virtuell dator och starta om virtuell dator i samma skalningsuppsättningen parallellt |
| nicPrefixOverride | string | --- | Undernätets Prefix som ”10.0.0.0/24” |
| commonNames | string[] | --- | Vanliga namn på installerade klustercertifikat |
| x509StoreName | string | --- | Namnet på Store där det finns installerade klustercertifikat |
| typeHandlerVersion | 1.1 | --- | Versionen av tillägget. 1.0 klassiska versionen av tillägget rekommenderas för att uppgradera till 1.1 |

## <a name="next-steps"></a>Nästa steg
* Se den [översikt över ”distribuera var som helst”-funktionen och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster security](service-fabric-cluster-security.md).
* [Fjärranslutning](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) till en specifik scale set-instans
* [Uppdatera RDP-portintervallvärden](./scripts/service-fabric-powershell-change-rdp-port-range.md) i klustret virtuella datorer efter distributionen
* [Ändra administratörens användarnamn och lösenord](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för virtuella datorer klustret

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
