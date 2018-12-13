---
title: 'SKU: er för SAP HANA på Azure (stora instanser) | Microsoft Docs'
description: 'SKU: er för SAP HANA på Azure (stora instanser).'
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a62c30235fdd262f0df5937b6a829382d2b45ef
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52866295"
---
# <a name="available-skus-for-hli"></a>Tillgängliga SKU: er för HLI

SAP HANA på Azure (stora instanser) service är tillgänglig i flera konfigurationer i Azure-regioner i västra USA och östra USA, Östra Australien, sydöstra Australien, Västeuropa, Nordeuropa, östra Japan och västra Japan.

[SKU: er för HANA – stora instanser av SAP HANA-certifierade](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lista som:

| SAP-lösning | Processor | Minne | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- |
| Optimerat för OLAP: SAP BW, BW/4HANA<br /> eller SAP HANA för generisk OLAP-arbetsbelastning | SAP HANA på Azure S72<br /> – 2 x Intel® Xeon®-Processor E7-8890 v3<br /> 36 processorkärnor och 72 CPU-trådar |  768 GB |  3 TB | Tillgängligt |
| --- | SAP HANA på Azure S144<br /> – 4 x Intel® Xeon®-Processor E7-8890 v3<br /> 72 processorkärnor och 144 CPU-trådar |  1,5 TB |  6 TB | Erbjuds inte längre |
| --- | SAP HANA på Azure S192<br /> – 4 x Intel® Xeon®-Processor E7-8890 v4<br /> 96 processorkärnor och 192 CPU-trådar |  2.0 TB |  8 TB | Tillgängligt |
| --- | SAP HANA på Azure S384<br /> – 8 x Intel® Xeon®-Processor E7-8890 v4<br /> 192 processorkärnor och 384 CPU-trådar |  4.0 TB |  16 TB | Tillgängligt |
| Optimerat för OLTP: SAP Business Suite<br /> på SAP HANA eller S/4HANA (OLTP)<br /> generisk OLTP | SAP HANA på Azure S72m<br /> – 2 x Intel® Xeon®-Processor E7-8890 v3<br /> 36 processorkärnor och 72 CPU-trådar |  1,5 TB |  6 TB | Tillgängligt |
|---| SAP HANA på Azure S144m<br /> – 4 x Intel® Xeon®-Processor E7-8890 v3<br /> 72 processorkärnor och 144 CPU-trådar |  3.0 TB |  12 TB | Erbjuds inte längre |
|---| SAP HANA på Azure S192m<br /> – 4 x Intel® Xeon®-Processor E7-8890 v4<br /> 96 processorkärnor och 192 CPU-trådar  |  4.0 TB |  16 TB | Tillgängligt |
|---| SAP HANA på Azure S384m<br /> – 8 x Intel® Xeon®-Processor E7-8890 v4<br /> 192 processorkärnor och 384 CPU-trådar |  6.0 TB |  18 TB | Tillgängligt |
|---| SAP HANA på Azure S384xm<br /> – 8 x Intel® Xeon®-Processor E7-8890 v4<br /> 192 processorkärnor och 384 CPU-trådar |  8.0 TB |  22 TB |  Tillgängligt |
|---| SAP HANA på Azure S576m<br /> – 12 x Intel® Xeon®-Processor E7-8890 v4<br /> 288 processorkärnor och 576 CPU-trådar |  12.0 TB |  28 TB | Tillgängligt |
|---| SAP HANA på Azure S768m<br /> – 16 x Intel® Xeon®-Processor E7-8890 v4<br /> 384 processorkärnor och 768 CPU-trådar |  16,0 TB |  36 TB | Tillgängligt |
|---| SAP HANA på Azure S960m<br /> – 20 x Intel® Xeon®-Processor E7-8890 v4<br /> 480 processorkärnor och 960 CPU-trådar |  20.0 TB |  46 TB | Tillgängligt |


Under TDIv5 för SAP HANA kan SAP kundspecifika storlek och kundspecifika projekt som kan leda till serverkonfigurationer som inte är listade som certifierats i:

- [SAP HANA-certifierade enheter](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

I många fall har dessa kundspecifika serverkonfigurationer mer minne än de serverenheterna certifierad med SAP. Arbetar med SAP har kunderna möjlighet att få SAP support och certifiera för sina kundspecifika storlekar serverkonfigurationer. I Azure följande stora HANA-instansen standard SKU: er är tillgängliga och i Microsofts prislista för sådana TDIv5 kundspecifika storlek projekt.

| SKU|Processor | Minne | Storage | Tillgänglighet |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA på Azure S96<br /> – 2 x Intel® Xeon®-Processor E7-8890 v4<br /> 48 processorkärnor och 96 CPU-trådar |  768 GB |  3 TB | Tillgängligt |


| Ursprungliga SKU som kan vara <br /> utökade i minnet | Processor | Minne | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- |
| S192m kan utökas till | SAP HANA på Azure S192xm<br /> – 4 x Intel® Xeon®-Processor E7-8890 v4<br /> 96 processorkärnor och 192 CPU-trådar |  6.0 TB |  16 TB | Tillgängligt |
| S384xm kan utökas till | SAP HANA på Azure S384xxm<br /> – 8 x Intel® Xeon®-Processor E7-8890 v4<br /> 192 processorkärnor och 384 CPU-trådar |  12.0 TB |  28 TB | Tillgängligt |
| S576m kan utökas till | SAP HANA på Azure S576xm<br /> – 12 x Intel® Xeon®-Processor E7-8890 v4<br /> 288 processorkärnor och 576 CPU-trådar |  18.0 TB |  41 TB | Tillgängligt |
| S768m kan utökas till | SAP HANA på Azure S768xm<br /> – 16 x Intel® Xeon®-Processor E7-8890 v4<br /> 384 processorkärnor och 768 CPU-trådar |  24.0 TB |  56 TB | Tillgängligt |

- CPU-kärnor = summan av icke-hyper-threaded processorkärnor summan av processorer med server-enhet.
- CPU-trådar = summan av beräkning trådar som tillhandahålls av hyper-threaded processorkärnor summan av processorer med server-enhet. De flesta enheter är konfigurerade som standard att använda Hyper-Threading Technology.
- Utifrån leverantör rekommendationer S768m har S768xm och S960m inte konfigurerats för att använda Hyper-Threading för att köra SAP HANA.


Vilka konfigurationer valt är beroende av arbetsbelastning, CPU-resurser och önskad minne. Det är möjligt för arbetsbelastningen för OLTP som att använda SKU: er som är optimerade för OLAP-arbetsbelastning. 

Maskinvaran som bas för betalning, förutom enheter för kundspecifika storlek projekt är SAP HANA TDI-certifierade. Två olika klasser av maskinvara dela SKU: er till:

- S72, S72m, S96, S144, S144m, S192, S192m och S192xm, som kallas ”typen jag klassen” SKU: er.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm och S960m som kallas den ”Type II klass” SKU: er.

En fullständig stämpel för stora HANA-instansen inte är exklusivt allokeras för en enda kund&#39;s användning. Detta gäller för beräknings- och lagringsresurser som är anslutna via en nätverksinfrastruktur som distribueras i Azure samt serverrack. Infrastruktur för stora HANA-instansen, som Azure, distribuerar annan kund &quot;klienter&quot; som är isolerade från varandra i följande tre nivåer:

- **Nätverk**: isolering via virtuella nätverk i stämpeln stora HANA-instansen.
- **Storage**: isolering via storage virtuella datorer som har tilldelats lagringsvolymer och isolera lagringsvolymer mellan klienter.
- **Compute**: dedikerade tilldelningen av server-enheter till en enda klient. Inte hårt eller mjuk partitionering av server-enheter. Ingen delning av en enda enhet för servern eller värden mellan klienter. 

Distributioner av stora HANA-instansen enheter mellan olika klienter är inte synliga för varandra. Stora HANA-instansen enheter som distribueras i olika klienter kan inte kommunicera direkt med varandra på stora HANA-instansen stämpel nivå. Endast stora HANA-instansen enheter inom en klient kan kommunicera med varandra på stora HANA-instansen stämpel nivå.

En distribuerad klient i stor instans stämpeln är tilldelad till en Azure-prenumeration för fakturering. För ett nätverk, kan den nås från virtuella nätverk med andra Azure-prenumerationer i samma Azure-registrering. Om du distribuerar med en annan Azure-prenumeration i samma Azure-region, kan du också välja att be om en klient för avgränsas stora HANA-instansen.

Det finns betydande skillnader mellan att köra SAP HANA på stora HANA-instansen och SAP HANA som körs på virtuella datorer som distribueras i Azure:

- Det finns inga virtualiseringsskiktet för SAP HANA på Azure (stora instanser). Du får prestanda för den underliggande maskinvaran utan operativsystem.
- Till skillnad från Azure, är SAP HANA på Azure (stora instanser)-servern dedikerad till en viss kund. Det finns ingen risk att ett server-enhet eller en värddator är hårt eller mjuk partitionerade. Därför används en enhet för stora HANA-instansen som helhet till en klient och med som du tilldelats. En omstart eller avstängning av servern leda inte automatiskt till operativsystemet och SAP HANA som distribueras på en annan server. (För typen jag klass SKU: er, det enda undantaget är om en server påträffar problem och omdistribution måste utföras på en annan server.)
- Till skillnad från Azure, där värden processor väljs för förhållandet bästa pris/prestanda, är processor-typer som valts för SAP HANA på Azure (stora instanser) högsta prestanda av raden Intel E7v3 och E7v4 processor.

**Nästa steg**
- Se [HLI storlek](hana-sizing.md)
