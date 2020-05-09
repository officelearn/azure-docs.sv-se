---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187783"
---
| Port nr.| In eller ut | Port omfång| Krävs|   Obs! |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Ut|WAN |Inga|Utgående port används för Internet åtkomst för att hämta uppdateringar. <br>Den utgående webbproxyn är användare konfigurerbar. |
| TCP 443 (HTTPS)|Ut|WAN|Ja|Utgående port används för att komma åt data i molnet.<br>Den utgående webbproxyn är användare konfigurerbar.|
| UDP 123 (NTP)|Ut|WAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du använder en Internetbaserad NTP-server.  |   
| UDP 53 (DNS)|Ut|WAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du använder en Internet-baserad DNS-server.<br>Vi rekommenderar att du använder en lokal DNS-server. |
| TCP 5985 (WinRM)|Ut/in|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs för att ansluta till enheten via fjärr-PowerShell över HTTP.  |
| UDP 67 (DHCP)|Ut|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du använder en lokal DHCP-server.  |
| TCP 80 (HTTP)|Ut/in|LAN|Ja|Den här porten är den inkommande porten för lokalt användar gränssnitt på enheten för lokal hantering. <br>Åtkomst till det lokala användar gränssnittet över HTTP omdirigeras automatiskt till HTTPS.  |
| TCP 443 (HTTPS)|Ut/in|LAN|Ja|Den här porten är den inkommande porten för lokalt användar gränssnitt på enheten för lokal hantering. |
| TCP 445 (SMB)|I|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du ansluter via SMB. |
| TCP 2049 (NFS)|I|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du ansluter via NFS. |
