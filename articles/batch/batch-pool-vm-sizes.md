---
title: Välj VM-storlekar för pooler – Azure Batch | Microsoft Docs
description: Hur du väljer bland tillgängliga VM-storlekar för datornoderna i Azure Batch pooler
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
ms.openlocfilehash: be19de19dab92bc40ca5529ad578e033a98929cd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023573"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Välj en VM-storlek för Compute-noder i en Azure Batch pool

När du väljer en Node-storlek för en Azure Batch pool kan du välja bland nästan alla VM-storlekar som är tillgängliga i Azure. Azure erbjuder ett antal storlekar för virtuella Linux-och Windows-datorer för olika arbets belastningar.

Det finns några undantag och begränsningar för att välja en VM-storlek:

* Vissa VM-serier eller VM-storlekar stöds inte i batch.
* Vissa VM-storlekar är begränsade och måste vara särskilt aktiverade innan de kan allokeras.

## <a name="supported-vm-series-and-sizes"></a>VM-serien och storlekar som stöds

### <a name="pools-in-virtual-machine-configuration"></a>Pooler i konfiguration av virtuell dator

Batch-pooler i den virtuella dator konfigurationen stöder nästan alla VM-storlekar ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). I följande tabell finns mer information om storlekar och begränsningar som stöds.

Alla kampanjer och för hands versioner av virtuella datorer som inte listas är inte garanterade för support.

| VM-serie  | Storlekar som stöds | Allokeringsinställningar för batch-konto<sup>1</sup> |
|------------|---------|-----------------|
| Basic A-Series | Alla storlekar *utom* Basic_A0 (a0) | Alla |
| A-serien | Alla storlekar *utom* Standard_A0 | Alla |
| Av2-serien | Alla storlekar | Alla |
| B-serien | Inget | Inte tillgänglig |
| DC-serien | Inget | Inte tillgänglig |
| Dv2, DSv2-serien | Alla storlekar | Alla |
| Dv3, Dsv3-series | Alla storlekar | Alla |
| Ev3, Esv3-serien | Alla storlekar | Alla |
| Fsv2-serien | Alla storlekar | Alla |
| H-serien | Alla storlekar | Alla |
| HB-serie<sup>2</sup> | Alla storlekar | Alla |
| HC-serie<sup>2</sup> | Alla storlekar | Alla |
| Ls-serien | Alla storlekar | Alla |
| Lsv2-serien | Inget | Inte tillgänglig |
| M-serien | Standard_M64ms (endast låg prioritet) Standard_M128s (endast låg prioritet) | Alla |
| Mv2-serien | Inget | Inte tillgänglig |
| NC-serien | Alla storlekar | Alla |
| NCv2-serie<sup>2</sup> | Alla storlekar | Alla |
| NCv3-serie<sup>2</sup> | Alla storlekar | Alla |
| ND-serien<sup>2</sup> | Alla storlekar | Alla |
| NDv2-serien | Alla storlekar | Användarprenumerationsläge |
| NV-serien | Alla storlekar | Alla |
| NVv3-serien | Inget | Inte tillgänglig |
| SAP HANA | Inget | Inte tillgänglig |

<sup>1</sup> vissa nyare VM-serier stöds delvis. Dessa VM-serier kan allokeras av batch-konton med **poolens fördelnings läge** inställt på **användar prenumeration**. Mer information om konfiguration av batch-konton finns i [Hantera batch-konton](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) . Se [kvoter och begränsningar](batch-quota-limit.md) för att lära dig att begära kvot för de här delvis STÖDda VM-serien för batch-konton för **användar prenumeration** .  

<sup>2</sup> de här VM-storlekarna kan allokeras i batch-pooler i konfigurationen av den virtuella datorn, men du måste begära en speciell [kvot ökning](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Pooler i moln tjänst konfiguration

Batch-pooler i moln tjänst konfigurationen har stöd [för alla VM-storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **utom** följande:

| VM-serie  | Storlekar som inte stöds |
|------------|-------------------|
| A-serien   | Extra liten       |
| Av2-serien | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Överväganden för storlek

* **Program krav** – beakta egenskaperna och kraven för det program som ska köras på noderna. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. För [MPI arbets belastningar](batch-mpi.md) eller CUDA-program med flera instanser bör du överväga specialiserade [HPC](../virtual-machines/linux/sizes-hpc.md) [-eller GPU-aktiverade VM-](../virtual-machines/linux/sizes-gpu.md) storlekar. (Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](batch-pool-compute-intensive-sizes.md).)

* **Aktiviteter per nod** – det är vanligt att välja en Node-storlek förutsatt att en aktivitet körs på en nod i taget. Det kan dock vara fördelaktigt att ha flera aktiviteter (och därför flera program instanser) [som körs parallellt](batch-parallel-node-tasks.md) på datornoderna under jobb körningen. I det här fallet är det vanligt att välja en storlek för flera kärnor för att hantera den ökade efter frågan av parallell körning.

* **Belastnings nivåer för olika aktiviteter** -alla noder i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler.

* **Region tillgänglighet** – en VM-serie eller storlek kanske inte är tillgänglig i de regioner där du skapar dina batch-konton. Information om hur du kontrollerar att en storlek är tillgänglig finns i [produkt tillgänglighet per region](https://azure.microsoft.com/regions/services/).

* **Kvoter** - [kärnor](batch-quota-limit.md#resource-quotas) i batch-kontot kan begränsa antalet noder i en särskild storlek som du kan lägga till i en batch-pool. Information om hur du begär en kvot ökning finns i [den här artikeln](batch-quota-limit.md#increase-a-quota). 

* **Konfiguration av pool** – i allmänhet har du fler alternativ för VM-storlek när du skapar en pool i konfigurationen för den virtuella datorn, jämfört med moln tjänst konfigurationen.

## <a name="next-steps"></a>Nästa steg

* En djupgående översikt över batch finns i [utveckla storskaliga parallella beräknings lösningar med batch](batch-api-basics.md).
* Information om hur du använder beräknings intensiva VM-storlekar finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](batch-pool-compute-intensive-sizes.md).
