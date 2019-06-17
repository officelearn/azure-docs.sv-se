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
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170312"
---
## <a name="scenario"></a>Scenario
För att bättre visar hur du skapar NSG: er, används det här dokumentet följande scenario:

![VNet-scenario](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

I det här scenariot skapar du en NSG för varje undernät i den **TestVNet** virtuellt nätverk, på följande sätt: 

* **NSG-FrontEnd**. Klientdelen NSG som kopplats till den *klientdel* undernät, och innehåller två regler:    
  * **RDP-regeln**. Tillåter RDP-trafik till den *klientdel* undernät.
  * **regel för Web**. Tillåter HTTP-trafik till den *klientdel* undernät.
* **NSG-BackEnd**. Backend-NSG som kopplats till den *serverdel* undernät, och innehåller två regler:    
  * **sql-rule**. Tillåter SQL trafik enbart från de *klientdel* undernät.
  * **regel för Web**. Nekar alla internet-bunden trafik från den *serverdel* undernät.

Kombinationen av reglerna skapar en DMZ-liknande-scenario, där det backend-undernätet kan endast ta emot inkommande trafik för SQL från klientdelens undernät och har ingen åtkomst till Internet, medan klientdelsundernätet kan kommunicera med Internet och ta emot inkommande HTTP-begäranden endast.

