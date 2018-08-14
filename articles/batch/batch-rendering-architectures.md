---
title: Azure-återgivning - referensarkitekturer
description: Arkitekturer för att använda Azure Batch och andra Azure-tjänster för att utöka ett lokalt rendera grupp genom att ta dig till molnet
services: batch
author: davefellows
manager: jeconnoc
ms.author: danlep
ms.date: 08/13/2018
ms.topic: conceptual
ms.openlocfilehash: 0fe101ee6eb88094034b90c4d39f06ba509c9512
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099933"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referensarkitekturer för Azure-återgivning

Den här artikeln visar övergripande Arkitekturdiagram för scenarier för att utöka eller ”burst-”, en lokal rendera servergrupp till Azure. Exemplen visar olika alternativ för Azure beräknings-, nätverks- och storage-tjänster.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrid med NFS eller CFS

I följande diagram visas ett scenario med hybridanvändning som innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure virtuellt nätverk.

* **Storage** – inkommande och utgående filer: NFS eller CFS med hjälp av virtuella Azure-datorer som är synkroniserad med den lokala lagringen via Azure File Sync eller RSync.

  ![”Cloud bursting” - Hybrid med NFS eller CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Hybrid med Blobfuse

I följande diagram visas ett scenario med hybridanvändning som innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure virtuellt nätverk.

* **Storage** – inkommande och utgående filer: Blob-lagring, monterad för att beräkna resurser via Azure Blobfuse.

  ![”Cloud bursting” - Hybrid med Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrid beräkning och lagring

Följande diagram visar ett fullständigt anslutna hybridscenario för beräkning och lagring och innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure virtuellt nätverk.

* **Storage** -mellan lokala: Avere vFXT. Valfritt arkivering av lokala filer via Azure Data Box till Blob storage.

  ![”Cloud bursting” - Hybrid beräkning och lagring](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du använder [rendera chefer](batch-rendering-render-managers.md) med Azure Batch.

* Mer information om alternativ för [Rendering i Azure](batch-rendering-service.md).