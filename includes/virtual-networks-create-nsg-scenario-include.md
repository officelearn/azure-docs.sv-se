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
ms.openlocfilehash: 09c6871fc5243296da2f2defd594afb80c62ac95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
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

