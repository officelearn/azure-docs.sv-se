---
title: Övervakning av SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Övervaka SAP HANA på en Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50a6b4f15a7de02533e3bb51e5659f7b4c078b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617298"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Så här övervakar du SAP HANA (stora instanser) på Azure

SAP HANA på Azure (Stora instanser) skiljer sig inte från någon annan IaaS-distribution – du måste övervaka vad operativsystemet och programmet gör och hur programmen förbrukar följande resurser:

- Processor
- Minne
- Nätverksbandbredd
- Diskutrymme

Med Virtuella Azure-datorer måste du ta reda på om resursklasserna som nämns ovan är tillräckliga eller om de är uttömda. Här är mer information om var och en av de olika klasserna:

**Cpu-resursförbrukning:** Förhållandet som SAP definierade för viss arbetsbelastning mot HANA tillämpas för att se till att det bör finnas tillräckligt med CPU-resurser tillgängliga för att arbeta igenom de data som lagras i minnet. Det kan dock finnas fall där HANA förbrukar många processorer som kör frågor på grund av saknade index eller liknande problem. Det innebär att du bör övervaka CPU-resursförbrukning för HANA-enheten för stora instanser samt CPU-resurser som förbrukas av de specifika HANA-tjänsterna.

**Minnesförbrukning:** Är viktigt att övervaka inifrån HANA, såväl som utanför HANA på enheten. Inom HANA övervakar du hur data förbrukar HANA-allokerat minne för att hålla sig inom de riktlinjer för dimensionering som krävs för SAP. Du vill också övervaka minnesförbrukningen på den stora instansnivån för att se till att ytterligare installerad icke-HANA-programvara inte förbrukar för mycket minne och därför konkurrerar med HANA om minne.

**Nätverkets bandbredd:** Azure VNet-gatewayen är begränsad i bandbredd för data som flyttar till Azure VNet, så det är bra att övervaka data som tas emot av alla virtuella Azure-datorer i ett virtuella nätverk för att ta reda på hur nära du är gränserna för Azure gateway SKU du valt. På HANA Large Instance-enheten är det också meningsfullt att övervaka inkommande och utgående nätverkstrafik och att hålla reda på de volymer som hanteras över tid.

**Diskutrymme:** Förbrukningen av diskutrymme ökar vanligtvis med tiden. De vanligaste orsakerna är: datavolymen ökar, körning av säkerhetskopior av transaktionsloggar, lagring av spårningsfiler och lagringsögonblicksbilder. Därför är det viktigt att övervaka användningen av diskutrymme och hantera diskutrymmet som är associerat med HANA Large Instance-enheten.

För **skriv-II-SKU:erna** för DE STORA HANA-instanserna levereras servern med de förinstallerade systemdiagnostikverktygen. Du kan använda dessa diagnostiska verktyg för att utföra systemet hälsokontroll. Kör följande kommando för att generera loggfilen för hälsokontroll på /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
När du arbetar med Microsoft Support-teamet för att felsöka ett problem kan du också bli ombedd att ange loggfilerna med hjälp av dessa diagnostikverktyg. Du kan zippa filen med följande kommando.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Nästa steg**

- Se Så här övervakar du [SAP HANA (stora instanser) på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot).
