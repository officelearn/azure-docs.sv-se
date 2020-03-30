---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187783"
---
| Port nr.| In eller ut | Port-scope| Krävs|   Anteckningar |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Ut|WAN |Inga|Utgående port används för internetåtkomst för att hämta uppdateringar. <br>Den utgående webbproxyn kan konfigureras av användaren. |
| TCP 443 (HTTPS)|Ut|WAN|Ja|Utgående port används för åtkomst till data i molnet.<br>Den utgående webbproxyn kan konfigureras av användaren.|
| UDP 123 (NTP)|Ut|WAN|I vissa fall<br>Se anteckningar|Den här porten krävs bara om du använder en Internetbaserad NTP-server.  |   
| UDP 53 (DNS)|Ut|WAN|I vissa fall<br>Se anteckningar|Den här porten krävs bara om du använder en Internetbaserad DNS-server.<br>Vi rekommenderar att du använder en lokal DNS-server. |
| TCP 5985 (WinRM)|Ut/In|LAN|I vissa fall<br>Se anteckningar|Den här porten krävs för att ansluta till enheten via fjärr-PowerShell via HTTP.  |
| UDP 67 (DHCP)|Ut|LAN|I vissa fall<br>Se anteckningar|Den här porten krävs bara om du använder en lokal DHCP-server.  |
| TCP 80 (HTTP)|Ut/In|LAN|Ja|Den här porten är den inkommande porten för lokalt användargränssnitt på enheten för lokal hantering. <br>Åtkomst till det lokala användargränssnittet via HTTP omdirigeras automatiskt till HTTPS.  |
| TCP 443 (HTTPS)|Ut/In|LAN|Ja|Den här porten är den inkommande porten för lokalt användargränssnitt på enheten för lokal hantering. |
| TCP 445 (SMB)|I|LAN|I vissa fall<br>Se anteckningar|Denna port krävs endast om du ansluter via SMB. |
| TCP 2049 (NFS)|I|LAN|I vissa fall<br>Se anteckningar|Denna port krävs endast om du ansluter via NFS. |
