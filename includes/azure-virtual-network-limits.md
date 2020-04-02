---
title: ta med fil
description: ta med fil
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 95cb29e871cce2ba600ab654d48c685b90ed027e
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80573185"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Nätverksgränser - Azure Resource Manager
Följande gränser gäller endast för nätverksresurser som hanteras via **Azure Resource Manager** per region per prenumeration. Läs om hur du [visar din nuvarande resursanvändning mot dina prenumerationsgränser](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Vi har nyligen ökat alla standardgränser till deras maxgränser. Om det inte finns någon högsta gränskolumn har resursen inga justerbara gränser. Om du tidigare har höjt dessa gränser med support och inte ser uppdaterade gränser i följande tabeller [öppnar du en kundsupportbegäran online utan kostnad](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Resurs | Gräns | 
| --- | --- |
| Virtuella nätverk |1,000 |
| Undernät per virtuellt nätverk |3 000 |
| Peering för virtuella nätverk per virtuellt nätverk |500 |
| [Virtuella nätverksgateways (VPN-gateways) per virtuellt nätverk](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Virtuella nätverksgateways (ExpressRoute gateways) per virtuellt nätverk](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS-servrar per virtuellt nätverk |20 |
| Privata IP-adresser per virtuellt nätverk |65 536 |
| Privata IP-adresser per nätverksgränssnitt |256 |
| Privata IP-adresser per virtuell dator |256 |
| Offentliga IP-adresser per nätverksgränssnitt |256 |
| Offentliga IP-adresser per virtuell dator |256 |
| [Samtidiga TCP- eller UDP-flöden per nätverkskort för en virtuell dator eller rollinstans](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500 000 |
| Nätverkskort |65 536 |
| Nätverkssäkerhetsgrupper |5 000 |
| NSG-regler per NSG |1,000 |
| IP-adresser och intervall som angetts för källa eller mål i en säkerhetsgrupp |4 000 |
| Programsäkerhetsgrupper |3 000 |
| Programsäkerhetsgrupper per IP-konfiguration, per nätverkskort |20 |
| IP-konfigurationer per programsäkerhetsgrupp |4 000 |
| Programsäkerhetsgrupper som kan anges inom alla säkerhetsregler för en nätverkssäkerhetsgrupp |100 |
| Användardefinierade flödestabeller |200 |
| Användardefinierade vägar per flödestabell |400 |
| Rotcertifikat från punkt till plats per Azure VPN Gateway |20 |
| Taps för virtuella nätverk |100 |
| TAP-konfigurationer för nätverksgränssnitt per virtuellt nätverk TAP |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Begränsningar för offentlig IP-adress
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Offentliga IP-adresser<sup>1</sup> | 10 för Basic. | Kontakta supporten. |
| Statiska offentliga IP-adresser<sup>1</sup> | 10 för Basic. | Kontakta supporten. |
| Vanliga offentliga IP-adresser<sup>1</sup> | 10 | Kontakta supporten. |
| Prefix till offentliga IP-adresser | begränsas av antalet offentliga standard-IPs i en prenumeration | Kontakta supporten. |
| Allmän IP-prefixlängd | /28 | Kontakta supporten. |

<sup>1.</sup> Standardgränser för offentliga IP-adresser varierar beroende på erbjudandekategorityp, till exempel kostnadsfri utvärderingsversion, användningsbaserad betalning, CSP. Standardprenumerationerna för Enterprise Agreement är till exempel 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Belastningsutjämnaregränser
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration. Läs om hur du [visar din nuvarande resursanvändning mot dina prenumerationsgränser](../articles/networking/check-usage-against-limits.md).

**Standard belastningsutjämnare**

| Resurs                                | Gräns         |
|-----------------------------------------|-------------------------------|
| Lastbalanserare                          | 1,000                         |
| Regler per resurs                      | 1500                         |
| Regler per nätverkskort (över alla IPs på ett nätverkskort) | 300                           |
| IP-konfigurationer för klientdel              | 600                           |
| Storlek på serverdelspool                       | 1 000 IP-konfigurationer, ett enda virtuellt nätverk |
| Backend-resurser per <sup>belastningsutjämnare 1<sup> | 150                   |
| Portar med hög tillgänglighet                 | 1 per inre frontend       |
| Utgående regler per belastningsutjämnare        | 20                            |
| [Timeout för tomgång för TCP](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minuter/30 minuter          |

<sup>1.</sup> Gränsen är upp till 150 resurser, i valfri kombination av fristående resurser för virtuella datorer, tillgänglighetsuppsättningsresurser och skaluppsättningsresurser för virtuella datorer.

**Grundläggande belastningsutjämnare**

| Resurs                                | Gräns        |
|-----------------------------------------|------------------------------|
| Lastbalanserare                          | 1,000                        |
| Regler per resurs                      | 250                          |
| Regler per nätverkskort (över alla IPs på ett nätverkskort) | 300                          |
| IP-konfigurationer för klientdel              | 200                          |
| Storlek på serverdelspool                       | 300 IP-konfigurationer, en tillgänglighetsuppsättning |
| Tillgänglighetsuppsättningar per belastningsutjämnare     | 150                          |

<a name="virtual-networking-limits-classic"></a>Följande gränser gäller endast för nätverksresurser som hanteras via den **klassiska** distributionsmodellen per prenumeration. Läs om hur du [visar din nuvarande resursanvändning mot dina prenumerationsgränser](../articles/networking/check-usage-against-limits.md).

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |100 |100 |
| Lokala nätverksplatser |20 |50 |
| DNS-servrar per virtuellt nätverk |20 |20 |
| Privata IP-adresser per virtuellt nätverk |4,096 |4,096 |
| Samtidiga TCP- eller UDP-flöden per nätverkskort för en virtuell dator eller rollinstans |500 000, upp till 1 000 000 för två eller flera nätverkskort. |500 000, upp till 1 000 000 för två eller flera nätverkskort. |
| Nätverkssäkerhetsgrupper (NSG) |200 |200 |
| NSG-regler per NSG |1,000 |1,000 |
| Användardefinierade flödestabeller |200 |200 |
| Användardefinierade vägar per flödestabell |400 |400 |
| Offentliga IP-adresser (dynamiska) |500 |500 |
| Reserverade offentliga IP-adresser |500 |500 |
| Offentlig VIP per distribution |5 |Kontakta supporten |
| Privat VIP (intern belastningsutjämning) per distribution |1 |1 |
| Kontrolllistor för slutpunktsåtkomst (ACL:er) |50 |50 |
