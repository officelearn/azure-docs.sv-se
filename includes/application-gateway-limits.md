---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211918"
---
| Resurs | Standardgräns | Obs! |
| --- | --- | --- |
| Application Gateway |1 000 per prenumeration | |
| Frontend IP-konfigurationer |2 |1 offentlig och 1 privat |
| Klientportar |100<sup>1</sup> | |
| Backend-adresspooler |100<sup>1</sup> | |
| Backend-servrar per pool |1200 | |
| HTTP-lyssnare |100<sup>1</sup> | |
| Regler för HTTP-belastningsutjämning |100<sup>1</sup> | |
| Backend-HTTP-inställningar |100<sup>1</sup> | |
| Instanser per gateway |32 | |
| SSL-certifikat |100<sup>1</sup> |1 per HTTP-lyssnare |
| Autentiseringscertifikat |100 | |
| Betrodda rotcertifikat |100 | |
| Minsta timeout för begäran |1 sekund | |
| Högsta timeout för begäran |24 timmar | |
| Antal platser |100<sup>1</sup> |1 per HTTP-lyssnare |
| URL-mappningar per lyssnare |1 | |
| Maximal sökväg-baserade regler per URL: en karta|100||
| Omdirigerings-konfigurationer |100<sup>1</sup>| |
| Samtidiga WebSocket-anslutningar |5000| |
| Maximal URL-längd|8000||
| Maximala storleken för filöverföring Standard |2 GB | |
| Maximal uppladdning storlek WAF |Medelhög WAF-gatewayer – 100 MB<br>Stora WAF-gatewayer – 500 MB| |
| Storleksgräns för WAF brödtext (utan-filer)|128 KB||

<sup>1</sup> vid WAF-aktiverade SKU: er rekommenderar vi att du begränsar antalet resurser till 40 för optimala prestanda.
