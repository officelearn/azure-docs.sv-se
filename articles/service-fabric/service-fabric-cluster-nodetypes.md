---
title: Nodtyper och skaluppsättningar för virtuella datorer
description: Lär dig hur nodtyper i Azure Service Fabric relaterar till skalningsuppsättningar för virtuella datorer och hur du fjärransluter till en skalningsuppsättningsinstans eller klusternod.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199724"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Nodtyper för Azure Service Fabric-nod och skalningsuppsättningar för virtuella datorer

[Skalningsuppsättningar för virtuella datorer](/azure/virtual-machine-scale-sets) är en Azure-beräkningsresurs. Du kan använda skalningsuppsättningar för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster ställer in exakt en skalningsuppsättning: flera nodtyper kan inte backas upp av samma skalningsuppsättning och en nodtyp bör inte (i de flesta fall) backas upp av flera skalningsuppsättningar. Ett undantag till detta är i den sällsynta situationen [med lodrät skalning](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) `nodeTypeRef` av en nodtyp, när du tillfälligt har två skalningsuppsättningar med samma värde medan repliker migreras från originalet till den uppgraderade skalningsuppsättningen.

Service Fabric-körningen installeras på varje virtuell dator i skalan som anges av tillägget *Microsoft.Azure.ServiceFabric* Virtual Machine. Du kan självständigt skala varje nodtyp uppåt eller nedåt, ändra OS SKU som körs på varje klusternod, ha olika uppsättningar portar öppna och använda olika kapacitetsmått.

Följande bild visar ett kluster med två nodtyper med namnet *FrontEnd* och *BackEnd*. Varje nodtyp har fem noder.

![Ett kluster som har två nodtyper][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mappa skalningsuppsättningsinstanser för virtuella datorer till noder

Som visas i föregående bild börjar skaluppsättningsinstanser vid instans 0 och ökar sedan med 1. Numreringen återspeglas i nodnamnen. Noden BackEnd_0 är till exempel instans 0 i backend-skalningsuppsättningen. Den här skalningsuppsättningen har fem instanser med namnet BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 och BackEnd_4.

När du skalar upp en skalningsuppsättning skapas en ny instans. Det nya skalningsuppsättningsinstansnamnet är vanligtvis skalningsuppsättningsnamnet plus nästa instansnummer. I vårt exempel är det BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Kartskalauppsättning belastningsutjämnare till nodtyper och skaluppsättningar

Om du har distribuerat klustret i Azure-portalen eller använt exempelmallen Azure Resource Manager visas alla resurser under en resursgrupp. Du kan se belastningsutjämnare för varje skalningsuppsättning eller nodtyp. Belastningsutjämnarens namn använder följande format: **LB-&lt;&gt;nodtypnamn**. Ett exempel är LB-sfcluster4doc-0, som visas i följande bild:

![Resurser][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Tillägg för virtuell dator för tjänstinfrastruktur

Service Fabric Virtual Machine Extension används för att bootstrap Service Fabric till Azure Virtuella datorer och konfigurera nodsäkerhet.

Följande är ett utdrag av tillägget Virtual Machine för service fabric:

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
         "dataPath": "D:\\\\SvcFab",
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

Följande är egenskapsbeskrivningarna:

| **Namn** | **Tillåtna värden** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
| namn | sträng | Unikt namn för tillägg |
| typ | "ServiceFabricLinuxNode" eller "ServiceFabricWindowsNode" | Identifierar OS Service Fabric är bootstrapping till |
| autoUpgradeMinorVersion | sant eller falskt | Aktivera automatisk uppgradering av SF Runtime-delversioner |
| utgivare | Microsoft.Azure.ServiceFabric | Namn på utgivaren för tillägget Service Fabric |
| clusterEndpont | sträng | URI:PORT till hantering slutpunkt |
| nodeTypeRef | sträng | Namn på nodtyp |
| hållbarhetNivå | brons, silver, guld, platina | Tid tillåts pausa oföränderlig Azure-infrastruktur |
| aktiveraParallelJobs | sant eller falskt | Aktivera Compute ParallelJobs som ta bort vm och starta om den virtuella datorn i samma skalningsuppsättning parallellt |
| nicPrefixOverride | sträng | Prefix för undernät som "10.0.0.0/24" |
| commonNames (vanligaste) | sträng[] | Vanliga namn på installerade klustercertifikat |
| x509StoreName | sträng | Namn på Arkivet där det installerade klustercertifikatet finns |
| typHandlerVersion | 1.1 | Version av tillägg. 1.0 klassisk version av tillägget rekommenderas att uppgradera till 1.1 |
| dataPath (dataPath) | sträng | Sökväg till den enhet som används för att spara tillstånd för Service Fabric-systemtjänster och programdata.

## <a name="next-steps"></a>Nästa steg

* Se [översikten över funktionen "Distribuera var som helst" och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [klustersäkerhet](service-fabric-cluster-security.md).
* [Fjärranslutning](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) till en specifik skalningsuppsättningsinstans
* [Uppdatera RDP-portintervallvärdena](./scripts/service-fabric-powershell-change-rdp-port-range.md) på kluster-virtuella datorer efter distribution
* [Ändra administratörsanvändarnamn och lösenord](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för virtuella kluster-datorer

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
