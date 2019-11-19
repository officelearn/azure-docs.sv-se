---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 3b692ed697d69deca4c50a0595cc54251bac4990
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74175015"
---
| Resurs | Standard/högsta gräns | Obs! |
| --- | --- | --- |
| Azure Application Gateway |1 000 per prenumeration | |
| IP-konfigurationer på klient Sidan |2 |1 offentlig och 1 privat |
| Klient dels portar |100<sup>1</sup> | |
| Backend-adresspooler |100<sup>1</sup> | |
| Backend-servrar per pool |1,200 | |
| HTTP-lyssnare |100<sup>1</sup> | |
| Regler för belastnings utjämning i HTTP |100<sup>1</sup> | |
| HTTP-inställningar på Server Sidan |100<sup>1</sup> | |
| Instanser per gateway |32 | |
| SSL-certifikat |100<sup>1</sup> |1 per HTTP-lyssnare |
| Maximal storlek för SSL-certifikat |V1 SKU – 10 KB<br>V2 SKU – 16 KB| |
| Autentiseringscertifikat |100 | |
| Betrodda rot certifikat |100 | |
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
| Maximal storlek för fil överföring WAF |Medel WAF-gatewayer, 100 MB<br>Stora WAF-gatewayer, 500 MB| |
| Storleks gräns för WAF-Body, utan filer|128 KB||
| Maximalt antal anpassade WAF-regler|100||
| Maximalt antal WAF-undantag|100||

<sup>1</sup> i händelse av WAF-aktiverade SKU: er rekommenderar vi att du begränsar antalet resurser till 40 för optimala prestanda.
