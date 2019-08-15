---
title: Metod tips för Azure Service Fabric nätverk | Microsoft Docs
description: Metod tips för hantering av Service Fabric nätverk.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d221b828624e649a0d04a89c4394fe5a7fa857dd
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "66237325"
---
# <a name="networking"></a>Nätverk

När du skapar och hanterar Azure Service Fabric-kluster ger du nätverks anslutning för dina noder och program. Nätverks resurserna omfattar IP-adressintervall, virtuella nätverk, belastnings utjämning och nätverks säkerhets grupper. I den här artikeln får du lära dig metod tips för dessa resurser.

Granska Azure [Service Fabric nätverks mönster](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) för att lära dig hur du skapar kluster som använder följande funktioner: Befintligt virtuellt nätverk eller undernät, statisk offentlig IP-adress, intern belastningsutjämnare eller intern och extern belastningsutjämnare.

## <a name="infrastructure-networking"></a>Infrastruktur nätverk
Maximera prestandan för den virtuella datorn med accelererat nätverk, genom att deklarera enableAcceleratedNetworking-egenskapen i din Resource Manager-mall, är följande fragment en skalnings uppsättning för virtuella datorer NetworkInterfaceConfigurations som aktiverar accelererat nätverk:

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

Accelererat nätverk stöds för Azure Virtual Machine-serien SKU: er: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 och MS/MMS. Accelererat nätverk har testats med Standard_DS8_v3 SKU på 1/23/2019 för ett Service Fabric Windows-kluster och med Standard_DS12_v2 på 01/29/2019 för ett Service Fabric Linux-kluster.

Om du vill aktivera accelererat nätverk på ett befintligt Service Fabric-kluster måste du först [skala ett Service Fabric kluster genom att lägga till en skalnings uppsättning för virtuella datorer](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)för att utföra följande:
1. Etablera en NodeType med accelererat nätverk aktiverat
2. Migrera dina tjänster och deras tillstånd till den etablerade NodeType med accelererat nätverk aktiverat

Att skala ut infrastruktur krävs för att aktivera accelererat nätverk i ett befintligt kluster, eftersom aktivering av accelererade nätverk på plats skulle orsaka drift stopp, eftersom det krävs att alla virtuella datorer i en tillgänglighets uppsättning stoppas och frigörs [innan Aktivera accelererat nätverk på alla befintliga nätverkskort](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Kluster nätverk

* Service Fabric kluster kan distribueras till ett befintligt virtuellt nätverk genom att följa stegen som beskrivs i [Service Fabric nätverks mönster](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Nätverks säkerhets grupper (NSG: er) rekommenderas för nodtyper som begränsar inkommande och utgående trafik till deras kluster. Se till att de nödvändiga portarna är öppna i NSG. Exempel: ![Service Fabric NSG-regler][NSGSetup]

* Den primära nodtypen som innehåller Service Fabric system tjänsterna behöver inte exponeras via den externa belastningsutjämnaren och kan exponeras av en [intern belastningsutjämnare](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Använd en [statisk offentlig IP-adress](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) för klustret.

## <a name="application-networking"></a>Program nätverk

* Om du vill köra arbets belastningar i Windows-behållare använder du [Öppna Nätverks läge](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) för att förenkla kommunikationen mellan tjänster och tjänster.

* Använd en omvänd proxy, till exempel [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) eller [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) den omvända proxyn för att visa vanliga program portar, till exempel 80 eller 443.

* För Windows-behållare som finns på gapped datorer som inte kan hämta bas lager från moln lagring i Azure kan du åsidosätta beteendet för det främmande lagret med hjälp av flaggan [--Allow-undistribuerbara-artefakter](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) i Docker daemon.

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
