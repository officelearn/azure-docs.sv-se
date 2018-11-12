---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 67de9042af11a2b17c4b65f8225ecd0580b95466
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263605"
---
| Porten Nej.| In eller ut | Port omfång| Krävs|   Anteckningar |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Utdata|WAN |Nej|Utgående port används för åtkomst till internet för att hämta uppdateringar. <br>Utgående webbproxy kan konfigureras av användaren. |
| TCP 443 (HTTPS)|Utdata|WAN|Ja|Utgående port används för att komma åt data i molnet.<br>Utgående webbproxy kan konfigureras av användaren.|   
| UDP 53 (DNS)|Utdata|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en internet-DNS-server.<br>Vi rekommenderar att du använder en lokal DNS-server. |
| UDP 123 (NTP)|Utdata|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en Internetbaserad NTP-server.  |
| UDP 67 (DHCP)|Utdata|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en DHCP-server.  |
| TCP 80 (HTTP)|I|LAN|Ja|Den här porten är den inkommande porten för lokala Användargränssnittet på enheten för lokal hantering. <br>Åtkomst till lokala Användargränssnittet via HTTP omdirigeras automatiskt till HTTPS.  |
| TCP 443 (HTTPS)|I|LAN|Ja|Den här porten är den inkommande porten för lokala Användargränssnittet på enheten för lokal hantering. |