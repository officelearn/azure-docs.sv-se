---
title: ta med fil
description: ta med fil
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cff737b8c79c44494cb0151d6a6281550401b26e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805074"
---
## <a name="scenario"></a>Scenario

Det här dokumentet används för att illustrera hur du skapar ett VNet och undernät i följande scenario:

![VNet-scenario](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

I det här scenariot skapar du ett VNet med namnet **TestVNet**, med en reserverade CIDR-blocket **192.168.0.0./16**. VNet innehåller följande undernät: 

* **FrontEnd**, som använder **192.168.1.0/24** som sitt CIDR-block.
* **BackEnd**, som använder **192.168.2.0/24** som sitt CIDR-block.

