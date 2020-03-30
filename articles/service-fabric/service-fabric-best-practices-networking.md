---
title: Metodtips för Azure Service Fabric-nätverk
description: Metodtips och designöverväganden för hantering av nätverksanslutning med Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551802"
---
# <a name="networking"></a>Nätverk

När du skapar och hanterar Azure Service Fabric-kluster tillhandahåller du nätverksanslutning för dina noder och program. Nätverksresurserna omfattar IP-adressintervall, virtuella nätverk, belastningsutjämnare och nätverkssäkerhetsgrupper. I den här artikeln får du lära dig metodtips för dessa resurser.

Granska Azure [Service Fabric Networking Patterns](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) för att lära dig hur du skapar kluster som använder följande funktioner: Befintligt virtuellt nätverk eller undernät, Statisk offentlig IP-adress, Intern belastningsutjämnare eller Intern och extern belastningsutjämnare.

## <a name="infrastructure-networking"></a>Nätverk av infrastruktur
Maximera prestanda för den virtuella datorn med Accelerated Networking, genom att deklarera egenskapen EnableAcceleratedNetworking i resource manager-mallen, följande utdrag är av ett nätverksgränssnitt för virtuell datorskalauppsättning som NetworkInterfaceConfigurationer som möjliggör accelererat nätverkande:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric-kluster kan etableras på [Linux med accelerated networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)och [Windows med accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Accelererad nätverkning stöds för SKU:er i Azure Virtual Machine Series: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 och Ms/Mms. Accelererad nätverk har testats med hjälp av Standard_DS8_v3 SKU den 2019-01-23 för ett Service Fabric Windows-kluster och med hjälp av Standard_DS12_v2 den 29 01/29 för ett Service Fabric Linux Cluster.

Om du vill aktivera Accelererat nätverk i ett befintligt Service Fabric-kluster måste du först [skala ut ett Service Fabric-kluster genom att lägga](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)till en skalauppsättning för virtuella datorer för att kunna utföra följande:
1. Etablera en nodetyp med accelererat nätverk aktiverat
2. Migrera dina tjänster och deras tillstånd till den etablerade NodeType med Accelerated Networking aktiverat

Det krävs en utskalning av infrastrukturen för att aktivera accelererat nätverk i ett befintligt kluster, eftersom det skulle orsaka driftstopp, eftersom det kräver att alla virtuella datorer i en tillgänglighetsuppsättning [stoppas och frigörs innan accelererade nätverk aktiveras på ett befintligt nätverkskort](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Klusternätverk

* Service Fabric-kluster kan distribueras till ett befintligt virtuellt nätverk genom att följa stegen i [nätverksmönster](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)för Service Fabric .

* Nätverkssäkerhetsgrupper (NSG) rekommenderas för nodtyper som begränsar inkommande och utgående trafik till deras kluster. Se till att nödvändiga portar öppnas i NSG. Till exempel: ![Service Fabric NSG Regler][NSGSetup]

* Den primära nodtypen, som innehåller Service Fabric-systemtjänsterna, behöver inte exponeras via den externa belastningsutjämnaren och kan exponeras av en [intern belastningsutjämnare](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Använd en [statisk offentlig IP-adress](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) för klustret.

## <a name="application-networking"></a>Nätverk av program

* Om du vill köra Windows-behållararbetsbelastningar använder du [öppet nätverksläge](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) för att underlätta kommunikation mellan service och tjänst.

* Använd en omvänd proxy som [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) eller [reverse proxy för Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) för att exponera vanliga programportar som 80 eller 443.

* För Windows-behållare som finns på luft-gapade datorer som inte kan hämta baslager från Azure-molnlagring åsidosätter du beteendet för främmande lager med hjälp av flaggan [--allow-nondistributable-artifacts](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) i Docker-demonen.

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa service fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
