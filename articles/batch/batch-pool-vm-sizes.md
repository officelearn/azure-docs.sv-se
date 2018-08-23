---
title: Välj VM-storlekar för Azure Batch-pooler | Microsoft Docs
description: Så här att välja bland de tillgängliga storlekarna för beräkningsnoder i Azure Batch-pooler
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: danlep
ms.openlocfilehash: 1669d5a2237322f72dce3b172c32e7199900a4e0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055366"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Välj en VM-storlek för beräkningsnoder i en Azure Batch-pool

När du väljer en nodstorlek för en Azure Batch-pool kan välja du bland nästan alla storlekarna som tillgängliga i Azure. Azure erbjuder flera olika storlekar för Linux- och Windows-datorer för olika arbetsbelastningar. 

Det finns några undantag och begränsningar med att välja en VM-storlek:
* Vissa VM-serier eller storlekar för Virtuella datorer stöds inte i Batch. 
* Vissa VM-storlekar är begränsade och måste aktiveras specifikt innan de kan tilldelas.


## <a name="supported-vm-families-and-sizes"></a>Stöds VM-familjer och storlekar

### <a name="pools-in-virtual-machine-configuration"></a>Pooler i konfiguration av virtuell dator

Batch-pooler i den virtuella datorkonfigurationen stöder alla VM-storlekar ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *utom* för följande:

| Familj  | Storlekar för stöds inte  |
|---------|---------|
| Basic A-serien | Basic_A0 (A0) |
| A-serien | Standard_A0 |
| B-serien | Alla |
| M-serien | Alla |



### <a name="pools-in-cloud-service-configuration"></a>Pooler i Cloud Service-konfiguration

Batch-pooler i Cloud Service-konfigurationen har stöd för alla [VM-storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md) *utom* för följande:

| Familj  | Storlekar för stöds inte  |
|---------|---------|
| A-serien | ExtraSmall |
| Av2-serien | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Begränsad VM-serier
Följande VM-familjer kan fördelas i Batch-pooler, men du måste begära en specifik kvot (se [i den här artikeln](batch-quota-limit.md#increase-a-quota)):
* NCv2-serien
* NCv3-serien
* ND-serien

Dessa storlekar kan endast användas i pooler i konfigurationen av virtuella datorn.

## <a name="size-considerations"></a>Storlek-överväganden

* **Programkrav** -Överväg egenskaperna och kraven för programmet som ska köras på noderna. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. För flera instanser [MPI arbetsbelastningar](batch-mpi.md) eller CUDA-program, Överväg att specialiserade [HPC](../virtual-machines/linux/sizes-hpc.md) eller [GPU-aktiverade](../virtual-machines/linux/sizes-gpu.md) respektive Virtuella datorer. (Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](batch-pool-compute-intensive-sizes.md).) 

* **Aktiviteter per nod** – det är vanligt att välja en nodstorlek under antagandet en aktivitet körs på en nod i taget. Det kan dock vara bra att ha flera aktiviteter (och därmed flera programinstanser) [köras parallellt](batch-parallel-node-tasks.md) på datornoder under jobbkörningen. I det här fallet är det vanligt att välja en flerkärniga nodstorlek för utökade behovet av att köra åtgärder parallellt.

* **Belastningsnivåer för olika uppgifter** -alla noder i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler. 

* **Regiontillgänglighet** – en VM-familj eller storlek kanske inte är tillgängliga i regioner där du skapar Batch-konton. Du kan kontrollera att en storlek är tillgänglig [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/).

* **Kvoter** – [kärnor kvoter](batch-quota-limit.md#resource-quotas) i batchen konto kan begränsa antalet noder i en given storlek som du kan lägga till en Batch-pool. Om du vill begära en kvot, se [i den här artikeln](batch-quota-limit.md#increase-a-quota). 

* **Poolkonfigurationen** -generellt sett du har fler alternativ för VM-storleken när du skapar en pool i den virtuella datorkonfigurationen jämfört med konfigurationen av molnet.

## <a name="next-steps"></a>Nästa steg

* En detaljerad översikt över Batch finns [utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
* Information om hur du använder beräkningsintensiva VM-storlekar finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](batch-pool-compute-intensive-sizes.md). 


