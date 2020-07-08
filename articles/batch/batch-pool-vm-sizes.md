---
title: Välj VM-storlekar för pooler
description: Hur du väljer bland tillgängliga VM-storlekar för datornoderna i Azure Batch pooler
ms.topic: conceptual
ms.date: 06/10/2020
ms.custom: seodec18
ms.openlocfilehash: c1621bbbe6676144176636346f3f5c34db169b7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84693166"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Välj en VM-storlek för Compute-noder i en Azure Batch pool

När du väljer en Node-storlek för en Azure Batch pool kan du välja bland nästan alla VM-storlekar som är tillgängliga i Azure. Azure erbjuder ett antal storlekar för virtuella Linux-och Windows-datorer för olika arbets belastningar.

Det finns några undantag och begränsningar för att välja en VM-storlek:

* Vissa VM-serier eller VM-storlekar stöds inte i batch.
* Vissa VM-storlekar är begränsade och måste vara särskilt aktiverade innan de kan allokeras.

## <a name="supported-vm-series-and-sizes"></a>VM-serien och storlekar som stöds

### <a name="pools-in-virtual-machine-configuration"></a>Pooler i konfiguration av virtuell dator

Batch-pooler i den virtuella dator konfigurationen stöder nästan alla VM-storlekar ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). I följande tabell finns mer information om storlekar och begränsningar som stöds.

| VM-serie  | Storlekar som stöds |
|------------|---------|
| Basic A | Alla storlekar *utom* Basic_A0 (a0) |
| A | Alla storlekar *utom* Standard_A0 |
| AV2 | Alla storlekar |
| B | Ingen |
| DC | Ingen |
| Dv2, DSv2 | Alla storlekar |
| Dv3, Dsv3 | Alla storlekar |
| Dav4<sup>1</sup> | Ingen – ännu inte tillgänglig |
| Dasv4<sup>1</sup> | Alla storlekar, förutom Standard_D48as_v4 Standard_D64as_v4, Standard_D96as_v4 |
| Ddv4, Ddsv4 |  Ingen – ännu inte tillgänglig |
| Ev3, Esv3 | Alla storlekar, förutom E64is_v3 och E64i_v3 |
| Eav4<sup>1</sup> | Alla storlekar, förutom Standard_E48a_v4 Standard_E64a_v4, Standard_E96a_v4 |
| Easv4<sup>1</sup> | Alla storlekar, förutom Standard_E48as_v4 Standard_E64as_v4, Standard_E96as_v4 |
| Edv4, Edsv4 |  Ingen – ännu inte tillgänglig |
| F, FS | Alla storlekar |
| Fsv2 | Alla storlekar |
| G, GS | Alla storlekar |
| H | Alla storlekar |
| HB<sup>1</sup> | Alla storlekar |
| HBv2<sup>1</sup> | Alla storlekar |
| HC<sup>1</sup> | Alla storlekar |
| Ls | Alla storlekar |
| Lsv2<sup>1</sup> | Alla storlekar |
| M<sup>1</sup> | Alla storlekar |
| Mv2 | Ingen – ännu inte tillgänglig |
| NC | Alla storlekar |
| NCv2<sup>1</sup> | Alla storlekar |
| NCv3<sup>1</sup> | Alla storlekar |
| ND<sup>1</sup> | Alla storlekar |
| NDv2<sup>1</sup> | Ingen – ännu inte tillgänglig |
| NV | Alla storlekar |
| NVv3<sup>1</sup> | Alla storlekar |
| NVv4 | Ingen |
| SAP HANA | Ingen |

<sup>1</sup> dessa VM-storlekar kan allokeras i batch-pooler i konfigurationen av den virtuella datorn, men du måste skapa ett nytt batch-konto och begära en bestämd [kvot ökning](batch-quota-limit.md#increase-a-quota). Den här begränsningen tas bort när vCPU-kvoten per VM-serien har fullt stöd för batch-konton.

### <a name="pools-in-cloud-service-configuration"></a>Pooler i moln tjänst konfiguration

Batch-pooler i moln tjänst konfigurationen har stöd [för alla VM-storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **utom** följande:

| VM-serie  | Storlekar som inte stöds |
|------------|-------------------|
| A-serien   | Extra liten       |
| Av2-serien | Standard_A1_v2 Standard_A2_v2 Standard_A2m_v2 |

## <a name="size-considerations"></a>Överväganden gällande storlek

* **Program krav** – beakta egenskaperna och kraven för det program som ska köras på noderna. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. För [MPI arbets belastningar](batch-mpi.md) eller CUDA-program med flera instanser bör du överväga specialiserade [HPC](../virtual-machines/linux/sizes-hpc.md) [-eller GPU-aktiverade VM-](../virtual-machines/linux/sizes-gpu.md) storlekar. (Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](batch-pool-compute-intensive-sizes.md).)

* **Aktiviteter per nod** – det är vanligt att välja en Node-storlek förutsatt att en aktivitet körs på en nod i taget. Det kan dock vara fördelaktigt att ha flera aktiviteter (och därför flera program instanser) [som körs parallellt](batch-parallel-node-tasks.md) på datornoderna under jobb körningen. I det här fallet är det vanligt att välja en storlek för flera kärnor för att hantera den ökade efter frågan av parallell körning.

* **Belastnings nivåer för olika aktiviteter** -alla noder i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler.

* **Region tillgänglighet** – en VM-serie eller storlek kanske inte är tillgänglig i de regioner där du skapar dina batch-konton. Information om hur du kontrollerar att en storlek är tillgänglig finns i [produkt tillgänglighet per region](https://azure.microsoft.com/regions/services/).

* **Kvoter** - [kärnor](batch-quota-limit.md#resource-quotas) i batch-kontot kan begränsa antalet noder i en särskild storlek som du kan lägga till i en batch-pool. Information om hur du begär en kvot ökning finns i [den här artikeln](batch-quota-limit.md#increase-a-quota). 

* **Konfiguration av pool** – i allmänhet har du fler alternativ för VM-storlek när du skapar en pool i konfigurationen för den virtuella datorn, jämfört med moln tjänst konfigurationen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
* Information om hur du använder beräknings intensiva VM-storlekar finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](batch-pool-compute-intensive-sizes.md).
