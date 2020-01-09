---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436993"
---
|  | **Privat Peering** | **Microsoft-Peering** |  **Offentlig Peering** (inaktuellt för nya kretsar) |
| --- | --- | --- | --- |
| **Max. # prefix som stöds per peering** |4000 som standard 10 000 med ExpressRoute Premium |200 |200 |
| **IP-adressintervall som stöds** |Alla giltiga IP-adress inom ditt WAN. |Offentliga IP-adresser som ägs av dig eller din anslutningsleverantör. |Offentliga IP-adresser som ägs av dig eller din anslutningsleverantör. |
| **SOM antal krav** |Privata och offentliga AS-nummer. Du måste äga allmänheten som tal om du väljer att använda en. |Privata och offentliga AS-nummer. Du måste dock bevisa ägarskapet för offentliga IP-adresser. |Privata och offentliga AS-nummer. Du måste dock bevisa ägarskapet för offentliga IP-adresser. |
| **IP-protokoll som stöds**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Routing gränssnitts-IP-adresser** |RFC1918 och offentliga IP-adresser |Offentliga IP-adresser registrerade åt dig i routningsregister. |Offentliga IP-adresser registrerade åt dig i routningsregister. |
| **MD5-Hash-stöd** |Ja |Ja |Ja |