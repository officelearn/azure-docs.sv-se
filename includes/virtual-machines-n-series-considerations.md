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
ms.openlocfilehash: 3267f649e360c512a5523ce1d5948719a1969934
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32297594"
---
## <a name="deployment-considerations"></a>Distributionsöverväganden

* Tillgängligheten för virtuella datorer N-serien finns [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/).

* N-serien virtuella datorer kan bara distribueras i Resource Manager-distributionsmodellen.

* N-serien virtuella datorer skiljer sig i typ av Azure Storage som de har stöd för deras diskar. NC och NV virtuella datorer stöder bara Virtuella diskar som backas upp av Standard Disk lagring (HDD). NCv2, ND och NCv3 virtuella datorer stöder bara Virtuella diskar som backas upp av Premium Disk lagring (SSD).

* Om du vill distribuera flera virtuella datorer N-serien du en prenumeration med användningsbaserad betalning eller andra köpalternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* Du kan behöva öka kvoten kärnor (per region) i din Azure-prenumeration och öka separat kvoten för NC NCv2, NCv3, ND eller NV kärnor. Att begära en ökad kvot [öppna en supportbegäran online customer](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. Standardgränser kan variera beroende på din prenumerationskategori.




