---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75436993"
---
|  | **Privat peering** | **Microsoft-peering** |  **Offentlig peering** (inaktuell för nya kretsar) |
| --- | --- | --- | --- |
| **Bekräftat. antal prefix som stöds per peering** |4000 som standard 10 000 med ExpressRoute Premium |200 |200 |
| **IP-adressintervall som stöds** |En giltig IP-adress i WAN-nätverket. |Offentliga IP-adresser som ägs av dig eller anslutnings leverantören. |Offentliga IP-adresser som ägs av dig eller anslutnings leverantören. |
| **SOM nummer krav** |Privat och offentlig som tal. Du måste äga det offentliga AS-numret om du väljer att använda en. |Privat och offentlig som tal. Du måste dock bevisa ägarskap för offentliga IP-adresser. |Privat och offentlig som tal. Du måste dock bevisa ägarskap för offentliga IP-adresser. |
| **IP-protokoll som stöds**| IPv4 |  IPv4, IPv6 | IPv4 |
| **IP-adresser för routningsgränssnitt** |RFC1918 och offentliga IP-adresser |Offentliga IP-adresser som registrerats för dig i vägvals register. |Offentliga IP-adresser som registrerats för dig i vägvals register. |
| **Stöd för MD5-hash** |Ja |Ja |Ja |