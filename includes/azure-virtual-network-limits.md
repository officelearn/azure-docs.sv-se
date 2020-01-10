---
title: ta med fil
description: ta med fil
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 12/09/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: cf79911c24998c3d00937937cce5c68bbb564f1e
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751557"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Nätverks gränser – Azure Resource Manager följande begränsningar gäller endast för nätverks resurser som hanteras via **Azure Resource Manager** per region per prenumeration. Lär dig hur du [visar din aktuella resursanvändning mot dina prenumerations gränser](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Vi har nyligen ökat alla standard gränser till sina maximala gränser. Om det inte finns någon övre gräns kolumn har inte resursen några justerbara gränser. Om du hade de här gränserna ökat med stöd tidigare och inte ser uppdaterade gränser i följande tabeller, öppnar du [en support förfrågan online för kunder utan kostnad](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resurs | Standard/högsta gräns | 
| --- | --- |
| Virtuella nätverk |1,000 |
| Undernät per virtuellt nätverk |3 000 |
| Peering för virtuellt nätverk per virtuellt nätverk |500 |
| [Virtuella nätverksgateway (VPN-gatewayer) per virtuellt nätverk](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| DNS-servrar per virtuellt nätverk |20 |
| Privata IP-adresser per virtuellt nätverk |65 536 |
| Privata IP-adresser per nätverks gränssnitt |256 |
| Privata IP-adresser per virtuell dator |256 |
| Offentliga IP-adresser per nätverks gränssnitt |256 |
| Offentliga IP-adresser per virtuell dator |256 |
| [Samtidiga TCP-eller UDP-flöden per nätverkskort för en virtuell dator eller roll instans](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500,000 |
| Nätverkskort |65 536 |
| Nätverkssäkerhetsgrupper |5 000 |
| NSG-regler per NSG |1,000 |
| IP-adresser och intervall som har angetts för källa eller mål i en säkerhets grupp |4,000 |
| Programsäkerhetsgrupper |3 000 |
| Program säkerhets grupper per IP-konfiguration, per nätverkskort |20 |
| IP-konfigurationer per program säkerhets grupp |4,000 |
| Program säkerhets grupper som kan anges i alla säkerhets regler för en nätverks säkerhets grupp |100 |
| Användardefinierade routningstabeller |200 |
| Användardefinierade vägar per routningstabell |400 |
| Punkt-till-plats-rot certifikat per Azure-VPN Gateway |20 |
| Kranar för virtuellt nätverk |100 |
| Nätverks gränssnitt för att trycka på konfigurationer per virtuellt nätverk |100 |

#### <a name="publicip-address"></a>Begränsningar för offentliga IP-adresser
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Offentliga IP-adresser – dynamiska | 1 000 för Basic. |Kontakta supporten. |
| Offentliga IP-adresser – statiska | 1 000 för Basic. |Kontakta supporten. |
| Offentliga IP-adresser – statiska | 1 000 för standard.|Kontakta supporten. |
| Prefixlängd för offentlig IP | /28 | Kontakta supporten. |

#### <a name="load-balancer"></a>Gränser för belastnings utjämning
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration. Lär dig hur du [visar din aktuella resursanvändning mot dina prenumerations gränser](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Resurs                                | Standard/högsta gräns         |
|-----------------------------------------|-------------------------------|
| Lastbalanserare                          | 1,000                         |
| Regler per resurs                      | 1,500                         |
| Regler per nätverkskort (över alla IP-adresser på ett nätverkskort) | 300                           |
| IP-konfigurationer för klient del             | 600                           |
| Storlek på serverdelspool                          | 1 000 instanser, enskilt virtuellt nätverk |
| Server dels resurser per belastningsutjämnare<sup>1<sup>   | 150                 |
| Portar med hög tillgänglighet                 | 1 per intern klient del      |

<sup>1</sup> Gränsen är upp till 150 resurser, i valfri kombination av fristående virtuella dator resurser, tillgänglighets uppsättnings resurser och resurser för skalnings uppsättning för virtuella datorer.

**Grundläggande Load Balancer**

| Resurs                                | Standard/högsta gräns        |
|-----------------------------------------|------------------------------|
| Lastbalanserare                          | 1,000                        |
| Regler per resurs                      | 250                          |
| Regler per nätverkskort (över alla IP-adresser på ett nätverkskort) | 300                          |
| IP-konfigurationer för klient del             | 200                          |
| Storlek på serverdelspool                           | 100 instanser, enskild tillgänglighets uppsättning |

#### <a name="virtual-networking-limits-classic"></a>Följande begränsningar gäller endast för nätverks resurser som hanteras via den **klassiska** distributions modellen per prenumeration. Lär dig hur du [visar din aktuella resursanvändning mot dina prenumerations gränser](../articles/networking/check-usage-against-limits.md).

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |100 |100 |
| Lokala nätverksplatser |20 |50 |
| DNS-servrar per virtuellt nätverk |20 |20 |
| Privata IP-adresser per virtuellt nätverk |4 096 |4 096 |
| Samtidiga TCP-eller UDP-flöden per nätverkskort för en virtuell dator eller roll instans |500 000, upp till 1 000 000 för två eller flera nätverkskort. |500 000, upp till 1 000 000 för två eller flera nätverkskort. |
| Nätverks säkerhets grupper (NSG: er) |200 |200 |
| NSG-regler per NSG |1,000 |1,000 |
| Användardefinierade routningstabeller |200 |200 |
| Användardefinierade vägar per routningstabell |400 |400 |
| Offentliga IP-adresser (dynamiska) |500 |500 |
| Reserverade offentliga IP-adresser |500 |500 |
| Offentlig VIP per distribution |5 |Kontakta supporten |
| Privat VIP (intern belastnings utjämning) per distribution |1 |1 |
| Åtkomst kontrol listor för slut punkt (ACL) |50 |50 |
