---
title: Storlekar för virtuella Azure-datorer – generell användning | Microsoft Docs
description: Visar en lista över de olika generella syftes storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: fc263eb6fbe6c6402aaf529229bb7025f070b8d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269677"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Generella storlekar för virtuella datorer

Generella storlekar för virtuella datorer ger balanserade förhållandet mellan processor och minne. Utmärkt för testning och utveckling, små till medelstora databaser och webbservrar med låg till medelhög trafik. Den här artikeln innehåller information om erbjudanden för generell användning av ändamål.

- De virtuella datorerna i [AV2-serien](av2-series.md) kan distribueras på olika typer av maskin vara och processorer. Virtuella datorer i A-serien har CPU-prestanda och minneskonfigurationer som är bäst lämpade för arbets belastningar på ingångs nivå som utveckling och testning. Storleken begränsas, baserat på maskinvaran, för att erbjuda enhetliga processorprestanda på instansen som körs, oavsett vilken maskinvara instansen har distribuerats på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn. Exempel på användnings områden är utvecklings-och test servrar, webb servrar med låg trafik, små till medel stora databaser, koncept koncept och kod databaser.

  > [!NOTE]
  > De virtuella datorerna A8 – A11 planeras för att dra på 3/2021. Mer information finns i [Guide för HPC-migrering](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [B-seriens burst-](sizes-b-series-burstable.md) överföring Virtuella datorer är idealiska för arbets belastningar som inte behöver PROCESSORns fulla prestanda kontinuerligt, till exempel webb servrar, små databaser och utvecklings-och test miljöer. Dessa arbets belastningar har vanligt vis höga prestanda krav. B-serien ger kunderna möjlighet att köpa en VM-storlek med en pris medveten bas linje prestanda som gör det möjligt för den virtuella dator instansen att bygga krediter när den virtuella datorn använder mindre än dess grundläggande prestanda. När den virtuella datorn har ackumulerat kredit kan den virtuella datorn överföras över den virtuella datorns bas linje med upp till 100% av CPU: n när programmet kräver högre CPU-prestanda.

- [Dav4 och Dasv4-serien](dav4-dasv4-series.md) är nya storlekar som använder AMD: s 2.35 GHz-EPYC<sup>TM</sup> 7452-processor i en multi-threadd konfiguration med upp till 256 MB L3-cache dedikera 8 MB av den L3-cachen till varje 8 kärnor som ökar kund alternativen för att köra sina generella arbets belastningar. Dav4-serien och Dasv4-serien har samma minnes-och diskkonfigurationer som D & Dsv3-serien.

- [DCv2-serien](dcv2-series.md) kan hjälpa till att skydda konfidentialiteten och integriteten hos dina data och kod när de bearbetas i det offentliga molnet. De här datorerna backas upp av den senaste generationen Intel XEON E-2288G-processor med SGX-teknik. Med Intel Turbo Boost-tekniken kan de här datorerna gå upp till 5,0 GHz. DCv2 Series-instanser gör det möjligt för kunder att skapa säkra enklaven-baserade program för att skydda sin kod och sina data medan den används.

- [Dv2 och Dsv2-serien](dv2-dsv2-series.md) Virtuella datorer, en uppföljning till den ursprungliga D-serien, har en kraftfullare processor och optimal konfiguration av processor till minne som gör dem lämpliga för de flesta produktions arbets belastningar. Dv2-serien är cirka 35% snabbare än D-serien. Dv2-serien körs på Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2,0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

- [Dv3 och Dsv3-serien](dv3-dsv3-series.md) De virtuella datorerna körs på Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar. Minnet har utökats (från ~ 3,5 GiB/vCPU till 4 GiB/vCPU) medan disk-och nätverks gränser har justerats per kärna för att justeras efter flytta till hyperthreading. Dv3-serien har inte längre hög minnes storlek för virtuella datorer i D/Dv2-serien, de har flyttats till den minnesoptimerade [Ev3 och Esv3-serien](ev3-esv3-series.md).

Exempel på användnings områden i D-serien är program i företags klass, Relations databaser, minnes intern cachelagring och analys.

## <a name="other-sizes"></a>Andra storlekar

- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
