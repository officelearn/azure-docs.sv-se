---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1a9c4dc5a4d21f8837bde171283cd8a070297674
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043872"
---
## <a name="deployment-considerations"></a>Distributionsöverväganden

* Tillgängligheten för virtuella datorer i N-serien finns [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/).

* Virtuella datorer i N-serien kan bara distribueras i Resource Manager-distributionsmodellen.

* N-serien virtuella datorer skiljer sig åt i Azure-lagringstyp som de har stöd för deras diskar. NC- och NV-datorer stöder bara Virtuella diskar som backas upp av Standard Disk Storage (HDD). NCv2, NCv3, ND och NVv2 virtuella datorer stöder bara Virtuella diskar som backas upp av Premium Disk Storage (SSD).

* Om du vill distribuera flera virtuella datorer i N-serien, Överväg en användningsbaserad prenumeration eller andra alternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* Du kan behöva öka kärnkvoten (per region) i din Azure-prenumeration och öka kärnkvoten för NC, NCv2, NCv3, ND, NV eller NVv2 separat. En kvot, [öppna en supportbegäran online-kund](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. Standardgränser kan variera beroende på din prenumerationskategori.




