---
title: "Välj VM-storlekar för Azure Batch pooler | Microsoft Docs"
description: "Hur du kan välja bland tillgängliga VM-storlekar för compute-noder i Azure Batch-pooler"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: addd1e9314a754b40cc5d49c0299f007580f512f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Välj en VM-storlek för compute-noder i en Azure Batch-pool

När du väljer storleken på en nod för en Azure Batch-pool, kan du välja mellan nästan alla storlek på Virtuella datorer finns i Azure. Azure erbjuder en mängd storlekar för Linux och Windows-datorer för olika arbetsbelastningar. 

Det finns några undantag och begränsningar för att välja en VM-storlek:
* Vissa VM familjer eller VM-storlekar stöds inte i en Batch. 
* Vissa VM-storlekar är begränsat och måste aktiveras specifikt innan de kan tilldelas.


## <a name="supported-vm-families-and-sizes"></a>VM-familjer och storlekar som stöds

### <a name="pools-in-virtual-machine-configuration"></a>Pooler i konfiguration av virtuell dator

Batch-adresspooler i konfigurationen av virtuella datorn stöder alla VM-storlekar ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *utom* för följande:

| Familj  | Stöds inte storlekar  |
|---------|---------|
| Grundläggande A-series | Basic_A0 (A0) |
| A-serien | Standard_A0 |
| B-serien | Alla |
| Fsv2-serien<sup>*</sup> | Alla |

<sup>*</sup>I den här serien strömstorlekarna på Översikt för framtida stöd.

### <a name="pools-in-cloud-service-configuration"></a>Pooler i Cloud Service-konfiguration

Batch-pooler i Cloud Service-konfigurationen stöder alla [VM-storlekar för molntjänster](../cloud-services/cloud-services-sizes-specs.md) *utom* för följande:

| Familj  | Stöds inte storlekar  |
|---------|---------|
| A-serien | ExtraSmall |
| Av2-serien | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Begränsad VM-familjer
Följande VM-familjer kan fördelas i Batch-pooler, men du måste begära en ökad specifika kvot (se [i den här artikeln](batch-quota-limit.md#increase-a-quota)):
* NCv2-serien
* NCv3-serien
* ND-serien

Dessa storlekar kan endast användas i pooler i konfigurationen av virtuella datorn.

## <a name="size-considerations"></a>Storlek överväganden

* **Krav för Application** -egenskaper och krav för det program som du kör på noderna. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. För flera instanser [arbetsbelastningar MPI](batch-mpi.md) eller CUDA program, Överväg specialiserade [HPC](../virtual-machines/linux/sizes-hpc.md) eller [GPU-aktiverade](../virtual-machines/linux/sizes-gpu.md) VM storlekar respektive. (Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](batch-pool-compute-intensive-sizes.md).) 

* **Uppgifter per nod** – det är vanligt att markera en nod storlek under förutsättning att en aktivitet som körs på en nod i taget. Det kan dock vara bra att ha flera uppgifter (och därför flera instanser av programmet) [körs parallellt](batch-parallel-node-tasks.md) på datornoder under jobbkörningen. I det här fallet är det vanligt att välja en nodstorlek för flera kärnor för ökad efterfrågan för körning av parallell uppgift.

* **Läsa in nivåer för olika uppgifter** -alla noder i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler. 

* **Regional tillgänglighet** -familj för en virtuell dator eller storlek kanske inte är tillgänglig i regioner där du skapar Batch-konton. Du kan kontrollera att det finns en storlek [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/).

* **Kvoter** – [kärnor kvoter](batch-quota-limit.md#resource-quotas) i din Batch konto kan begränsa antalet noder i en given storlek som du kan lägga till en Batch-pool. Om du vill begära en ökad kvot finns [i den här artikeln](batch-quota-limit.md#increase-a-quota). 

* **Konfigurationen för lagringspooler** -generellt sett du har fler alternativ för VM-storleken när du skapar en pool i den virtuella datorkonfigurationen jämfört med molntjänster tjänstkonfigurationen.

## <a name="next-steps"></a>Nästa steg

* En detaljerad översikt över Batch finns [utveckla storskaliga parallell compute lösningar med Batch](batch-api-basics.md).
* Information om hur du använder beräkningsintensiva VM-storlekar finns [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](batch-pool-compute-intensive-sizes.md). 


