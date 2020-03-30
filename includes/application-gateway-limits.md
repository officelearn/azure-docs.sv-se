---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334689"
---
| Resurs | Gräns | Obs! |
| --- | --- | --- |
| Azure Application Gateway |1 000 per abonnemang | |
| IP-konfigurationer för frontend |2 |1 offentlig och 1 privat |
| Front-end-portar |100<sup>1</sup> | |
| Backend-adresspooler |100<sup>1</sup> | |
| Backend-servrar per pool |1200 | |
| HTTP-lyssnare |200<sup>1</sup> |Begränsad till 100 aktiva lyssnare som dirigerar trafik. Aktiva lyssnare = totalt antal lyssnare - lyssnare är inte aktiva.<br>Om en standardkonfiguration i en routningsregel är inställd på dirigera trafik (till exempel har en lyssnare, en serverdpool och HTTP-inställningar) räknas det även som lyssnare.|
| HTTP-regler för belastningsutjämning |100<sup>1</sup> | |
| Http-inställningar för backend |100<sup>1</sup> | |
| Instanser per gateway |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL-certifikat |100<sup>1</sup> |1 per HTTP-lyssnare |
| Maximal SSL-certifikatstorlek |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| Autentiseringscertifikat |100 | |
| Betrodda rotcertifikat |100 | |
| Minsta timeout för begäran |1 sekund | |
| Maximalt tidsgränsen för begäran |24 timmar | |
| Antal platser |100<sup>1</sup> |1 per HTTP-lyssnare |
| URL-kartor per lyssnare |1 | |
| Maximala sökvägsbaserade regler per URL-mappning|100||
| Omdirigeringskonfigurationer |100<sup>1</sup>| |
| Samtidiga WebSocket-anslutningar |Medelstora gateways 20k<br> Stora gateways 50k| |
| Maximal URL-längd|32KB (3200000000| |
| Maximal rubrikstorlek för HTTP/2 |4KB (4KB)| |
| Maximal filuppladdningsstorlek, Standard |2 GB | |
| Maximal filuppladdningsstorlek WAF |V1 Medelstora WAF-gateways, 100 MB<br>V1 Stora WAF-gateways, 500 MB<br>V2 WAF, 750 MB| |
| WAF kroppsstorleksgräns, utan filer|128 kB||
| Högsta vanliga anpassade WAF-regler|100||
| Maximala WAF-undantag|100||

<sup>1</sup> När det gäller WAF-aktiverade SKU:er rekommenderar vi att du begränsar antalet resurser till 40 för optimal prestanda.
