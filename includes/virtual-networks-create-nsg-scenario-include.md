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
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309971"
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
