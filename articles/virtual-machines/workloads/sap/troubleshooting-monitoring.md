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
ms.openlocfilehash: e5a5d462be5555090d1dfced5fa07c9b748eb312
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345666"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Så här övervakar du SAP HANA (stora instanser) på Azure

SAP HANA på Azure (stora instanser) skiljer sig från andra IaaS-distribution – du behöver övervaka vad Operativsystemet och programmet gör och hur program som använder följande resurser:

- Processor
- Minne
- Nätverkets bandbredd
- Diskutrymme

Med Azure virtuella datorer du behöver ta reda på om resursklasser som anges ovan är tillräckliga eller de få förbrukat. Här är mer information om var och en av de olika klasserna:

**CPU-resursförbrukning:** förhållandet som SAP har definierats för vissa arbetsbelastningar mot HANA tillämpas för att se till att det ska finnas tillräckligt med CPU-resurser som är tillgängliga för att gå igenom de data som lagras i minnet. Dock kan det finnas fall där HANA förbrukar många processorer som köra frågor på grund av saknar index eller liknande problem. Det innebär att du ska övervaka CPU resurser som används för HANA stora instanser enhet samt CPU-resurser som används av de specifika HANA-tjänsterna.

**Minnesförbrukning:** är viktigt att övervaka från inom HANA, samt utanför HANA på enheten. Övervaka hur data förbrukar allokerat minne för att hålla sig inom de nödvändiga storlek riktlinjerna för SAP HANA i HANA. Du bör också övervaka minnesförbrukning på stor instans-nivå om du vill se till att ytterligare installerade icke-HANA klientprogramvaran inte förbrukar för mycket minne och därför konkurrera med HANA om minne.

**Nätverkets bandbredd:** The Azure VNet-gateway har begränsad bandbredd för data som flyttas till Azure VNet, så är det bra att övervaka data tas emot av alla Azure virtuella datorer inom ett virtuellt nätverk för att ta reda på hur väl du är att gränserna för Azure-gatewayen SKU du markering å. På stora HANA-instansen-enhet det vara klokt att övervaka inkommande och utgående nätverkstrafik samt och för att hålla reda på de volymer som hanteras med tiden.

**Diskutrymme:** förbrukningen av diskutrymme vanligtvis ökar med tiden. De vanligaste orsakerna är: datavolym ökar, körning av säkerhetskopieringar av transaktionsloggen kan lagra spårningsfiler och utför ögonblicksbilder av lagring. Det är därför viktigt att övervaka användning av diskutrymme och hantera det diskutrymme som är associerade med den stora HANA-instansen-enheten.

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