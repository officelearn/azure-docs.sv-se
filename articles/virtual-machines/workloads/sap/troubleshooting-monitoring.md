---
title: Övervakning av SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Övervakare för SAP HANA på Azure (stora instanser).
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
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f046304121e0aed8efa1bbc2535d34186eba3496
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713715"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Så här övervakar du SAP HANA (stora instanser) på Azure

SAP HANA på Azure (stora instanser) skiljer sig från andra IaaS-distribution – du behöver övervaka vad Operativsystemet och programmet gör och hur program som använder följande resurser:

- Processor
- Minne
- Nätverkets bandbredd
- Diskutrymme

Med Azure virtuella datorer du behöver ta reda på om resursklasser som anges ovan är tillräckliga eller de få förbrukat. Här är mer information om var och en av de olika klasserna:

**CPU-användning för resursen:** Förhållandet mellan att SAP har definierats för vissa arbetsbelastningar mot HANA tillämpas för att se till att det ska finnas tillräckligt med CPU-resurser som är tillgängliga för att gå igenom de data som lagras i minnet. Dock kan det finnas fall där HANA förbrukar många processorer som köra frågor på grund av saknar index eller liknande problem. Det innebär att du ska övervaka CPU resurser som används för HANA stora instanser enhet samt CPU-resurser som används av de specifika HANA-tjänsterna.

**Minnesförbrukning:** Är viktigt att övervaka från inom HANA, samt utanför HANA på enheten. Övervaka hur data förbrukar allokerat minne för att hålla sig inom de nödvändiga storlek riktlinjerna för SAP HANA i HANA. Du bör också övervaka minnesförbrukning på stor instans-nivå om du vill se till att ytterligare installerade icke-HANA klientprogramvaran inte förbrukar för mycket minne och därför konkurrera med HANA om minne.

**Nätverkets bandbredd:** Azure VNet-gateway har begränsad bandbredd för data som flyttas till det virtuella nätverket i Azure, så är det bra att övervaka de data som tas emot av alla Azure virtuella datorer inom ett virtuellt nätverk för att ta reda på hur nära du kommer att gränserna för Azure-gateway SKU som du har valt. På stora HANA-instansen-enhet det vara klokt att övervaka inkommande och utgående nätverkstrafik samt och för att hålla reda på de volymer som hanteras med tiden.

**Diskutrymme:** Förbrukningen av diskutrymme ökar vanligtvis över tid. De vanligaste orsakerna är: datavolym ökar, körning av säkerhetskopieringar av transaktionsloggen kan lagra spårningsfiler och utför ögonblicksbilder av lagring. Det är därför viktigt att övervaka användning av diskutrymme och hantera det diskutrymme som är associerade med den stora HANA-instansen-enheten.

För den **typ II-SKU: er** av HANA stora instanser, servern medföljer förinlästa system diagnostiska verktyg. Du kan använda dessa diagnostiska verktyg för att utföra hälsokontroll system. Kör följande kommando för att genererar hälsotillstånd Kontrollera loggfilen på /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
När du arbetar med Microsoft Support-teamet för att felsöka ett problem kan du också bli ombedd att tillhandahålla filerna med hjälp av dessa diagnostiska verktyg. Du kan zip-filen med följande kommando.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Nästa steg**

- Se [övervaka SAP HANA (stora instanser) på Azure](troubleshooting-monitoring.md).