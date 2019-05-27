---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: f0e575af51f952a80fe42102b033727713c75cf9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66122235"
---
## <a name="configuration-scenario"></a>Konfigurationsscenario

I det här scenariot skapar vi en intern lastbalanserare i ett virtuellt nätverk som det visas i följande figur:

![Scenario med intern lastbalanserare](./media/load-balancer-get-started-ilb-scenario-include/figure1.png)

Konfigurationen för vårt scenario är följande:

* Två virtuella datorer som heter **DB1** och **DB2**
* Slutpunkter för den interna lastbalanseraren
* En intern lastbalanserare
