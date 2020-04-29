---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334689"
---
| Resurs | Gräns | Obs! |
| --- | --- | --- |
| Azure Application Gateway |1 000 per prenumeration | |
| IP-konfigurationer på klient Sidan |2 |1 offentlig och 1 privat |
| Klient dels portar |100<sup>1</sup> | |
| Backend-adresspooler |100<sup>1</sup> | |
| Backend-servrar per pool |1200 | |
| HTTP-lyssnare |200<sup>1</sup> |Begränsad till 100 aktiva lyssnare som dirigerar trafik. Aktiva lyssnare = totalt antal lyssnare-lyssnare som inte är aktiva.<br>Om en standard konfiguration i en routningsprincip är inställd på att dirigera trafik (till exempel har den en lyssnare, en backend-pool och HTTP-inställningar) som också räknas som en lyssnare.|
| Regler för belastnings utjämning i HTTP |100<sup>1</sup> | |
| HTTP-inställningar på Server Sidan |100<sup>1</sup> | |
| Instanser per gateway |V1 SKU – 32<br>V2 SKU – 125 | |
| SSL-certifikat |100<sup>1</sup> |1 per HTTP-lyssnare |
| Maximal storlek för SSL-certifikat |V1 SKU – 10 KB<br>V2 SKU – 16 KB| |
| Autentiseringscertifikat |100 | |
| Betrodda rotcertifikat |100 | |
| Lägsta timeout för begäran |1 sekund | |
| Maximal timeout för begäran |24 timmar | |
| Antal platser |100<sup>1</sup> |1 per HTTP-lyssnare |
| URL-kartor per lyssnare |1 | |
| Maximalt antal Sök vägs baserade regler per URL-mappning|100||
| Omdirigera konfigurationer |100<sup>1</sup>| |
| Samtidiga WebSocket-anslutningar |20 000 för medium Gateway<br> Stora gatewayer 50 000| |
| Maximal URL-längd|32 KB| |
| Maximal sidhuvud storlek för HTTP/2 |4KB| |
| Maximal fil överförings storlek, standard |2 GB | |
| Maximal storlek för fil överföring WAF |V1 mellan WAF-gatewayer, 100 MB<br>V1 stora WAF-gatewayer, 500 MB<br>V2 WAF, 750 MB| |
| Storleks gräns för WAF-Body, utan filer|128 kB||
| Maximalt antal anpassade WAF-regler|100||
| Maximalt antal WAF-undantag|100||

<sup>1</sup> i händelse av WAF-aktiverade SKU: er rekommenderar vi att du begränsar antalet resurser till 40 för optimala prestanda.
