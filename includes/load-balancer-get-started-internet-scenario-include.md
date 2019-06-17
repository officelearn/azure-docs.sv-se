---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122171"
---
Följande uppgifter utförs i det här scenariot:

* Skapa en lastbalanserare som tar emot nätverkstrafik på port 80 och skickar lastbalanserad trafik till virtuella datorerna "web1" och "web2"
* Skapa NAT-regler för åtkomt till fjärrskrivbord/SSH för virtuella datorer bakom lastbalanseraren
* Skapa hälsotillståndsavsökningar

![Lastbalanseringsscenario](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
