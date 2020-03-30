---
title: VLAN och undernät i Azure VMware Solution by CloudSimple
description: Lär dig mer om VLAN och undernät i CloudSimple Private Cloud
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024899"
---
# <a name="vlans-and-subnets-overview"></a>Översikt över VLAN och undernät

CloudSimple tillhandahåller ett nätverk per region där din CloudSimple-tjänst distribueras.  Nätverket är ett enda TCP Layer 3-adressutrymme med routning aktiverat som standard.  Alla privata moln och undernät som skapas i den här regionen kan kommunicera med varandra utan någon ytterligare konfiguration.  Du kan skapa distribuerade portgrupper på vCenter med hjälp av VLAN.You can create distributed port groups on the vCenter using the VLAN.

![CloudSimple nätverkstopologi](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Vlan

Ett VLAN -nätverk (Layer 2) skapas för varje privat moln.  Layer 2-trafiken håller sig inom gränsen för ett privat moln, så att du kan isolera den lokala trafiken i det privata molnet.  En VLAN som skapats i det privata molnet kan endast användas för att skapa distribuerade portgrupper i det privata molnet.  En VLAN som skapats på ett privat moln konfigureras automatiskt på alla växlar som är anslutna till värdarna för ett privat moln.

## <a name="subnets"></a>Undernät

Du kan skapa ett undernät när du skapar ett VLAN genom att definiera adressutrymmet i undernätet. En IP-adress från adressutrymmet tilldelas som en undernätsgateway. Ett enskilt lager 3-adressutrymme tilldelas per kund och region. Du kan konfigurera alla icke överlappande anslutningsutrymmen för RFC 1918, med ditt lokala nätverk eller virtuella Azure-nätverk, i nätverksregionen.

Alla undernät kan kommunicera med varandra som standard, vilket minskar konfigurationskostnaderna för routning mellan privata moln. Öst-västliga data över datorer i samma region finns kvar i samma Layer 3-nätverk och överför över den lokala nätverksinfrastrukturen i regionen. Ingen utgående krävs för kommunikation mellan privata moln i en region. Den här metoden eliminerar alla WAN/egress-prestandastraff när du distribuerar olika arbetsbelastningar i olika privata moln.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN-undernät CIDR-intervall

Ett privat moln skapas som en isolerad VMware-stack (ESXi-värdar, vCenter, vSAN och NSX) som hanteras av en vCenter-server.  Hanteringskomponenter distribueras i det nätverk som valts för vSphere/vSAN-undernät CIDR.  Nätverkets CIDR-område är indelat i olika undernät under distributionen.

* Lägsta vSphere/vSAN-undernät CIDR-intervallprefix: **/24**
* Högsta vSphere/vSAN-undernät CIDR-intervallprefix: **/21**

> [!CAUTION]
> IP-adresser i vSphere/vSAN CIDR-intervallet är reserverade för användning av infrastrukturen Private Cloud.  Använd inte IP-adressen i det här intervallet på en virtuell dator.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN-undernät CIDR-intervallgränser

Om du väljer CIDR-intervallstorleken vSphere/vSAN påverkar storleken på ditt privata moln storleken på ditt privata moln.  I följande tabell visas det maximala antalet noder som du kan ha baserat på storleken på vSphere/vSAN-undernäten CIDR.

| Angiven vSphere/vSAN-undernät CIDR-prefixlängd | Maximalt antal noder |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Hanteringsundernät som skapats i ett privat moln

Följande hanteringsundernät skapas när du skapar ett privat moln.

* **Systemhantering**. VLAN och undernät för ESXi är värd för hanteringsnätverk, DNS-server, vCenter-server.
* **VMotion**. VLAN och undernät för ESXi är värd för vMotion-nätverket.
* **VSAN**. VLAN och undernät för ESXi är värd för vSAN-nätverket.
* **NsxtEdgeUplink1**. VLAN och undernät för VLAN-länkar till ett externt nätverk.
* **NsxtEdgeUplink2**. VLAN och undernät för VLAN-länkar till ett externt nätverk.
* **NsxtEdgeTransport**. VLAN och undernät för transportzoner styr räckvidden för Layer 2-nätverk i NSX-T.
* **NsxtHostTransport**. VLAN och undernät för värdtransportzon.

### <a name="management-network-cidr-range-breakdown"></a>Uppdelning av ledningsnätverket CIDR-intervall

vSphere/vSAN-undernät CIDR-område som anges är indelat i flera undernät.  I följande tabell visas ett exempel på uppdelningen för tillåtna prefix.  I exemplet används 192.168.0.0 som CIDR-intervall.

Exempel:

| Angiven vSphere/vSAN-undernät CIDR/prefix | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Systemhantering | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion (vMotion) | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN (på ett sätt) | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T Värdtransport | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T Kanttransport | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Kant upplänk1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Kant upplänk2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera virtuella lokala nätverk och undernät](create-vlan-subnet.md)
