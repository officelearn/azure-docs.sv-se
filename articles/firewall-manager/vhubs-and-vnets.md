---
title: Vilka är arkitekturalternativen för Azure Firewall Manager?
description: Jämför och kontrastera med virtuella hubbnätverk eller säkra virtuella hubbarkitekturer med Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444581"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Vilka är arkitekturalternativen för Azure Firewall Manager?

Azure Firewall Manager kan tillhandahålla säkerhetshantering för två nätverksarkitekturtyper:

- **säker virtuell hubb**

   En [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) är en Microsoft-hanterad resurs som gör att du enkelt kan skapa hubb- och ekrararkitekturer. När säkerhet och routningsprinciper är associerade med ett sådant nav kallas det för ett *[skyddat virtuellt nav](secured-virtual-hub.md)*. 
- **virtuellt nätverk för nav**

   Det här är ett virtuellt standardnätverk för Azure som du skapar och hanterar själv. När säkerhetsprinciper är associerade med ett sådant nav kallas det för ett *virtuellt navnätverk*. För närvarande stöds endast Azure Firewall Policy. Du kan peer spoke virtuella nätverk som innehåller din arbetsbelastning servrar och tjänster. Du kan också hantera brandväggar i fristående virtuella nätverk som inte är peered till någon eker.

## <a name="comparison"></a>Jämförelse

I följande tabell jämförs dessa två arkitekturalternativ och du kan avgöra vilken som är rätt för organisationens säkerhetskrav:


|  |**Virtuellt hub-nätverk**|**Skyddad virtuell hubb**  |
|---------|---------|---------|
|**Underliggande resurs**     |Virtuellt nätverk|Virtuell WAN-hubb|
|**Hub & talade**     |Använder virtual network peering|Automatiserad med virtuell hubbnätverksanslutning|
|**Anslutning på prem**     |VPN Gateway upp till 10 Gbit/s och 30 S2S-anslutningar. ExpressRoute (ort)|Mer skalbar VPN-gateway upp 20 Gbit/s och 1000 S2S-anslutningar. Expressrutt|
|**Automatiserad filialanslutning med SDWAN**      |Stöds inte|Stöds|
|**Nav per region**     |Flera virtuella nätverk per region|En virtuell hubb per region. Flera hubbar möjliga med flera virtuella WAN|
|**Azure-brandväggen – flera offentliga IP-adresser**      |Kunden tillhandahålls|Genereras automatiskt. Ska vara tillgänglig av GA.|
|**Tillgänglighetszoner för Azure-brandväggen**     |Stöds|Inte tillgängligt i förhandsversionen. Ska vara tillgänglig av GA|
|**Avancerad Internetsäkerhet med tredjepartssäkerhet som tjänstpartner**     |Kunden etablerade och hanterade VPN-anslutning till partnertjänst val|Automatiserad via Trusted Security Partner flöde och partner hantering erfarenhet|
|**Centraliserad rutthantering för att dirigera trafik till navet**     |Kundhanterad användardefinierad väg|Stöds med BGP|
|**Brandvägg för webbaserade program på Application Gateway** |Stöds i virtuellt nätverk|Stöds för närvarande i ekernätverk|
|**Virtuell nätverksinstallation**|Stöds i virtuellt nätverk|Stöds för närvarande i ekernätverk|

## <a name="next-steps"></a>Nästa steg

- Granska [distributionsdistributionsöversikten för förhandsversionen av Azure Firewall Manager](deployment-overview.md)
- Läs mer om [säkra virtuella hubbar](secured-virtual-hub.md).