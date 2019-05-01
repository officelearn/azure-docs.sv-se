---
title: VLAN och undernät i VMware-lösning genom CloudSimple - Azure
description: Lär dig mer om VLAN och undernät i ett privat moln CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 448b2b51ef6c5f7aa0bd660eec580019714caf5d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577937"
---
# <a name="vlans-and-subnets-overview"></a>Översikt över virtuella lokala nätverk och undernät

CloudSimple ger en nätverket för varje region där din CloudSimple-tjänsten har distribuerats.  Nätverket är ett enda TCP Layer 3-adressutrymme med routning aktiverad som standard.  Alla privata moln och undernät som skapats i den här regionen kan kommunicera med varandra utan någon ytterligare konfiguration.  Du kan skapa distribuerade portgrupper på vCenter med hjälp av de virtuella lokala nätverken.

## <a name="vlans"></a>VLAN

VLAN (Layer 2-nätverk) skapas per privat moln.  Layer 2-trafiken stannar inom gränserna för ett privat moln, så att du kan isolera lokala trafiken i det privata molnet.  Ett VLAN som skapats på det privata molnet kan användas för att skapa distribuerade portgrupper endast i det privata molnet.  Ett VLAN som skapats på ett privat moln konfigureras automatiskt på alla växlar som är anslutna till värdar i ett privat moln.

## <a name="subnets"></a>Undernät

Du kan skapa ett undernät när du skapar ett VLAN, genom att definiera adressutrymmet för undernätet. En IP-adress från adressutrymmet är tilldelad som en gateway för undernätet. Ett enda privat Layer 3-adressutrymme tilldelas per kund och region. Du kan konfigurera alla RFC 1918 inte överlappar adressutrymmet, med ditt lokala nätverk eller Azure-nätverk i din region för nätverket.

Alla undernät kan kommunicera med varandra som standard, vilket minskar konfigurationen användning för routning mellan privata moln. Öst-väst data över datorer i samma region kvar i samma Layer 3-nätverk och överför över lokala nätverksinfrastruktur för regionen. Ingen utgående krävs för kommunikation mellan privata moln i en region. Den här metoden eliminerar påföljd WAN/utgång prestanda i distribution av olika arbetsbelastningar i olika privata moln.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/virtuellt SAN undernät CIDR-intervall

Ett privat moln skapas som en isolerad VMware-stacken (ESXi-värdar, vCenter, virtuellt SAN-nätverk och NSX) miljön som hanteras av en vCenter-server.  Komponenterna distribueras i nätverket som valts för **vSphere/virtuellt SAN undernät CIDR**.  Nätverket CIDR-intervall är indelade i olika undernät under distributionen.

Minsta vSphere/virtuellt SAN undernät CIDR-intervall prefix: **/24** maximalt vSphere/virtuellt SAN undernät CIDR-intervall prefix:   **/21**

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/virtuellt SAN undernät CIDR-intervall gränser

Att välja virtuella vSphere/SAN-nätverket undernät CIDR-intervall storlek har en inverkan på storleken på ditt privata moln.  Tabellen nedan visar det maximala antalet noder som du kan ha baserat på storleken på vSphere/virtuellt SAN undernät CIDR.

| Den angivna virtuella vSphere/SAN-nätverket undernät CIDR prefixlängd | Maximalt antal noder |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Management-undernät som skapats på ett privat moln

Följande management undernät skapas när du skapar ett privat moln. 

* **Systemhantering** -VLAN och undernät för hantering av ESXi-värdar-nätverk, DNS-server, vCenter-servern.
* **VMotion** -VLAN och undernät för ESXi-värdar vMotion nätverk.
* **Virtuellt SAN** -VLAN och undernät för ESXi-värdar virtuellt SAN-nätverk.
* **NsxtEdgeUplink1** -VLAN och undernät för virtuellt lokalt nätverk överordnade länkar till ett externt nätverk.
* **NsxtEdgeUplink2** -VLAN och undernät för virtuellt lokalt nätverk överordnade länkar till ett externt nätverk.
* **NsxtEdgeTransport** -VLAN och undernät för transport zoner styra räckvidden för Layer 2-nätverk i NSX T.
* **NsxtHostTransport** -VLAN och undernät för värd transport zon.

### <a name="management-network-cidr-range-breakdown"></a>Hantering av nätverket CIDR-intervall analys på detaljnivå

vSphere/virtuellt SAN undernät CIDR-intervall som angetts är uppdelad i flera undernät.  Tabellen nedan visar ett exempel på en analys på detaljnivå för tillåtna prefix.  I exemplet används **192.168.0.0** som CIDR-intervall.

Exempel:

| Den angivna virtuella vSphere/SAN-nätverket undernät CIDR-prefixet | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Systemhantering | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T värd Transport | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T Edge Transport | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera virtuella lokala nätverk och undernät](https://docs.azure.cloudsimple.com/vlansubnet/)