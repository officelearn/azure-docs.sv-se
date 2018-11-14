---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 44f9400a1ecebd3c204ec0b891c5d7fe01bdfd25
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51628215"
---
| Resurs | Standardgräns | Obs! |
| --- | --- | --- |
| Application Gateway |1 000 per prenumeration | |
| IP-konfigurationer för frontend |2 |1 offentlig och 1 privat |
| Frontend-portar |40 | |
| Backend-adresspooler |40 | |
| Backend-servrar per pool |1200 | |
| HTTP-lyssnare |40 | |
| Regler för HTTP-belastningsutjämning |400 |Antal HTTP-lyssnare * n |
| HTTP-inställningar för backend |40 | |
| Instanser per gateway |75 | |
| SSL-certifikat |40 |1 per HTTP-lyssnare |
| Autentiseringscertifikat |40 | |
| Minsta timeout för begäran |1 sekund | |
| Högsta timeout för begäran |24 timmar | |
| Antal platser |20 |1 per HTTP-lyssnare |
| URL-mappningar per lyssnare |1 | |
| Maximal sökväg-baserade regler per URL: en karta|100|
| Omdirigerings-konfigurationer |40| |
| Samtidiga WebSocket-anslutningar |5000| |
|Maximal URL-längd|8000|
| Maximala storleken för filöverföring Standard |2 GB | |
| Maximal uppladdning storlek WAF |Medelhög WAF-gatewayer – 100 MB<br>Stora WAF-gatewayer – 500 MB| |
|Storleksgräns för WAF brödtext (utan-filer)|128 KB|
