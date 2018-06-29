---
title: ta med fil
description: ta med fil
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 06/20/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 326da32f91b263bbd09a4c6f521c9ec72094820c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37066075"
---
<a name="virtual-networking-limits-classic"></a>Följande begränsningar gäller endast för nätverksresurser som hanteras via den klassiska distributionsmodellen per prenumeration. Lär dig hur du [visa din aktuella resursanvändningen mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md).

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |50 |100 |
| Lokala nätverksplatser |20 |kontakta supporten |
| DNS-servrar per virtuellt nätverk |20 |100 |
| Privata IP-adresser per virtuellt nätverk |4096 |4096 |
| Samtidiga TCP eller UDP flödar per nätverkskort på en virtuell dator eller rollinstans |500 000 |500 000 |
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
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration. Lär dig hur du [visa din aktuella resursanvändningen mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md).

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |50 |1000 |
| Undernät per virtuellt nätverk |1000 |10000 |
| Peerkopplingar mellan virtuella nätverk per virtuellt nätverk |10 |50 |
| DNS-servrar per virtuellt nätverk |9 |25 |
| Privata IP-adresser per virtuellt nätverk |16384 ** |16384 |
| Privata IP-adresser per nätverksgränssnitt |256 |256 |
| Samtidiga TCP eller UDP flödar per nätverkskort på en virtuell dator eller rollinstans |500 000 |500 000 |
| Nätverksgränssnitt (NIC) |24000 ** |24000 |
| Nätverkssäkerhetsgrupper (NSG) |100 |5000 |
| NSG-regler per NSG |1000 ** |1000 |
| IP-adresser och intervall som angetts för källan eller målet i en säkerhetsgrupp |2000 |4000 |
| Programsäkerhetsgrupper |500 |3000 |
| Programmet säkerhetsgrupper per IP-konfiguration per NIC |10 |20 |
| IP-konfigurationer per program säkerhetsgrupp |1000 |4000 |
| Säkerhetsgrupper för program som kan anges i alla säkerhetsregler för en nätverkssäkerhetsgrupp |50 |100 |
| Användardefinierade vägtabeller |100 |200 |
| Användardefinierade vägar vägtabell |100 |400 |
| Offentliga IP-adresser – dynamisk |60 (grundläggande) |kontakta supporten |
| Offentliga IP-adresser – statiska |20 (grundläggande) |kontakta supporten |
| Offentliga IP-adresser – statiska |20 (standard) |kontakta supporten |
| Punkt-till-plats-rotcertifikat per VPN Gateway |20 |20 |

** Standard begränsningar gäller för prenumerationer som inte tidigare har dessa gränser ökat genom stöd

#### <a name="load-balancer"></a>Belastningsutjämnaren begränsar
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration. Lär dig hur du [visa din aktuella resursanvändningen mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md)

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Belastningsutjämning | 100 | 1000 |
| Regler per resurs, Basic | 150 | 250 |
| Regler per resurs, Standard | 1250 | 1500 |
| Regler per IP-konfiguration | 299 |299 |
| Frontend-IP-konfigurationer, Basic | 10 | 200 |
| Frontend-IP-konfigurationer, Standard | 10 | 600 |
| Serverdelspool Basic | 100, enkel Tillgänglighetsuppsättning | 100, enkel Tillgänglighetsuppsättning |
| Serverdelspool, Standard | 1000, enkel VNet | 1000, enkel VNet |
| Serverdelsresurser per belastningsutjämnare, Standard &ast; | 50 | 150 |
| Hög tillgänglighet portar, Standard | 1 per interna klientdel | 1 per interna klientdel |

&ast; Upp till 150 resurser, valfri kombination av fristående virtuella datorer, tillgänglighetsuppsättningar och skalningsuppsättningar i virtuella datorer.

[Kontakta supporten](../articles/azure-supportability/resource-manager-core-quotas-request.md ) om du måste öka gränserna från standardvärdet.

