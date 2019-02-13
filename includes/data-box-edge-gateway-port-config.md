---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2019
ms.author: alkohli
ms.openlocfilehash: 783ae29e9ca0c9a609d1d1d283525a952c2f7f33
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118713"
---
| Porten Nej.| In eller ut | Port omfång| Krävs|   Anteckningar |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Out/i|WAN  |Nej|Utgående port används för åtkomst till internet för att hämta uppdateringar. <br>Utgående webbproxy kan konfigureras av användaren. |
| TCP 443 (HTTPS)|Out/i|WAN|Ja|Utgående port används för att komma åt data i molnet.<br>Utgående webbproxy kan konfigureras av användaren.|
| UDP 123 (NTP)|Utdata|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en Internetbaserad NTP-server.  |   
| UDP 53 (DNS)|Utdata|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en internet-DNS-server.<br>Vi rekommenderar att du använder en lokal DNS-server. |
| TCP 5985 (WinRM)|Out/i|LAN|I vissa fall<br>Se information|Den här porten krävs för att ansluta till enheten via fjärr-PowerShell via HTTP.  |
| UDP 67 (DHCP)|Utdata|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en lokal DHCP-server.  |
| TCP 80 (HTTP)|Out/i|LAN|Ja|Den här porten är den inkommande porten för lokala Användargränssnittet på enheten för lokal hantering. <br>Åtkomst till lokala Användargränssnittet via HTTP omdirigeras automatiskt till HTTPS.  |
| TCP 443 (HTTPS)|Out/i|LAN|Ja|Den här porten är den inkommande porten för lokala Användargränssnittet på enheten för lokal hantering. |
| TCP 445 (SMB)|I|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du ansluter via SMB. |
| TCP 2049 (NFS)|I|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du ansluter via NFS. |

