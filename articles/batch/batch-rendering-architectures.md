---
title: Azure-återgivning referensarkitekturer – Azure Batch
description: Arkitekturer för att använda Azure Batch och andra Azure-tjänster för att utöka ett lokalt rendera grupp genom att ta dig till molnet
services: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 08/13/2018
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: d5102ba94e2b7808a457df00a87b35ef7022c454
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543503"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referensarkitekturer för Azure-återgivning

Den här artikeln visar övergripande Arkitekturdiagram för scenarier för att utöka eller ”burst-”, en lokal rendera servergrupp till Azure. Exemplen visar olika alternativ för Azure beräknings-, nätverks- och storage-tjänster.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrid med NFS eller CFS

I följande diagram visas ett scenario med hybridanvändning som innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure virtuellt nätverk.

* **Storage** – inkommande och utgående filer: NFS- eller CFS med hjälp av Azure virtuella datorer, synkroniseras med den lokala lagringen via Azure File Sync eller RSync.

  ![”Cloud bursting” - Hybrid med NFS eller CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Hybrid med Blobfuse

I följande diagram visas ett scenario med hybridanvändning som innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure virtuellt nätverk.

* **Storage** – inkommande och utgående filer: BLOB-lagring, monterad för att beräkna resurser via Azure Blobfuse.

  ![”Cloud bursting” - Hybrid med Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrid beräkning och lagring

Följande diagram visar ett fullständigt anslutna hybridscenario för beräkning och lagring och innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure virtuellt nätverk.

* **Storage** – på olika platser: Avere vFXT. Valfritt arkivering av lokala filer via Azure Data Box till Blob storage.

  ![”Cloud bursting” - Hybrid beräkning och lagring](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du använder [rendera chefer](batch-rendering-render-managers.md) med Azure Batch.

* Mer information om alternativ för [Rendering i Azure](batch-rendering-service.md).