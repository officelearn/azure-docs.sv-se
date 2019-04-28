---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516958"
---
Följande uppgifter utförs i det här scenariot:

* Skapa en lastbalanserare som tar emot nätverkstrafik på port 80 och skickar lastbalanserad trafik till virtuella datorerna "web1" och "web2"
* Skapa NAT-regler för åtkomt till fjärrskrivbord/SSH för virtuella datorer bakom lastbalanseraren
* Skapa hälsotillståndsavsökningar

![Lastbalanseringsscenario](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)