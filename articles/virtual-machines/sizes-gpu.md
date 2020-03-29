---
title: Azure VM storlekar - GPU | Microsoft-dokument
description: Visar en lista över de olika GPU-optimerade storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, datadiskar och nätverkskort samt lagringsdataflöde och nätverksbandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913590"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU optimerade storlekar för virtuella datorer

GPU optimerade VM storlekar är specialiserade virtuella maskiner som finns med enstaka eller flera NVIDIA GPU: er. Dessa storlekar är utformade för beräkningsintensiva, grafikintensiva och visualiseringsarbetsbelastningar. Den här artikeln innehåller information om antalet och typen av GPU:er, virtuella processorer, datadiskar och nätverkskort. Lagringsdataflöde och nätverksbandbredd ingår också för varje storlek i den här gruppningen.

- [NC-serien](nc-series.md), [NCv2-serien](ncv2-series.md), [NCv3-serien](ncv3-series.md) storlekar är optimerade för beräkningsintensiva och nätverksintensiva program och algoritmer. Några exempel är CUDA och OpenCL-baserade program och simuleringar, AI och Deep Learning. NCv3-serien fokuserar på högpresterande datorarbetsbelastningar med NVIDIA:s Tesla V100 GPU. NC-serien använder Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) processor, och NCv2-serien och NCv3-serien virtuella datorer använder Intel Xeon E5-2690 v4 (Broadwell) processor.

- [Storlekar i ND-serien](nd-series.md)och [NDv2-serien](ndv2-series.md) är inriktade på tränings- och inferensscenarier för djupinlärning. De använder NVIDIA Tesla P40 GPU och Intel Xeon E5-2690 v4 (Broadwell) processor. NDv2-serien använder Intel Xeon Platinum 8168 (Skylake) processor.

- [NV-seriens](nv-series.md) och [NVv3-seriens](nvv3-series.md) storlekar är optimerade och utformade för fjärrvisualisering, streaming, spel, kodning och VDI-scenarier med ramverk som OpenGL och DirectX. Dessa virtuella datorer backas upp av NVIDIA Tesla M60 GPU.

- [NVv4-serien](nvv4-series.md) VM-storlekar optimerade och utformade för VDI och fjärrvisualisering. Med partitionerade GPU:er erbjuder NVv4 rätt storlek för arbetsbelastningar som kräver mindre GPU-resurser. Dessa virtuella datorer backas upp av AMD Radeon Instinct MI25 GPU. Virtuella NVv4-datorer stöder för närvarande endast Windows gästoperativsystem.

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste NVIDIA GPU-drivrutiner installeras.

[NVIDIA GPU Driver Extension](/azure/virtual-machines/extensions/hpccompute-gpu-windows) installerar lämpliga NVIDIA CUDA- eller GRID-drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg som Azure PowerShell- eller Azure Resource Manager-mallar. Se [dokumentationen](/azure/virtual-machines/extensions/hpccompute-gpu-windows) för NVIDIA GPU Driver Extension för operativsystem och driftsättningssteg som stöds. Allmän information om vm-tillägg finns i [Azure-tillägg och funktioner för virtuella datorer](/azure/virtual-machines/extensions/overview).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt läser du [GPU-drivrutinsinställningarna i N-serien för](/azure/virtual-machines/windows/n-series-driver-setup) [GPU-drivrutinsinställningar i](/azure/virtual-machines/linux/n-series-driver-setup) Windows eller N-serien för Linux för operativsystem, drivrutiner, installations- och verifieringssteg.

## <a name="deployment-considerations"></a>Distributionsöverväganden

- För tillgänglighet för virtuella datorer i N-serien finns i [Produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/).

- Virtuella datorer i N-serien kan bara distribueras i resurshanterarens distributionsmodell.

- Virtuella datorer i N-serien skiljer sig åt i vilken typ av Azure Storage de stöder för sina diskar. NC och NV virtuella datorer stöder endast VM-diskar som backas upp av HDD (Standard Disk Storage). Virtuella datorer med NCv2, NCv3, ND, NDv2 och NVv2 stöder endast virtuella datorer som backas upp av Premium Disk Storage (SSD).

- Om du vill distribuera fler än några virtuella datorer i N-serien bör du överväga en prenumeration med användningsbaserad betalning eller andra köpalternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

- Du kan behöva öka kärnkvoten (per region) i din Azure-prenumeration och öka den separata kvoten för NC-, NCv2-, NCv3-, ND-, NDv2-, NV- eller NVv2-kärnor. Om du vill begära en kvotökning [öppnar du en kundsupportbegäran online](../azure-portal/supportability/how-to-create-azure-support-request.md) utan kostnad. Standardgränserna kan variera beroende på din prenumerationskategori.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
