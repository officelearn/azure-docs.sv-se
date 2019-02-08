---
title: Azure-återgivning referensarkitekturer – Azure Batch
description: Arkitekturer för att använda Azure Batch och andra Azure-tjänster för att utöka ett lokalt rendera grupp genom att ta dig till molnet
services: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: b8813466b9c0f74a608c0150c037dfec3db08dbc
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893824"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referensarkitekturer för Azure-återgivning

Den här artikeln visar övergripande Arkitekturdiagram för scenarier för att utöka eller ”burst-”, en lokal rendera servergrupp till Azure. Exemplen visar olika alternativ för Azure beräknings-, nätverks- och storage-tjänster.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrid med NFS eller CFS

I följande diagram visas ett scenario med hybridanvändning som innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure VNet.

* **Storage** – inkommande och utgående filer: NFS- eller CFS med hjälp av Azure virtuella datorer, synkroniseras med den lokala lagringen via Azure File Sync eller RSync. Du kan också: Avere vFXT som indata eller utdata från den lokala NAS-enheter med hjälp av NFS-filerna.

  ![”Cloud bursting” - Hybrid med NFS eller CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybrid med Blobfuse

I följande diagram visas ett scenario med hybridanvändning som innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure VNet.

* **Storage** – inkommande och utgående filer: BLOB-lagring, monterad för att beräkna resurser via Azure Blobfuse.

  ![”Cloud bursting” - Hybrid med Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrid beräkning och lagring

Följande diagram visar ett fullständigt anslutna hybridscenario för beräkning och lagring och innehåller följande Azure-tjänster:

* **Compute** – Azure Batch-pool eller Virtual Machine Scale Sets.

* **Nätverk** -lokala: Azure ExpressRoute eller VPN. Azure: Azure VNet.

* **Storage** – på olika platser: Avere vFXT. Valfritt arkivering av lokala via Azure Data Box-filer till Blob-lagring eller lokalt Avere FXT för NAS acceleration.

  ![”Cloud bursting” - Hybrid beräkning och lagring](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du använder [rendera chefer](batch-rendering-render-managers.md) med Azure Batch.

* Mer information om alternativ för [Rendering i Azure](batch-rendering-service.md).