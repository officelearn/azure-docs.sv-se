---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839746"
---
| Porten Nej.| In eller ut | Port omfång| Krävs| Anteckningar |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|I|LAN|Ja|Den här porten används för att ansluta till Data Box Blog storage REST API: er via HTTP. Om du inte ansluta till REST API: er kan omdirigerar detta automatiskt till lokala webbgränssnittet över 8443. |
| TCP 443 (HTTPS)|I|LAN|Ja|Den här porten används för att ansluta till Data Box Blog storage REST API: er via HTTPS. Om du inte ansluta till REST API: er kan omdirigerar detta automatiskt till lokala webbgränssnittet över 8443. |
| TCP 8443 (HTTPS-Alt)|I|LAN|Ja|Detta är en alternativ port för HTTPS och används när du ansluter till lokala webbgränssnittet för enhetshantering. |
| TCP 445 (SMB)|Out/i|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du ansluter via SMB. |
| TCP 2049 (NFS)|Out/i|LAN|I vissa fall<br>Se information|Den här porten krävs endast om du ansluter via NFS. |
| TCP 111 (NFS)|Out/i|LAN|I vissa fall<br>Se information|Den här porten används för rpcbind/portmappning och krävs endast om du ansluter via NFS.  |
