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
ms.openlocfilehash: aa513d63a2af0fe994b8ab1ed7335a30998ff8ce
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scenario
Det här dokumentet används för att illustrera hur du skapar udr: er bättre följande scenario:

![BESKRIVNING AV AVBILDNING](./media/virtual-network-create-udr-scenario-include/figure1.png)

I det här scenariot skapar du en UDR för den *frontend undernät* och en annan UDR för den *backend-undernät*enligt följande: 

* **UDR klientdel**. Frontend UDR tillämpas på den *klientdel* undernät, och innehåller en väg:    
  * **RouteToBackend**. Den här vägen skickar all trafik till backend-undernät som den **FW1** virtuella datorn.
* **UDR BackEnd**. Backend-UDR tillämpas på den *BackEnd* undernät, och innehåller en väg:    
  * **RouteToFrontend**. Den här vägen skickar all trafik frontend-undernät som den **FW1** virtuella datorn.

Kombinationen av dessa vägar garanterar att all trafik från ett undernät till en annan dirigeras till den **FW1** virtuell dator som används som en virtuell installation. Du måste också aktivera IP-vidarebefordring för den **FW1** VM, så det kan ta emot trafik till andra virtuella datorer.

