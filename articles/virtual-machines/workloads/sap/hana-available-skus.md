---
title: 'SKU: er för SAP HANA på Azure (stora instanser) | Microsoft Docs'
description: 'SKU: er för SAP HANA på Azure (stora instanser).'
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
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617264"
---
# <a name="available-skus-for-hli"></a>Tillgängliga SKU:er för HLI

SAP HANA på Azure-tjänsten (stora instanser) som baseras på revision 3-stämplar finns i flera konfigurationer i Azure-regionerna i:

- USA, västra
- USA, östra
- Australien, östra
- Australien, sydöstra
- Europa, västra
- Europa, norra
- Japan, östra
- Japan, västra

SAP HANA på Azure-tjänsten (stora instanser) som baseras på revision 4-stämplar finns i flera konfigurationer i Azure-regionerna i:

- USA, västra 2
- USA, östra
- USA, södra centrala
- Europa, västra
- Europa, norra



[SAP HANA Certified SKU: er av Hana stor instances](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) -lista som:

| SAP-lösning | Processor | Minne | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- |
| Optimerad för OLAP: SAP BW, BW/4HANA<br /> eller SAP HANA för allmän OLAP-arbetsbelastning | SAP HANA på Azure-S72<br /> – 2 x Intel® Xeon®-processor E7-8890 v3<br /> 36 CPU-kärnor och 72 CPU-trådar |  768 GB |  3 TB | Tillhandahålls inte längre |
| --- | SAP HANA på Azure-S144<br /> – 4 x Intel® Xeon®-processor E7-8890 v3<br /> 72 CPU-kärnor och 144 CPU-trådar |  1,5 TB |  6 TB | Tillhandahålls inte längre |
| --- | SAP HANA på Azure-S192<br /> – 4 x Intel® Xeon®-processor E7 – 8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar |  2,0 TB |  8 TB | Tillhandahålls inte längre |
| --- | SAP HANA på Azure-S224<br /> – 4 x Intel® Xeon® Platinum 8276-processor (kallas även för överlappande sjö)<br /> 112 CPU-kärnor och 224 CPU-trådar |  3,0 TB |  6,3 TB | Tillgängligt i Revision3-och Revision4-stämplar  |
| --- | SAP HANA på Azure-S384<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  4,0 TB |  16 TB | Tillgängligt i Revision4-stämplar |
| Optimerad för OLTP: SAP Business Suite<br /> på SAP HANA eller S/4HANA (OLTP),<br /> allmän OLTP | SAP HANA på Azure-S72m<br /> – 2 x Intel® Xeon®-processor E7-8890 v3<br /> 36 CPU-kärnor och 72 CPU-trådar |  1,5 TB |  6 TB | Tillhandahålls inte längre |
|---| SAP HANA på Azure-S144m<br /> – 4 x Intel® Xeon®-processor E7-8890 v3<br /> 72 CPU-kärnor och 144 CPU-trådar |  3,0 TB |  12 TB | Tillhandahålls inte längre |
|---| SAP HANA på Azure-S192m<br /> – 4 x Intel® Xeon®-processor E7 – 8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar  |  4,0 TB |  16 TB | Tillhandahålls inte längre |
| --- | SAP HANA på Azure-S224m<br /> – 4 x Intel® Xeon® Platinum 8276-processor (kallas även för överlappande sjö)<br /> 112 CPU-kärnor och 224 CPU-trådar |  6,0 TB |  10,5 TB | Tillgängligt i Revision3-och Revision4-stämplar  |
|---| SAP HANA på Azure-S384m<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  6,0 TB |  18 TB | Tillgängligt i Revision4-stämplar|
|---| SAP HANA på Azure-S384xm<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  8,0 TB |  22 TB |  Tillgängligt i Revision4-stämplar |
|---| SAP HANA på Azure-S576m<br /> – 12 x Intel® Xeon®-processor E7 – 8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  12,0 TB |  28 TB | Tillgängligt i Revision4-stämplar|
|---| SAP HANA på Azure-S768m<br /> – 16 x Intel® Xeon®-processor E7-8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  16,0 TB |  36 TB | Tillgängligt i Revision4-stämplar|
|---| SAP HANA på Azure-S960m<br /> – 20 x Intel® Xeon®-processor E7-8890 v4<br /> 480 CPU-kärnor och 960 CPU-trådar |  20,0 TB |  46 TB | Tillgängligt i Revision4-stämplar|


Under SAP HANA TDIv5 kan SAP använda kundspecifika storleks-och kundspecifika projekt, vilket kan leda till serverkonfigurationer, som inte listas som certifierat i:

- [SAP HANA certifierade apparater](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

I många fall har de här kundspecifika serverkonfigurationer mer minne än de Server enheter som certifierats med SAP. I arbetet med SAP har kunderna möjlighet att få SAP-support och certifiera sig för kundspecifika konfigurationer för storleks Server. I Azure är följande HANA stor instans standard SKU: er tillgängliga och i Microsoft pris listan för sådana TDIv5-kundspecifika storleks projekt.

| SKU|Processor | Minne | Storage | Tillgänglighet |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA på Azure-S96<br /> – 2 x Intel® Xeon®-processor E7-8890 v4<br /> 48 CPU-kärnor och 96 CPU-trådar |  768 GB |  3 TB | Tillgängligt i Revision3-och Revision4-stämplar|


| Ursprunglig SKU som kan vara <br /> utökad i minnet | Processor | Minne | Storage | Tillgänglighet |
| --- | --- | --- | --- | --- |
| S192m kan utökas till | SAP HANA på Azure-S192xm<br /> – 4 x Intel® Xeon®-processor E7 – 8890 v4<br /> 96 CPU-kärnor och 192 CPU-trådar |  6,0 TB |  16 TB | Tillhandahålls inte längre |
| S384xm kan utökas till | SAP HANA på Azure-S384xxm<br /> – 8 x Intel® Xeon®-processor E7-8890 v4<br /> 192 CPU-kärnor och 384 CPU-trådar |  12,0 TB |  28 TB | Tillgängligt i Revision4-stämplar |
| S576m kan utökas till | SAP HANA på Azure-S576xm<br /> – 12 x Intel® Xeon®-processor E7 – 8890 v4<br /> 288 CPU-kärnor och 576 CPU-trådar |  18,0 TB |  41 TB | Tillgängligt i Revision4-stämplar|
| S768m kan utökas till | SAP HANA på Azure-S768xm<br /> – 16 x Intel® Xeon®-processor E7-8890 v4<br /> 384 CPU-kärnor och 768 CPU-trådar |  24,0 TB |  56 TB | Tillgängligt i Revision4-stämplar |

- PROCESSOR kärnor = summan av icke-processorers processor kärnor som summan av processorerna för server enheten har.
- PROCESSOR trådar = summan av de beräknings trådar som tillhandahålls av processor kärnor i Hyper-Threading av summan av processorerna för server enheten. De flesta enheter är konfigurerade som standard för att använda Hyper-Threading-teknik.
- Baserat på leverantörs rekommendationer S768m, S768xm och S960m har inte kon figurer ATS för att använda Hyper-Threading för att köra SAP HANA.


De angivna konfigurationerna är beroende av arbets belastning, processor resurser och önskat minne. Det är möjligt för OLTP-arbetsbelastningen att använda de SKU: er som är optimerade för OLAP-arbetsbelastningen. 

Maskin varu basen för erbjudandena, förutom enheter för kundspecifika storleks projekt, är SAP HANA TDI-certifierad. Två olika klasser av maskin vara delar in SKU: er i:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 och S224m som kallas "typ I-klass" för SKU: er.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm och S960m, som kallas "typ II-klass" i SKU: er.
- Om du är intresse rad av andra S224 SKU-erbjudanden från 4,5 TB till 9TB med Optane kan du kontakta Microsoft-konto-teamet för att få mer information. 


En fullständig HANA-stor instans stämpel är inte helt allokerad för&#39;en enskild kund s användning. Detta faktum gäller för rack och lagrings resurser som är anslutna via en nätverks infrastruktur som också distribueras i Azure. HANA stor instans infrastruktur, som Azure, distribuerar olika kund &quot;klienter&quot; som är isolerade från varandra i följande tre nivåer:

- **Nätverk**: isolera via virtuella nätverk inom den Hana-stora instans stämplingen.
- **Lagring**: isolering genom virtuella lagrings datorer som har lagrings volymer tilldelade och isolerar lagrings volymer mellan klienter.
- **Compute**: dedikerad tilldelning av Server enheter till en enda klient. Ingen hård disk eller mjuk partitionering av Server enheter. Ingen delning av en enskild server eller värd enhet mellan klienter. 

Distributioner av HANA-stora instans enheter mellan olika klienter är inte synliga för varandra. HANA-stora instans enheter som distribueras i olika klienter kan inte kommunicera direkt med varandra på nivån HANA stor instans Stamp. Endast HANA stora instans enheter inom en klient organisation kan kommunicera med varandra på nivån HANA-stor instans stämpel.

En distribuerad klient i den stora instans stämpeln tilldelas till en Azure-prenumeration för fakturerings syfte. För ett nätverk kan det nås från virtuella nätverk av andra Azure-prenumerationer inom samma Azure-registrering. Om du distribuerar med en annan Azure-prenumeration i samma Azure-region, kan du också välja att be om en kommaavgränsad instans av HANA-stor instans.

Det finns betydande skillnader mellan att köra SAP HANA på HANA stor instans och SAP HANA som körs på virtuella datorer som distribueras i Azure:

- Det finns inget Virtualization-lager för SAP HANA på Azure (stora instanser). Du får prestanda för den underliggande maskin varan utan operativ system.
- Till skillnad från Azure är SAP HANA på Azure-servern (stora instanser) dedikerad till en specifik kund. Det finns ingen risk för att en server enhet eller värd är hårt partitionerad. Det innebär att en HANA-stor instans enhet används som tilldelad som helhet till en klient organisation och med det till dig. En omstart eller avstängning av servern leder inte automatiskt till operativ systemet och SAP HANA distribueras på en annan server. (För typ I-klass-SKU: er är det enda undantaget om en server stöter på problem och omdistribution måste utföras på en annan server.)
- Till skillnad från Azure, där värd processor typer väljs för bästa pris/prestanda-kvot, är de processor typer som väljs för SAP HANA på Azure (stora instanser) den högsta utförandet av processor linjen för Intel E7v3 och E7v4.

**Nästa steg**
- Referera [HLI storlek](hana-sizing.md)
