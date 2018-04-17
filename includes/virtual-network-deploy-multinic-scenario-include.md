---
title: ta med fil
description: ta med fil
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 5fc9412713bc57c3fd7753e133b2587ea7a68938
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scenario
Det här dokumentet går igenom en distribution som använder flera nätverkskort i virtuella datorer i ett specifikt scenario. I det här scenariot har du en två skikt IaaS arbetsbelastning i Azure. Varje nivå har distribuerats i undernätet i ett virtuellt nätverk (VNet). Frontend-nivå består av flera webbservrar, grupperade i en belastningsutjämnare för hög tillgänglighet. Backend-nivå består av flera databasservrar. Database-servrar distribueras med två nätverkskort, ett för databasåtkomst för hantering. Scenariot omfattar även Nätverkssäkerhetsgrupper (NSG: er) att styra vilken trafik tillåts att varje undernät och NIC i distributionen. Följande bild visar den grundläggande arkitekturen i det här scenariot:  

![MultiNIC scenario](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

