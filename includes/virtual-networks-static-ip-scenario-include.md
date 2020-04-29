---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060596"
---
## <a name="scenario"></a>Scenario

För att bättre illustrera hur du konfigurerar en statisk IP-adress för en virtuell dator använder det här dokumentet det här scenariot:

![Scenario för virtuellt nätverk: frontend-och backend-undernät, med en statisk IP-adress för klient dels under nätet](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

I det här scenariot skapar du en virtuell dator med namnet *DNS01* i *klient delens* undernät och anger den sedan för att använda en statisk IP-adress för *192.168.1.101*.
