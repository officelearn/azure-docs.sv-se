---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165243"
---
Den här artikeln förutsätter att

1. En **plats-till-plats-VPN** eller en **Express Route**-anslutning redan har upprättats mellan ditt lokala nätverk och Azure Virtual Network.
2. Ditt användarkonto har behörighet att skapa en ny virtuell dator på den Azure-prenumeration som de virtuella datorerna har redundansväxlats till.
3. Din prenumeration har minst 4 kärnor tillgängliga för att lägga upp en ny virtuell processerverdator.
4. Du har **konfigurationsserverns lösenfras** tillgänglig.

> [!TIP]
> Se till att du kan ansluta port 443 för konfigurationsservern (körs lokalt) från det virtuella Azure-nätverk som de virtuella datorerna har redundansväxlats till.
