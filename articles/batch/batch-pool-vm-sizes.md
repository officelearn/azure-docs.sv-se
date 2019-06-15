---
title: Välj VM-storlekar för pooler – Azure Batch | Microsoft Docs
description: Så här att välja bland de tillgängliga storlekarna för beräkningsnoder i Azure Batch-pooler
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 033e0865f23034b94e3133e0ba5890eca4e746ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080878"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Välj en VM-storlek för beräkningsnoder i en Azure Batch-pool

När du väljer en nodstorlek för en Azure Batch-pool kan välja du bland nästan alla storlekarna som tillgängliga i Azure. Azure erbjuder flera olika storlekar för Linux- och Windows-datorer för olika arbetsbelastningar.

Det finns några undantag och begränsningar med att välja en VM-storlek:

* Vissa serierna med virtuella datorer eller VM-storlekar stöds inte i Batch.
* Vissa VM-storlekar är begränsade och måste aktiveras specifikt innan de kan tilldelas.

## <a name="supported-vm-series-and-sizes"></a>Stöds VM-serie och storlekar

### <a name="pools-in-virtual-machine-configuration"></a>Pooler i konfiguration av virtuell dator

Batch-pooler i den virtuella datorkonfigurationen stöder nästan alla VM-storlekar ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Se tabellen nedan för mer information om storlekar som stöds och begränsningar.

Alla erbjudanden eller förhandsversion VM-storlekar som inte finns i listan är inte garanterad för support.

| Virtuell datorserie  | Storlekar som stöds | Poolallokeringsläget för batch-konto<sup>1</sup> |
|------------|---------|-----------------|
| Basic A-serien | Alla storlekar *utom* Basic_A0 (A0) | Alla |
| A-serien | Alla storlekar *utom* Standard_A0 | Alla |
| Av2-serien | Alla storlekar | Alla |
| B-serien | Ingen | Inte tillgängligt |
| DC-serien | Ingen | Inte tillgängligt |
| Dv2, Dsv2-series | Alla storlekar | Alla |
| Dv3, Dsv3-series | Alla storlekar | Alla |
| [Optimerat minnesstorlekar](../virtual-machines/linux/sizes-memory.md) | Ingen | Inte tillgängligt |
| Fsv2-serien | Alla storlekar | Alla |
| H-serien | Alla storlekar | Alla |
| Hb-serien | Alla storlekar | Användarprenumerationsläge |
| Hc-serien | Alla storlekar | Användarprenumerationsläge |
| Ls-serien | Alla storlekar | Alla |
| Lsv2-serien | Ingen | Inte tillgängligt |
| M-serien | Standard_M64ms (med låg prioritet endast), Standard_M128s (endast med låg prioritet) | Alla |  
| NCv2-serien<sup>2</sup> | Alla storlekar | Alla |
| NCv3-serien<sup>2</sup> | Alla storlekar | Alla |
| ND-serien<sup>2</sup> | Alla storlekar | Alla |
| NDv2-serien | Alla storlekar | Användarprenumerationsläge |
| NV-serien | Alla storlekar | Alla |
| NVv3-serien | Ingen | Inte tillgängligt |
| SAP HANA | Ingen | Inte tillgängligt |

<sup>1</sup> vissa nyare VM-serier stöds delvis från början. Dessa VM-serier kan allokeras av Batch-konton med den **poolallokeringsläget** inställd **användarprenumeration**. Se [hantera Batch-konton](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) mer information om kontokonfigurationen för Batch. Se [kvoter och begränsningar](batch-quota-limit.md) information om hur du begär kvot för dessa stöds delvis VM-serie för **användarprenumeration** Batch-konton.  

<sup>2</sup> dessa VM-storlekar kan fördelas i Batch-pooler i konfiguration av virtuell dator, men du måste begära en specifik [kvot](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Pooler i Cloud Service-konfiguration

Batch-pooler i Cloud Service-konfigurationen har stöd för alla [VM-storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **utom** för följande:

| Virtuell datorserie  | Storlekar för stöds inte |
|------------|-------------------|
| A-serien   | Extra liten       |
| Av2-serien | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Storlek-överväganden

* **Programkrav** -Överväg egenskaperna och kraven för programmet som ska köras på noderna. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. För flera instanser [MPI arbetsbelastningar](batch-mpi.md) eller CUDA-program, Överväg att specialiserade [HPC](../virtual-machines/linux/sizes-hpc.md) eller [GPU-aktiverade](../virtual-machines/linux/sizes-gpu.md) respektive Virtuella datorer. (Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](batch-pool-compute-intensive-sizes.md).)

* **Aktiviteter per nod** – det är vanligt att välja en nodstorlek under antagandet en aktivitet körs på en nod i taget. Det kan dock vara bra att ha flera aktiviteter (och därmed flera programinstanser) [köras parallellt](batch-parallel-node-tasks.md) på datornoder under jobbkörningen. I det här fallet är det vanligt att välja en flerkärniga nodstorlek för utökade behovet av att köra åtgärder parallellt.

* **Belastningsnivåer för olika uppgifter** -alla noder i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler.

* **Regiontillgänglighet** – en VM-serien eller storlek kanske inte är tillgängliga i regioner där du skapar Batch-konton. Du kan kontrollera att en storlek är tillgänglig [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/).

* **Kvoter** – [kärnor kvoter](batch-quota-limit.md#resource-quotas) i batchen konto kan begränsa antalet noder i en given storlek som du kan lägga till en Batch-pool. Om du vill begära en kvot, se [i den här artikeln](batch-quota-limit.md#increase-a-quota). 

* **Poolkonfigurationen** -generellt sett du har fler alternativ för VM-storleken när du skapar en pool i den virtuella datorkonfigurationen jämfört med konfigurationen av molnet.

## <a name="next-steps"></a>Nästa steg

* En detaljerad översikt över Batch finns [utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
* Information om hur du använder beräkningsintensiva VM-storlekar finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i Batch-pooler](batch-pool-compute-intensive-sizes.md).
