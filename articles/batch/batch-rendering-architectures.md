---
title: Azure-renderingsreferensarkitekturer - Azure Batch
description: Arkitekturer för att använda Azure Batch och andra Azure-tjänster för att utöka en lokal renderingsgrupp genom att brista ut i molnet
services: batch
ms.service: batch
author: davefellows
manager: evansma
ms.author: labrenne
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: 20442a6618ca9357bb3be95879b68bffca45a40d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022961"
---
# <a name="reference-architectures-for-azure-rendering"></a>Referensarkitekturer för Azure-rendering

Den här artikeln visar arkitekturdiagram på hög nivå för scenarier som ska utöka, eller "burst", en lokal renderingsgrupp till Azure. Exemplen visar olika alternativ för Azure-beräknings-, nätverks- och lagringstjänster.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybrid med NFS eller CFS

Följande diagram visar ett hybridscenario som innehåller följande Azure-tjänster:

* **Beräkning** - Azure Batch pool eller virtuell dator skaluppsättning.

* **Nätverk** - Lokalt: Azure ExpressRoute eller VPN. Azure: Azure VNet.

* **Lagring** - In- och utdatafiler: NFS eller CFS med virtuella Azure-datorer, synkroniserade med lokalt lagringsutrymme via Azure File Sync eller RSync. Alternativt: Avere vFXT för att mata in eller mata ut filer från lokala NAS-enheter med NFS.

  ![Cloud bursting - Hybrid med NFS eller CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybrid med blus

Följande diagram visar ett hybridscenario som innehåller följande Azure-tjänster:

* **Beräkning** - Azure Batch pool eller virtuell dator skaluppsättning.

* **Nätverk** - Lokalt: Azure ExpressRoute eller VPN. Azure: Azure VNet.

* **Lagring** - In- och utdatafiler: Blob-lagring, monterad för att beräkna resurser via Azure Blobfuse.

  ![Cloud bursting - Hybrid med blus](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hybrid beräkning och lagring

Följande diagram visar ett fullständigt anslutet hybridscenario för både beräkning och lagring och innehåller följande Azure-tjänster:

* **Beräkning** - Azure Batch pool eller virtuell dator skaluppsättning.

* **Nätverk** - Lokalt: Azure ExpressRoute eller VPN. Azure: Azure VNet.

* **Förvaring** - Korslokaler: Avere vFXT. Valfri arkivering av lokala filer via Azure Data Box till Blob-lagring eller lokal Avere FXT för NAS-acceleration.

  ![Cloud bursting - Hybrid beräkning och lagring](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du använder [Render-chefer](batch-rendering-render-managers.md) med Azure Batch.

* Läs mer om alternativ för [rendering i Azure](batch-rendering-service.md).