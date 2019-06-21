---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 9a8d925e5d72500817028b913a80255a4339255c
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148468"
---
| Resource | Standard-/ maxgräns | Obs! |
| --- | --- | --- |
| Azure Application Gateway |1 000 per prenumeration | |
| Frontend IP-konfigurationer |2 |1 offentlig och 1 privat |
| Klientportar |100<sup>1</sup> | |
| Backend-adresspooler |100<sup>1</sup> | |
| Backend-servrar per pool |1,200 | |
| HTTP-lyssnare |100<sup>1</sup> | |
| Regler för HTTP-belastningsutjämning |100<sup>1</sup> | |
| Backend-HTTP-inställningar |100<sup>1</sup> | |
| Instanser per gateway |32 | |
| SSL-certifikat |100<sup>1</sup> |1 per HTTP-lyssnare |
| Maxstorlek för SSL-certifikat |V1-SKU - 10 KB<br>V2-SKU - 16 KB| |
| Autentiseringscertifikat |100 | |
| Betrodda rotcertifikat |100 | |
| Minsta timeout för begäran |1 sekund | |
| Högsta timeout för begäran |24 timmar | |
| Antal platser |100<sup>1</sup> |1 per HTTP-lyssnare |
| URL-mappningar per lyssnare |1 | |
| Maximal sökväg-baserade regler per URL: en karta|100||
| Omdirigerings-konfigurationer |100<sup>1</sup>| |
| Samtidiga WebSocket-anslutningar |Medelhög gatewayer 20k<br> Stora gatewayer 50k| |
| Maximal URL-längd|8,000||
| Ladda upp för maximal filstorlek, Standard |2 GB | |
| Maximal uppladdning storlek WAF |Medelhög WAF-gatewayer, 100 MB<br>Stora WAF-gatewayer, 500 MB| |
| WAF storleksgräns brödtext, utan filer|128 KB||
|Maximal WAF anpassade regler|100||

<sup>1</sup> vid WAF-aktiverade SKU: er, rekommenderar vi att du begränsar antalet resurser till 40 för optimala prestanda.
