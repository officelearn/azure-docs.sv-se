---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839746"
---
| Port nr.| In eller ut | Port-scope| Krävs| Anteckningar |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|I|LAN|Ja|Den här porten används för att ansluta till REST-API:er för lagring av dataruta blogg över HTTP. Om du inte ansluter till REST-API:er omdirigeras detta automatiskt till det lokala webbgränssnittet över 8443. |
| TCP 443 (HTTPS)|I|LAN|Ja|Den här porten används för att ansluta till REST-API:er för lagring av Data Box Blog via HTTPS. Om du inte ansluter till REST-API:er omdirigeras detta automatiskt till det lokala webbgränssnittet över 8443. |
| TCP 8443 (HTTPS-Alt)|I|LAN|Ja|Detta är en alternativ port för HTTPS och används vid anslutning till lokalt webbgränssnitt för enhetshantering. |
| TCP 445 (SMB)|Ut/In|LAN|I vissa fall<br>Se anteckningar|Den här porten krävs endast om du ansluter via SMB. |
| TCP 2049 (NFS)|Ut/In|LAN|I vissa fall<br>Se anteckningar|Den här porten krävs endast om du ansluter via NFS. |
| TCP 111 (NFS)|Ut/In|LAN|I vissa fall<br>Se anteckningar|Den här porten används för rpcbind/port mapping och krävs endast om du ansluter via NFS.  |
