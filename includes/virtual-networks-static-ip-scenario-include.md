---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060596"
---
## <a name="scenario"></a>Scenario

För att bättre illustrera hur du konfigurerar en statisk IP-adress för en virtuell dator använder det här dokumentet det här scenariot:

![Scenario för virtuellt nätverk: Front-end- och backend-undernät, med en statisk IP-adress för frontend-undernätet](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

I det här fallet skapar du en virtuell dator med namnet *DNS01* i *FrontEnd-undernätet* och ställer sedan in den så att den använder en statisk IP-adress *på 192.168.1.101*.
