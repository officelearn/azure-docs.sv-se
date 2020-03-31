---
title: Välj VM-storlekar för pooler – Azure Batch | Microsoft-dokument
description: Så här väljer du bland de tillgängliga vm-storlekarna för beräkningsnoder i Azure Batch-pooler
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087039"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Välj en VM-storlek för beräkningsnoder i en Azure Batch-pool

När du väljer en nodstorlek för en Azure Batch-pool kan du välja bland nästan alla VM-storlekar som är tillgängliga i Azure. Azure erbjuder en rad storlekar för virtuella Linux- och Windows-datorer för olika arbetsbelastningar.

Det finns några undantag och begränsningar för att välja en vm-storlek:

* Vissa VM-serier eller VM-storlekar stöds inte i Batch.
* Vissa vm-storlekar är begränsade och måste vara specifikt aktiverade innan de kan allokeras.

## <a name="supported-vm-series-and-sizes"></a>VM-serier och storlekar som stöds

### <a name="pools-in-virtual-machine-configuration"></a>Pooler i konfigurationen för virtuella datorer

Batchpooler i konfigurationen för virtuella datorer stöder nästan alla VM-storlekar ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Se följande tabell om du vill veta mer om storlekar och begränsningar som stöds.

| VM-serie  | Storlekar som stöds |
|------------|---------|
| Basic A | Alla storlekar *utom* Basic_A0 (A0) |
| A | Alla storlekar *utom* Standard_A0 |
| Av2 (av2) | Alla storlekar |
| B | Inget |
| DC | Inget |
| Dv2 , DSv2 | Alla storlekar |
| Dv3 , Dsv3 | Alla storlekar |
| Dav4 , Dasv4 | Ingen - ännu inte tillgänglig |
| Ev3, Esv3 | Alla storlekar, utom E64is_v3 och E64i_v3 |
| Eav4, Easv4 | Ingen - ännu inte tillgänglig |
| F, Fs | Alla storlekar |
| Fsv2 (på andra sätt) | Alla storlekar |
| G, Gs | Alla storlekar |
| H | Alla storlekar |
| HB<sup>1</sup> | Alla storlekar |
| HBv2<sup>1</sup> | Alla storlekar |
| HC<sup>1 (PÅ ANDRA)</sup> | Alla storlekar |
| Ls | Alla storlekar |
| Lsv2 (på andra sätt) | Ingen - ännu inte tillgänglig |
| M<sup>1</sup> | Alla storlekar, utom M64, M64m, M128, M128m |
| Mv2 (på andra) | Ingen - ännu inte tillgänglig |
| NC | Alla storlekar |
| NCv2<sup>1</sup> | Alla storlekar |
| NCv3<sup>1</sup> | Alla storlekar |
| ND<sup>1</sup> | Alla storlekar |
| NDv2<sup>1</sup> | Ingen - ännu inte tillgänglig |
| NV | Alla storlekar |
| NVv3<sup>1</sup> | Alla storlekar |
| NVv4 (på andra sätt) | Inget |
| SAP HANA | Inget |

<sup>1</sup> Dessa VM-storlekar kan allokeras i batchpooler i konfigurationen för virtuella datorer, men du måste skapa ett nytt batchkonto och begära en specifik [kvotökning](batch-quota-limit.md#increase-a-quota). Den här begränsningen tas bort när vCPU-kvoten per VM-serie stöds fullt ut för Batch-konton.

### <a name="pools-in-cloud-service-configuration"></a>Pooler i molntjänstkonfiguration

Batchpooler i Cloud Service-konfigurationen stöder alla [VM-storlekar för molntjänster](../cloud-services/cloud-services-sizes-specs.md) **utom** följande:

| VM-serie  | Storlekar som inte stöds |
|------------|-------------------|
| A-serien   | Extra liten       |
| Av2-serien | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Överväganden gällande storlek

* **Programkrav** - Tänk på egenskaperna och kraven för det program som du ska köra på noderna. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. För [MPI-arbetsbelastningar](batch-mpi.md) med flera instanser eller CUDA-program bör du överväga specialiserade [HPC-](../virtual-machines/linux/sizes-hpc.md) eller [GPU-aktiverade VM-storlekar.](../virtual-machines/linux/sizes-gpu.md) (Se [Använda RDMA-kompatibla eller GPU-aktiverade instanser i batchpooler](batch-pool-compute-intensive-sizes.md).)

* **Aktiviteter per nod** - Det är typiskt att välja en nodstorlek förutsatt att en aktivitet körs på en nod i taget. Det kan dock vara fördelaktigt att ha flera aktiviteter (och därmed flera programinstanser) [som körs parallellt](batch-parallel-node-tasks.md) på beräkningsnoder under jobbkörningen. I det här fallet är det vanligt att välja en nodstorlek med flera kärnor för att tillgodose den ökade efterfrågan på parallell körning av uppgifter.

* **Belastningsnivåer för olika aktiviteter** - Alla noder i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler.

* **Regiontillgänglighet** – En VM-serie eller storlek kanske inte är tillgänglig i de regioner där du skapar dina batchkonton. Information om att kontrollera att en storlek är tillgänglig finns i [Produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/).

* **Kvoter** - [Kärnorna kvoter](batch-quota-limit.md#resource-quotas) i ditt Batch-konto kan begränsa antalet noder av en viss storlek som du kan lägga till i en batch pool. Information om hur du begär en kvotökning finns i [den här artikeln](batch-quota-limit.md#increase-a-quota). 

* **Poolkonfiguration** - I allmänhet har du fler alternativ för vm-storlek när du skapar en pool i konfigurationen för virtuella datorer, jämfört med molntjänstkonfigurationen.

## <a name="next-steps"></a>Nästa steg

* En djupgående översikt över Batch finns i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
* Information om hur du använder beräkningsintensiva VM-storlekar finns i [Använda RDMA-kompatibla eller GPU-aktiverade instanser i batchpooler](batch-pool-compute-intensive-sizes.md).
