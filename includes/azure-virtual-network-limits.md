---
title: ta med fil
description: ta med fil
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 08/16/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 4ab574f99f678683497b0f00b36abcda89991f88
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "43435613"
---
<a name="virtual-networking-limits-classic"></a>Följande begränsningar gäller endast för nätverksresurser som hanteras via den klassiska distributionsmodellen per prenumeration. Lär dig hur du [visa din aktuella användning mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md).

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |50 |100 |
| Lokala nätverksplatser |20 |kontakta supporten |
| DNS-servrar per virtuellt nätverk |20 |100 |
| Privata IP-adresser per virtuellt nätverk |4096 |4096 |
| Samtidiga TCP eller UDP flöden per nätverkskort för en virtuell dator eller rollinstans |500 000 |500 000 |
| Nätverkssäkerhetsgrupper (NSG) |100 |200 |
| NSG-regler per NSG |200 |1000 |
| Användardefinierade vägtabeller |100 |200 |
| Användardefinierade vägar vägtabell |100 |400 |
| Offentliga IP-adresser (dynamiska) |5 |kontakta supporten |
| Reserverade offentliga IP-adresser |20 |kontakta supporten |
| Offentlig VIP per distribution |5 |kontakta supporten |
| Privat VIP (ILB) per distribution |1 |1 |
| Åtkomstkontrollistor (ACL:er) för slutpunkt |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Nätverksgränser – Azure Resource Manager
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration. Lär dig hur du [visa din aktuella användning mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Vi har nyligen ökat alla standardgränser till deras högsta gränser. Om det finns inga **maxgränsen** kolumnen och sedan resursen saknar justerbara gränser. Om du har haft gränserna ökat med stöd för tidigare och ser inte uppdaterade gränser enligt nedan, kan du [öppna en kundsupportärende utan kostnad](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resurs | Standardgräns | 
| --- | --- |
| Virtuella nätverk |1000 |
| Undernät per virtuellt nätverk |3000 |
| Vnet-peering per virtuellt nätverk |100 |
| DNS-servrar per virtuellt nätverk |25 |
| Privata IP-adresser per virtuellt nätverk |65536 |
| Privata IP-adresser per nätverksgränssnitt |256 |
| Samtidiga TCP eller UDP flöden per nätverkskort för en virtuell dator eller rollinstans |500 000 |
| Nätverksgränssnitt (NIC) |24000 |
| Nätverkssäkerhetsgrupper (NSG) |5000 |
| NSG-regler per NSG |1000 |
| IP-adresser och intervall som angetts för källa eller mål i en säkerhetsgrupp |4000 |
| Programsäkerhetsgrupper |3000 |
| Programsäkerhetsgrupper per IP-konfiguration, per nätverkskort |20 |
| IP-konfigurationer per programsäkerhetsgruppen |4000 |
| Programsäkerhetsgrupper som kan anges i alla säkerhetsregler för en grupp |100 |
| Användardefinierade vägtabeller |200 |
| Användardefinierade vägar vägtabell |400 |
| Punkt-till-plats-rotcertifikat per VPN Gateway |20 |

#### <a name="publicip-address"></a>Offentliga IP-adress gränser

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Offentliga IP-adresser – dynamisk |200 (grundläggande) |kontakta supporten |
| Offentliga IP-adresser – statiska |200 (grundläggande) |kontakta supporten |
| Offentliga IP-adresser – statiska |200 (standard) |kontakta supporten |

#### <a name="load-balancer"></a>Belastningsutjämnaren begränsar
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration. Lär dig hur du [visa din aktuella användning mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md)

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Lastbalanserare | 100 | 1000 |
| Regler per resurs, Basic | 250 | 250 |
| Regler per resurs, Standard | 1500 | 1500 |
| Regler per IP-konfiguration | 299 |299 |
| Frontend IP-konfigurationer, Basic | 10 | 200 |
| Frontend IP-konfigurationer, Standard | 10 | 600 |
| Serverpoolen Basic | 100, enkel Tillgänglighetsuppsättning | 100, enkel Tillgänglighetsuppsättning |
| Backend-pool, Standard | 1000, enkel VNet | 1000, enkel VNet |
| Serverdelsresurser per belastningsutjämnare, Standard * | 150 | 150 |
| Hög tillgänglighet portar, Standard | 1 per interna klientdel | 1 per interna klientdel |

** Upp till 150 resurser, valfri kombination av fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar.

[Kontakta supporten](../articles/azure-supportability/resource-manager-core-quotas-request.md ) om du måste öka gränserna från standardvärdet.

