---
title: Referens arkitekturer för Azure-åter givning – Azure Batch
description: Arkitekturer för att använda Azure Batch och andra Azure-tjänster för att utöka en lokal rendering-servergrupp genom burst-överföring till molnet
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: jushiman
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: a04f59983aca4b7db1a58ab4e8b8a2da47a52783
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026312"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referens arkitekturer för Azure-åter givning

Den här artikeln visar arkitektur diagram på hög nivå för scenarier som kan utökas eller "burst", en lokal åter givnings Server grupp till Azure. I exemplen visas olika alternativ för Azure Compute, nätverk och lagrings tjänster.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrid med NFS eller CFS

Följande diagram visar ett hybrid scenario som innehåller följande Azure-tjänster:

* **Compute** -Azure Batch pool eller skalnings uppsättning för virtuell dator.

* Lokalt **nätverk** : Azure EXPRESSROUTE eller VPN. Azure: Azure VNet.

* **Lagrings** -och utdatafiler: NFS eller CFS med virtuella Azure-datorer, synkroniserade med lokal lagring via Azure File Sync eller RSync. Alternativt kan du: aver vFXT till indata-eller utdatafiler från lokala NAS-enheter med hjälp av NFS.

  ![Cloud bursting – hybrid med NFS eller CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybrid med Blobfuse

Följande diagram visar ett hybrid scenario som innehåller följande Azure-tjänster:

* **Compute** -Azure Batch pool eller skalnings uppsättning för virtuell dator.

* Lokalt **nätverk** : Azure EXPRESSROUTE eller VPN. Azure: Azure VNet.

* **Lagrings** -och utdatafiler: Blob Storage, monteras på beräknings resurser via Azure Blobfuse.

  ![Cloud bursting – hybrid med Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrid beräkning och lagring

Följande diagram visar ett fullständigt kopplat hybrid scenario för både beräkning och lagring och innehåller följande Azure-tjänster:

* **Compute** -Azure Batch pool eller skalnings uppsättning för virtuell dator.

* Lokalt **nätverk** : Azure EXPRESSROUTE eller VPN. Azure: Azure VNet.

* **Lagring** – mellan platser: aver vFXT. Valfri arkivering av lokala filer via Azure Data Box till Blob Storage eller lokalt AVERT FXT för NAS-acceleration.

  ![Cloud burst – hybrid beräkning och lagring](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du använder [åter givnings hanterare](batch-rendering-render-managers.md) med Azure Batch.

* Lär dig mer om alternativ för [åter givning i Azure](batch-rendering-service.md).