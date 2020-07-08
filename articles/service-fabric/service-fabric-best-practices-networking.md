---
title: Metod tips för Azure Service Fabric-nätverk
description: Bästa praxis och design överväganden för att hantera nätverks anslutningar med hjälp av Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75551802"
---
# <a name="networking"></a>Nätverk

När du skapar och hanterar Azure Service Fabric-kluster ger du nätverks anslutning för dina noder och program. Nätverks resurserna omfattar IP-adressintervall, virtuella nätverk, belastnings utjämning och nätverks säkerhets grupper. I den här artikeln får du lära dig metod tips för dessa resurser.

Granska Azure [Service Fabric nätverks mönster](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) för att lära dig hur du skapar kluster som använder följande funktioner: befintligt virtuellt nätverk eller undernät, statisk offentlig IP-adress, intern belastningsutjämnare eller intern och extern belastningsutjämnare.

## <a name="infrastructure-networking"></a>Infrastruktur nätverk
Maximera prestandan för den virtuella datorn med accelererat nätverk, genom att deklarera enableAcceleratedNetworking-egenskapen i din Resource Manager-mall, är följande kodfragment av en NetworkInterfaceConfigurations för skalnings uppsättning för virtuella datorer som möjliggör accelererat nätverk:

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
Service Fabric kluster kan tillhandahållas i [Linux med accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)och [Windows med accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Accelererat nätverk stöds för Azure Virtual Machine serien SKU: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 och MS/MMS. Accelererat nätverk har testats med Standard_DS8_v3 SKU på 1/23/2019 för ett Service Fabric Windows-kluster och med Standard_DS12_v2 på 01/29/2019 för ett Service Fabric Linux-kluster.

Om du vill aktivera accelererat nätverk på ett befintligt Service Fabric-kluster måste du först [skala ett Service Fabric kluster genom att lägga till en skalnings uppsättning för virtuella datorer](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)för att utföra följande:
1. Etablera en NodeType med accelererat nätverk aktiverat
2. Migrera dina tjänster och deras tillstånd till den etablerade NodeType med accelererat nätverk aktiverat

Att skala ut infrastruktur krävs för att aktivera accelererat nätverk i ett befintligt kluster, eftersom aktivering av accelererade nätverk på plats skulle orsaka drift stopp, eftersom det krävs att alla virtuella datorer i en tillgänglighets uppsättning [stoppas och frigörs innan du aktiverar accelererat nätverk på ett befintligt nätverkskort](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Kluster nätverk

* Service Fabric kluster kan distribueras till ett befintligt virtuellt nätverk genom att följa stegen som beskrivs i [Service Fabric nätverks mönster](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Nätverks säkerhets grupper (NSG: er) rekommenderas för nodtyper som begränsar inkommande och utgående trafik till deras kluster. Se till att de nödvändiga portarna är öppna i NSG. Exempel: ![ Service Fabric NSG-regler][NSGSetup]

* Den primära nodtypen som innehåller Service Fabric system tjänsterna behöver inte exponeras via den externa belastningsutjämnaren och kan exponeras av en [intern belastningsutjämnare](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Använd en [statisk offentlig IP-adress](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) för klustret.

## <a name="application-networking"></a>Program nätverk

* Om du vill köra arbets belastningar i Windows-behållare använder du [Öppna Nätverks läge](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) för att förenkla kommunikationen mellan tjänster och tjänster.

* Använd en omvänd proxy, till exempel [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) eller [Service Fabric den omvända proxyn](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) för att visa vanliga program portar, till exempel 80 eller 443.

* För Windows-behållare som finns på gapped datorer som inte kan hämta bas lager från moln lagring i Azure kan du åsidosätta beteendet för det främmande lagret med hjälp av flaggan [--Allow-undistribuerbara-artefakter](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) i Docker daemon.

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
