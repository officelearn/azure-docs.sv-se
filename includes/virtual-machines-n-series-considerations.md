---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c1e57ea28f597293d8bb52207bbbb76892b1d5f5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
## <a name="deployment-considerations"></a>Distributionsöverväganden

* Tillgängligheten för virtuella datorer N-serien finns [produkter som är tillgängliga efter region](https://azure.microsoft.com/en-us/regions/services/).

* N-serien virtuella datorer kan bara distribueras i Resource Manager-distributionsmodellen.

* N-serien virtuella datorer skiljer sig i typ av Azure Storage som de har stöd för deras diskar. NC och NV virtuella datorer stöder bara Virtuella diskar som backas upp av Standard Disk lagring (HDD). NCv2, ND och NCv3 virtuella datorer stöder bara Virtuella diskar som backas upp av Premium Disk lagring (SSD).

* Om du vill distribuera flera virtuella datorer N-serien du en prenumeration med användningsbaserad betalning eller andra köpalternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* Du kan behöva öka kvoten kärnor (per region) i din Azure-prenumeration och öka separat kvoten för NC NCv2, NCv3, ND eller NV kärnor. Att begära en ökad kvot [öppna en supportbegäran online customer](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. Standardgränser kan variera beroende på din prenumerationskategori.




