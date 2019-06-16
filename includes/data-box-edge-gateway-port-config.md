---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161083"
---
| Porten Nej.| In eller ut | Port omfång| Obligatoriskt|   Anteckningar |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|ut|WAN |Nej|Utgående port används för åtkomst till internet för att hämta uppdateringar. <br>Utgående webbproxy kan konfigureras av användaren. |
| TCP 443 (HTTPS)|ut|WAN|Ja|Utgående port används för att komma åt data i molnet.<br>Utgående webbproxy kan konfigureras av användaren.|
| UDP 123 (NTP)|ut|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en Internetbaserad NTP-server.  |   
| UDP 53 (DNS)|ut|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en internet-DNS-server.<br>Vi rekommenderar att du använder en lokal DNS-server. |
| TCP 5985 (WinRM)|Out/i|LAN|I vissa fall<br>Se information|Den här porten krävs för att ansluta till enheten via fjärr-PowerShell via HTTP.  |
| UDP 67 (DHCP)|ut|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en lokal DHCP-server.  |
| TCP 80 (HTTP)|Out/i|LAN|Ja|Den här porten är den inkommande porten för lokala Användargränssnittet på enheten för lokal hantering. <br>Åtkomst till lokala Användargränssnittet via HTTP omdirigeras automatiskt till HTTPS.  |
| TCP 443 (HTTPS)|Out/i|LAN|Ja|Den här porten är den inkommande porten för lokala Användargränssnittet på enheten för lokal hantering. |
| TCP 445 (SMB)|I|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du ansluter via SMB. |
| TCP 2049 (NFS)|I|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du ansluter via NFS. |
