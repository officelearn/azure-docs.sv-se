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
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743384"
---
## <a name="scenario"></a>Scenario
För att bättre visar hur du skapar udr: er, används det här dokumentet följande scenario:

![BESKRIVNING AV AVBILDNING](./media/virtual-network-create-udr-scenario-include/figure1.png)

I det här scenariot skapar du en UDR för det *klientdelsundernätet* och en annan UDR för det *serverdelsundernätet*, enligt följande: 

* **UDR-FrontEnd**. Klientdelen UDR tillämpas på den *klientdel* undernät, och innehåller en väg:    
  * **RouteToBackend**. Den här vägen skickar all trafik till serverdelsundernätet till den **FW1** virtuell dator.
* **UDR-BackEnd**. Backend-UDR tillämpas på den *serverdel* undernät, och innehåller en väg:    
  * **RouteToFrontend**. Den här vägen skickar all trafik till klientdelsundernätet till den **FW1** virtuell dator.

Kombinationen av dessa vägar ser till att all trafik från ett undernät till en annan dirigeras till den **FW1** virtuell dator som används som en virtuell installation. Du måste också aktivera IP-vidarebefordran för den **FW1** virtuell dator, så det kan ta emot trafik till andra virtuella datorer.
