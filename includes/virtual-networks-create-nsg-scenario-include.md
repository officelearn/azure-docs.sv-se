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
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>Scenario
Det här dokumentet används för att illustrera hur du skapar NSG: er bättre följande scenario:

![VNet-scenario](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

I det här scenariot skapar du en NSG för varje undernät i den **TestVNet** virtuella nätverk på följande sätt: 

* **NSG-klientdel**. Frontend NSG som kopplats till den *klientdel* undernät, och innehåller två regler:    
  * **RDP-regel**. Tillåter RDP-trafik till den *klientdel* undernät.
  * **regel för Web**. Tillåter att HTTP-trafik på *klientdel* undernät.
* **NSG-BackEnd**. Backend-NSG som kopplats till den *BackEnd* undernät, och innehåller två regler:    
  * **SQL-regel**. Tillåter SQL-trafik från den *klientdel* undernät.
  * **regel för Web**. Nekar alla internet-bunden trafik från den *BackEnd* undernät.

Kombinationen av reglerna skapar ett DMZ-liknande scenario där backend-undernät kan endast ta emot inkommande trafik för SQL från klientdelen undernätet och inte har åtkomst till Internet, medan frontend undernätet kan kommunicera med Internet och ta emot inkommande HTTP-begäranden endast.

