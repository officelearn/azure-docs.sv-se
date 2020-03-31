---
title: Azure VM-storlekar – minne | Microsoft-dokument
description: Visar en lista över de olika minnesoptimerade storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, datadiskar och nätverkskort samt lagringsdataflöde och nätverksbandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM-isolering,isolerad virtuell dator,isolering,isolerad
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77493532"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Minnesoptimerade storlekar för virtuella datorer

Minnesoptimerade VM-storlekar erbjuder ett högt förhållande mellan minne och processor som är bra för relationsdatabasservrar, medelstora till stora cacheminnen och analys i minnet. Den här artikeln innehåller information om antalet virtuella processorer, datadiskar och nätverkskort samt lagringsdataflöde och nätverksbandbredd för varje storlek i den här gruppningen.

- [Dv2 och DSv2-serien](dv2-dsv2-series-memory.md), en uppföljning av den ursprungliga D-serien, har en mer kraftfull CPU. Dv2-serien är ca 35% snabbare än D-serien. Den körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer, och med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

    Dv2- och DSv2-serien är idealiska för program som kräver snabbare virtuella processorer, bättre tillfällig lagringsprestanda eller har högre minnesbehov. De utgör en kraftfull kombination för många program i företagsklass.

- [Eav4- och Easv4-serien](eav4-easv4-series.md) använder AMD:s 2,35 Ghz EPYC<sup>TM</sup> 7452-processor i en flertrådad konfiguration med upp till 256 MB L3-cache, vilket ökar alternativen för att köra de flesta minnesoptimerade arbetsbelastningar. Eav4-serien och Easv4-serien har samma minnes- och diskkonfigurationer som Ev3 & Esv3-serien.

- [Intel®](ev3-esv3-series.md) Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) i en hypergängad konfiguration. ger ett bättre värdeerbjudande för de flesta allmänna arbetsbelastningar och anpassar Ev3 till de virtuella datorerna för det allmänna syftet i de flesta andra moln. Minnet har utökats (från 7 GiB/vCPU till 8 GiB/vCPU) medan disk- och nätverksgränser har justerats per kärna för att anpassa sig till övergången till hypertrådning. Ev3 är uppföljningen av det höga minnet VM storlekar av D / Dv2 familjer.

- [M-serien](m-series.md) erbjuder ett högt vCPU-antal (upp till 128 virtuella processorer) och en stor mängd minne (upp till 3,8 TiB). Det är också idealiskt för extremt stora databaser eller andra program som drar nytta av höga vCPU-antal och stora mängder minne.

- [Mv2-serien](mv2-series.md) erbjuder det högsta vCPU-antalet (upp till 416 vCPU: er) och största minne (upp till 8,19 TiB) av alla virtuella datorer i molnet. Serien är det perfekta valet för mycket stora databaser eller andra program som har nytta av många virtuella processorer och stora mängder minne.

Azure Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskinvarutyp och dedikerade till en enskild kund. Dessa storlekar för virtuella datorer är bäst lämpade för arbetsbelastningar som kräver en hög grad av isolering från andra kunder för arbetsbelastningar som involverar element som efterlevnad och lagstadgade krav. Kunder kan också välja att ytterligare dela upp resurserna för dessa isolerade virtuella datorer med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Se sidorna för familjer med virtuella datorer nedan för dina isolerade vm-alternativ.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.