---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67839746"
---
| Port nr.| In eller ut | Port omfång| Obligatorisk| Obs! |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|I|LAN|Ja|Den här porten används för att ansluta till Data Box-enhet blogg lagring REST-API: er via HTTP. Om du inte ansluter till REST API: er omdirigeras detta automatiskt till lokalt webb gränssnitt över 8443. |
| TCP 443 (HTTPS)|I|LAN|Ja|Den här porten används för att ansluta till Data Box-enhet blogg lagring REST-API: er över HTTPS. Om du inte ansluter till REST API: er omdirigeras detta automatiskt till lokalt webb gränssnitt över 8443. |
| TCP 8443 (HTTPS-Alt)|I|LAN|Ja|Detta är en alternativ port för HTTPS och används vid anslutning till lokalt webb gränssnitt för enhets hantering. |
| TCP 445 (SMB)|Ut/in|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du ansluter via SMB. |
| TCP 2049 (NFS)|Ut/in|LAN|I vissa fall<br>Se kommentarer|Den här porten krävs bara om du ansluter via NFS. |
| TCP 111 (NFS)|Ut/in|LAN|I vissa fall<br>Se kommentarer|Den här porten används för rpcbind/port-mappning och krävs endast om du ansluter via NFS.  |
