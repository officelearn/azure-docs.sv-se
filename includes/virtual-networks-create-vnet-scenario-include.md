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
ms.openlocfilehash: 29b2ac1a5dc128028dbd40e683c1b45e6208d124
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scenario

Det här dokumentet används för att illustrera hur du skapar ett VNet och undernät i följande scenario:

![VNet-scenario](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

I det här scenariot skapar du ett VNet med namnet **TestVNet**, med en reserverade CIDR-blocket **192.168.0.0./16**. VNet innehåller följande undernät: 

* **FrontEnd**, som använder **192.168.1.0/24** som sitt CIDR-block.
* **BackEnd**, som använder **192.168.2.0/24** som sitt CIDR-block.

