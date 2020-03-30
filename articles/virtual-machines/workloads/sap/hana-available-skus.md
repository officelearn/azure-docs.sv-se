---
title: SKU:er för SAP HANA på Azure (Stora instanser) | Microsoft-dokument
description: SKU:er för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617264"
---
# <a name="available-skus-for-hli"></a>Tillgängliga SKU:er för HLI

SAP HANA på Azure-tjänsten (Stora instanser) baserat på Revision 3-stämplar är tillgänglig i flera konfigurationer i Azure-regionerna i:

- USA, västra
- USA, östra
- Australien, östra
- Australien, sydöstra
- Europa, västra
- Europa, norra
- Japan, östra
- Japan, västra

SAP HANA på Azure-tjänsten (Stora instanser) baserat på Revision 4-stämplar är tillgänglig i flera konfigurationer i Azure-regionerna i:

- USA, västra 2
- USA, östra
- USA, södra centrala
- Europa, västra
- Europa, norra



[SAP HANA-certifierade SKU:er för stora haninstanserlista](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) som:

| SAP-lösning | Processor | Minne | Lagring | Tillgänglighet |
| --- | --- | --- | --- | --- |
| Optimerad för OLAP: SAP BW, BW/4HANA<br /> eller SAP HANA för allmän OLAP-arbetsbelastning | SAP HANA på Azure S72<br /> – 2 x Intel® Xeon®-processor E7-8890 v3<br /> 36 CPU-kärnor och 72 CPU-trådar |  768 GB |  3 TB | Erbjuds inte längre |
| --- | SAP HANA på Azure S144<br /> – 4 x Intel® Xeon®-processor E7-8890 v3<br /> 72 CPU-kärnor och 144 CPU-trådar |  1,5 TB |  6 TB | Erbjuds inte längre |
| --- | SAP HANA på Azure S192<br /> – 4 x Intel® Xeon®-processor E7-8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar |  2,0 TB |  8 TB | Erbjuds inte längre |
| --- | SAP HANA på Azure S224<br /> – 4 x Intel® Xeon® Platinum 8276-processor (även känd som Cascade lake)<br /> 112 CPU-kärnor och 224 CPU-trådar |  3,0 TB |  6,3 TB | Finns i Revision3 och Revision4 frimärken  |
| --- | SAP HANA på Azure S384<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  4,0 TB |  16 TB | Finns i Revision4 frimärken |
| Optimerad för OLTP: SAP Business Suite<br /> på SAP HANA eller S/4HANA (OLTP),<br /> generisk OLTP | SAP HANA på Azure S72m<br /> – 2 x Intel® Xeon®-processor E7-8890 v3<br /> 36 CPU-kärnor och 72 CPU-trådar |  1,5 TB |  6 TB | Erbjuds inte längre |
|---| SAP HANA på Azure S144m<br /> – 4 x Intel® Xeon®-processor E7-8890 v3<br /> 72 CPU-kärnor och 144 CPU-trådar |  3,0 TB |  12 TB | Erbjuds inte längre |
|---| SAP HANA på Azure S192m<br /> – 4 x Intel® Xeon®-processor E7-8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar  |  4,0 TB |  16 TB | Erbjuds inte längre |
| --- | SAP HANA på Azure S224m<br /> – 4 x Intel® Xeon® Platinum 8276-processor (även känd som Cascade lake)<br /> 112 CPU-kärnor och 224 CPU-trådar |  6,0 TB |  10,5 TB | Finns i Revision3 och Revision4 frimärken  |
|---| SAP HANA på Azure S384m<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  6,0 TB |  18 TB | Finns i Revision4 frimärken|
|---| SAP HANA på Azure S384xm<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  8,0 TB |  22 TB |  Finns i Revision4 frimärken |
|---| SAP HANA på Azure S576m<br /> – 12 x Intel® Xeon®-processor E7-8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  12,0 TB |  28 TB | Finns i Revision4 frimärken|
|---| SAP HANA på Azure S768m<br /> – 16 x Intel® Xeon®-processor E7-8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  16,0 TB |  36 TB | Finns i Revision4 frimärken|
|---| SAP HANA på Azure S960m<br /> – 20 x Intel® Xeon®-processor E7-8890 v4<br /> 480 CPU-kärnor och 960 CPU-trådar |  20,0 TB |  46 TB | Finns i Revision4 frimärken|


Under SAP HANA TDIv5 tillåter SAP kundspecifika storleks- och kundspecifika projekt, vilket kan leda till serverkonfigurationer som inte anges som certifierade i:

- [SAP HANA-certifierade apparater](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

I många fall har dessa kundspecifika serverkonfigurationer mer minne än de serverenheter som certifierats med SAP. I arbetet med SAP har kunderna möjlighet att få SAP-support och certifiera för sina kundspecifika serverkonfigurationer. I Azure är följande HANA-standard-SKU:er för stora instanser tillgängliga och i Microsofts prislista för sådana TDIv5 kundspecifika storleksprojekt.

| SKU|Processor | Minne | Lagring | Tillgänglighet |
| ---| --- | --- | --- | --- |
| S96 (S96) | SAP HANA på Azure S96<br /> – 2 x Intel® Xeon®-processor E7-8890 v4<br /> 48 CPU-kärnor och 96 CPU-trådar |  768 GB |  3 TB | Finns i Revision3 och Revision4 frimärken|


| Original SKU som kan <br /> utökas i minnet | Processor | Minne | Lagring | Tillgänglighet |
| --- | --- | --- | --- | --- |
| S192m kan utökas till att | SAP HANA på Azure S192xm<br /> – 4 x Intel® Xeon®-processor E7-8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar |  6,0 TB |  16 TB | Erbjuds inte längre |
| S384xm kan utökas till att | SAP HANA på Azure S384xxm<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  12,0 TB |  28 TB | Finns i Revision4 frimärken |
| S576m kan utökas till att | SAP HANA på Azure S576xm<br /> – 12 x Intel® Xeon®-processor E7-8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  18,0 TB |  41 TB | Finns i Revision4 frimärken|
| S768m kan utökas till att | SAP HANA på Azure S768xm<br /> – 16 x Intel® Xeon®-processor E7-8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  24,0 TB |  56 TB | Finns i Revision4 frimärken |

- CPU-kärnor = summan av icke-hyper-threaded CPU-kärnor av summan av processorer av serverenheten.
- CPU-trådar = summan av beräkningstrådar som tillhandahålls av hypertrådade CPU-kärnor av summan av processorerna för serverenheten. De flesta enheter är konfigurerade som standard för att använda hypertrådsteknik.
- Baserat på leverantörsrekommendationerna är S768m, S768xm och S960m inte konfigurerade för att använda Hyper-Threading för att köra SAP HANA.


De specifika konfigurationer som väljs är beroende av arbetsbelastning, CPU-resurser och önskat minne. Det är möjligt för OLTP-arbetsbelastningen att använda SKU:erna som är optimerade för OLAP-arbetsbelastningen. 

Maskinvarubasen för erbjudandena, förutom enheter för kundspecifika storleksprojekt, är SAP HANA TDI-certifierade. Två olika maskinvaruklasser delar upp SKU:erna i:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 och S224m som kallas "Typ I-klassen" av SKU: er.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm och S960m, som kallas "Typ II-klassen" av SKU: er.
- Om du är intresserad av andra S224 SKU:er som erbjuder från 4,5 TB till 9 TB med Optane kontaktar du ditt Microsoft-kontoteam för att få mer information. 


En komplett HANA Large Instance-stämpel allokeras inte enbart för en enskild kund&#39;s användning. Detta gäller även rack av beräknings- och lagringsresurser som är anslutna via en nätverksinfrastruktur som distribueras i Azure. Hana Large Instance-infrastruktur, till exempel &quot;&quot; Azure, distribuerar olika kundklienter som är isolerade från varandra i följande tre nivåer:

- **Nätverk:** Isolering via virtuella nätverk i stämpeln STORA HANA-instans.
- **Lagring**: Isolering via virtuella lagringsdatorer som har lagringsvolymer tilldelade och isolera lagringsvolymer mellan klienter.
- **Beräkning:** Dedikerad tilldelning av serverenheter till en enda klient. Ingen hård eller mjuk partitionering av serverenheter. Ingen delning av en enda server eller värdenhet mellan klienter. 

Distributionerna av HANA-enheter för stora instanser mellan olika klienter är inte synliga för varandra. Stora HANA-enheter som distribueras i olika klienter kan inte kommunicera direkt med varandra på stämpelnivån HANA Large Instance. Endast HANA Stora instansenheter inom en klient kan kommunicera med varandra på stämpelnivån FÖR stora hana-instanser.

En distribuerad klient i stämpeln Stor instans tilldelas en Azure-prenumeration för faktureringsändamål. För ett nätverk kan det nås från virtuella nätverk av andra Azure-prenumerationer inom samma Azure-registrering. Om du distribuerar med en annan Azure-prenumeration i samma Azure-region kan du också välja att be om en separerad HANA Large Instance-klientorganisation.

Det finns betydande skillnader mellan att köra SAP HANA på HANA Large Instance och SAP HANA som körs på virtuella datorer som distribueras i Azure:

- Det finns inget virtualiseringslager för SAP HANA på Azure (stora instanser). Du får prestandan hos den underliggande bare-metal hårdvaran.
- Till skillnad från Azure är SAP HANA på Azure -servern (Stora instanser) dedikerad till en viss kund. Det finns ingen möjlighet att en serverenhet eller värd är hård eller mjuk partitionerad. Därför används en HANA-enhet för stor instans som tilldelats som en helhet till en klient och med det till dig. En omstart eller avstängning av servern leder inte automatiskt till att operativsystemet och SAP HANA distribueras på en annan server. (För SKU:er av typ I är det enda undantaget om en server stöter på problem och omfördelning måste utföras på en annan server.)
- Till skillnad från Azure, där värdprocessortyper väljs för bästa pris/prestandaförhållande, är de processortyper som valts för SAP HANA på Azure (stora instanser) de som har högst resultat av Intel E7v3- och E7v4-processorlinjen.

**Nästa steg**
- Se [HLI-storlek](hana-sizing.md)
