---
title: Azure Service Fabric nätverk säkerhetsmetoder | Microsoft Docs
description: Metodtips för att hantera Service Fabric-nätverk.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 4118e52e8fac34e860f05e17effb01e9375582ec
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54914070"
---
# <a name="networking"></a>Nätverk

När du skapar och hanterar Azure Service Fabric-kluster tillhandahåller du nätverksanslutning för dina noder och program. Nätverksresurser omfattar IP-adressintervall, virtuella nätverk, belastningsutjämnare och nätverkssäkerhetsgrupper. I den här artikeln får du lära dig bästa praxis för dessa resurser.

Granska Azure [Service Fabric nätverk mönster](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) att lära dig hur du skapar kluster som använder följande funktioner: Befintligt virtuellt nätverk eller undernät, statiska offentliga IP-adressen, endast intern belastningsutjämnare eller intern och extern belastningsutjämnare.

## <a name="infrastructure-networking"></a>Infrastruktur för nätverk
Maximera prestandan för din virtuella dator med Accelererat nätverk, genom att deklarera enableAcceleratedNetworking-egenskapen i Resource Manager-mallen i följande kodfragment är av en virtuell dator skala ange NetworkInterfaceConfigurations som gör det möjligt att Accelererat nätverk:

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
Service Fabric-kluster kan etableras på [Linux med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), och [Windows med Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Accelererat nätverk stöds för SKU: er för Azure VM-serier: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 och Ms-/ Mms. Accelererat nätverk har testats med Standard_DS8_v3 SKU: N 1/23/2019.

Om du vill aktivera Accelererat nätverk i ett befintligt Service Fabric-kluster, måste du först [skala ut ett Service Fabric-kluster genom att lägga till en Virtual Machine Scale Sets](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), för att utföra följande:
1. Etablera en NodeType med Accelererat nätverk aktiverat
2. Migrera dina tjänster och deras tillstånd till den etablerade NodeType med Accelererat nätverk aktiverat

Skala ut infrastruktur krävs för att aktivera Accelererat nätverk i ett befintligt kluster eftersom aktivering av Accelererat nätverk på plats skulle orsaka driftstopp, eftersom den kräver att alla virtuella datorer i en tillgänglighetsuppsättning [stoppa och Frigör innan du aktiverar Accelererat nätverk på en befintlig NIC](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Klusternätverk

* Service Fabric-kluster kan distribueras till ett befintligt virtuellt nätverk genom att följa anvisningarna i [Service Fabric nätverk mönster](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Nätverkssäkerhetsgrupper (NSG) rekommenderas för nodtyper som begränsar inkommande och utgående trafik till deras kluster. Se till att alla nödvändiga portar är öppna i NSG: N. Exempel: ![Service Fabric NSG-regler][NSGSetup]

* Den primära nodtypen som innehåller Service Fabric-systemtjänster behöver inte exponeras via den externa belastningsutjämnaren och kan visas genom en [intern belastningsutjämnare](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Använd en [statisk offentlig IP-adress](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) för klustret.

## <a name="application-networking"></a>Application Networking

* Använd för att köra Windows-behållararbetsbelastningar [öppna nätverk läge](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) att underlätta tjänst-till-tjänst-kommunikation.

* Använd en omvänd proxy som [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) eller [Service Fabric omvänd proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) att exponera programportar för vanliga, till exempel 80 eller 443.

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa för Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa en Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
