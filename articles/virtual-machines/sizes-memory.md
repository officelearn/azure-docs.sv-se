---
title: Virtuella Azure-datorer-storlekar – minne | Microsoft Docs
description: Visar en lista över de olika minnesoptimerade storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Isolering av virtuella datorer, isolerad virtuell dator, isolerad
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 5a20e9c64b6ef948167333b54b16b34e84dc0e32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273587"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Minnesoptimerade storlekar för virtuella datorer

Minnesoptimerade VM-storlekar erbjuder ett högt förhållande mellan minne och processor som är bra för Relations databas servrar, medel stora och stora cacheminnen och minnes intern analys. Den här artikeln innehåller information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för varje storlek i den här gruppen.

- [Dv2 och DSv2-serien](dv2-dsv2-series-memory.md), som är en uppföljning av den ursprungliga D-serien, har en mer kraftfull processor. Dv2-serien är cirka 35% snabbare än D-serien. Den&reg; körs på Intel xeon&reg; 8171M 2,1 GHz (Skylake) eller Intel&reg; Xeon&reg; E5-2673 v4 2,3 GHz (Broadwell) eller Intel&reg; Xeon&reg; E5-2673 v3 2,4 GHz-processorer (Haswell) och med Intel Turbo Boost-tekniken 2,0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

    Dv2 och DSv2-serien är idealiska för program som kräver snabbare virtuella processorer, bättre tillfälliga lagrings prestanda eller har högre minnes krav. De utgör en kraftfull kombination för många program i företagsklass.

- [Eav4 och Easv4-serien](eav4-easv4-series.md) använder AMD: s 2.35 GHz EPYC<sup>TM</sup> 7452-processor i en multi-threadd konfiguration med upp till 256 MB L3-cache, vilket ökar alternativen för att köra de flesta minnesoptimerade arbets belastningar. Eav4-serien och Easv4-serien har samma minnes-och diskkonfigurationer som Ev3 & Esv3-serien.

- [Ev3-och Esv3-serien](ev3-esv3-series.md) Intel&reg; Xeon&reg; 8171M 2,1 GHz (Skylake) eller intel&reg; Xeon&reg; E5-2673 v4 2,3 GHz-processorn (Broadwell) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar och ger Ev3 till justering med de flesta virtuella datorerna i de flesta andra moln. Minnet har utökats (från 7 GiB/vCPU till 8 GiB/vCPU) medan disk-och nätverks gränser har justerats per kärna för att justeras med flytten till Hyper-Threading. Ev3 är en uppföljning av de virtuella datorerna med hög minnes storlek för D/Dv2-familjer.

- [M-serien](m-series.md) erbjuder ett högt antal vCPU (upp till 128 virtuella processorer) och en stor mängd minne (upp till 3,8 TIB). Det är också idealiskt för extremt stora databaser eller andra program som drar nytta av höga vCPU-räkningar och stora mängder minne.

- [Mv2-serien](mv2-series.md) erbjuder det högsta antalet vCPU (upp till 416 virtuella processorer) och störst minne (upp till 11,4 TIB) för virtuella datorer i molnet. Det är idealiskt för extremt stora databaser eller andra program som drar nytta av höga vCPU-räkningar och stora mängder minne.

Azure Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskin varu typ och som är dedikerad till en enda kund. Dessa storlekar på virtuella datorer lämpar sig bäst för arbets belastningar som kräver en hög grad av isolering från andra kunder för arbets belastningar som inbegriper element som efterlevnad och myndighets krav. Kunder kan också välja att ytterligare dela upp resurserna i de isolerade virtuella datorerna med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Se sidorna för virtuella dator familjer nedan för de isolerade VM-alternativen.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
