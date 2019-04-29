---
title: ta med fil
description: ta med fil
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596990"
---
## <a name="scenario"></a>Scenario

Det här dokumentet används för att visa hur du skapar ett virtuellt nätverk och undernät kan följande scenario:

![VNet-scenario](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

I det här scenariot skapar du ett virtuellt nätverk med namnet **TestVNet**, med det reserverade CIDR-blocket för **192.168.0.0./16**. Det virtuella nätverket innehåller följande undernät: 

* **FrontEnd**, som använder **192.168.1.0/24** som sitt CIDR-block.
* **BackEnd**, som använder **192.168.2.0/24** som sitt CIDR-block.