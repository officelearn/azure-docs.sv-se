---
title: Kompatibilitetsmatris för operativsystem för SAP HANA (stora instanser)| Microsoft-dokument
description: Kompatibilitetsmatrisen representerar kompatibiliteten för olika versioner av operativsystemet med olika maskinvarutyper (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675643"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Kompatibla operativsystem för STORA HANA-instanser

## <a name="hana-large-instance-type-i"></a>HANA Stor instans typ I     
  | Operativsystem | Tillgänglighet        | SKU: er                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Erbjuds inte längre | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Tillgängligt           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Tillgängligt           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  
### <a name="persistent-memory-skus"></a>Beständiga minnesscku:er
  | Operativsystem | Tillgänglighet | SKU: er                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Tillgängligt    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA Stor instans typ II     
  |  Operativsystem       | Tillgänglighet        | SKU: er                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Erbjuds inte längre | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Tillgängligt           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Relaterade dokument

- Om du vill veta mer om [Tillgängliga SKU:er](hana-available-skus.md)
- Så här vet du om [att uppgradera operativsystemet](os-upgrade-hana-large-instance.md)
  

  
  
