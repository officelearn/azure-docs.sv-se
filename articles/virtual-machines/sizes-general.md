---
title: Azure VM-storlekar – Allmänt syfte | Microsoft-dokument
description: Visar en lista över de olika allmänna storlekarna som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, datadiskar och nätverkskort samt lagringsdataflöde och nätverksbandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 22826c32dc2aee0e580ec0b2a05c7eb8f08b7570
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115315"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Storlekar för virtuella datorer för allmänna ändamål

Vm-storlekar för allmänna ändamål ger ett balanserat förhållande mellan processor och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. Den här artikeln innehåller information om erbjudanden för allmän beräkning.

- Virtuella datorer i [Av2-serien](av2-series.md) kan distribueras på en mängd olika maskinvarutyper och processorer. Virtuella datorer i A-serien har processorprestanda och minneskonfigurationer som är bäst lämpade för arbetsbelastningar på ingångsnivå som utveckling och test. Storleken begränsas, baserat på maskinvaran, för att erbjuda enhetliga processorprestanda på instansen som körs, oavsett vilken maskinvara instansen har distribuerats på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn. Exempel på användningsfall är utvecklings- och testservrar, webbservrar med låg trafik, små till medelstora databaser, konceptbevis och kodarkiv.

  > [!NOTE]
  > De virtuella datorerna A8 – A11 är planerade att gå i pension den 3/2021. Mer information finns i [HPC:s migreringsguide](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [B-serien sprängbar](sizes-b-series-burstable.md) Virtuella datorer är idealiska för arbetsbelastningar som inte behöver full prestanda för processorn kontinuerligt, som webbservrar, små databaser och utvecklings- och testmiljöer. Dessa arbetsbelastningar har vanligtvis burstable prestandakrav. B-serien ger dessa kunder möjlighet att köpa en VM-storlek med en prismedveten baslinjeprestanda som gör att VM-instansen kan bygga upp krediter när den virtuella datorn använder mindre än basprestanda. När den virtuella datorn har ackumulerat kredit kan den virtuella datorn brista ovanför den virtuella datorns baslinje med upp till 100 % av processorn när ditt program kräver högre CPU-prestanda.

- [Dav4 och Dasv4-serien](dav4-dasv4-series.md) är nya storlekar som använder AMD: s 2.35Ghz EPYC<sup>TM</sup> 7452-processor i en flertrådad konfiguration med upp till 256 MB L3 cache dedikera 8 MB av att L3 cache till varje 8 kärnor öka kundalternativ för att köra sina allmänna ändamål arbetsbelastningar. Dav4-serien och Dasv4-serien har samma minnes- och diskkonfigurationer som D & Dsv3-serien.

- [DCv2-serien](dcv2-series.md) kan skydda sekretessen och integriteten för dina data och din kod medan den bearbetas i det offentliga molnet. Dessa maskiner backas upp av den senaste generationen av Intel XEON E-2288G-processor med SGX-teknik. Med Intel Turbo Boost Technology kan dessa maskiner gå upp till 5,0 GHz. DCv2-serien instanser gör det möjligt för kunder att bygga säkra enklav-baserade program för att skydda sin kod och data medan den används.

- [Dv2- och Dsv2-serien](dv2-dsv2-series.md) Virtuella datorer, en uppföljning av den ursprungliga D-serien, har en mer kraftfull CPU och optimal CPU-till-minne-konfiguration som gör dem lämpliga för de flesta produktionsarbetsbelastningar. Dv2-serien är ca 35% snabbare än D-serien. Dv2-serien körs på Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

- [Dv3- och Dsv3-serien](dv3-dsv3-series.md) Virtuella datorer körs på Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer i en hyper-threaded konfiguration, vilket ger ett bättre värde erbjudande för de flesta allmänna arbetsbelastningar. Minnet har utökats (från ~3.5 GiB/vCPU till 4 GiB/vCPU) medan disk- och nätverksgränser har justerats per kärna för att anpassa sig till övergången till hyperthreading. Dv3-serien har inte längre den höga minnes-VM-storleken på D/Dv2-serien, de har flyttats till minnet optimerade [Ev3 och Esv3-serien](ev3-esv3-series.md).

Exempel på användningsfall i D-serien omfattar program i företagsklass, relationsdatabaser, cachelagring i minnet och analyser.

## <a name="other-sizes"></a>Andra storlekar

- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
