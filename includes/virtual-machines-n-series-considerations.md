---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 202bffb03a73acde67f0d4a03b31aa9a6fbf9824
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901655"
---
## <a name="deployment-considerations"></a>Distributionsöverväganden

* Tillgänglighet för virtuella datorer i N-serien finns i [produkt tillgänglighet per region](https://azure.microsoft.com/regions/services/).

* Virtuella datorer i N-serien kan bara distribueras i distributions modellen för Resource Manager.

* Virtuella datorer i N-serien skiljer sig åt i den typ av Azure Storage de stöder för diskarna. NC och NV-virtuella datorer stöder bara virtuella hård diskar som backas upp av standard Disklagring (HDD). NCv2-, NCv3-, ND-, NDv2-och NVv2-VM: ar stöder bara virtuella hård diskar som backas upp av Premium Disklagring (SSD).

* Om du vill distribuera fler än några få virtuella datorer i N-serien bör du överväga att betala per användning-prenumeration eller andra köp alternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* Du kan behöva öka processor kvoten (per region) i din Azure-prenumeration och öka den separata kvoten för NC-, NCv2-, NCv3-, ND-, NDv2-, NV-eller NVv2-kärnor. Om du vill begära en kvot ökning [öppnar du en support förfrågan online](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) utan kostnad. Standard gränserna kan variera beroende på din prenumerations kategori.




