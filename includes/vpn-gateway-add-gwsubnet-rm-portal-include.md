---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1fa4d58cefa82e70c036d697957254531042b9c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
1. I [portalen](http://portal.azure.com) går du till det virtuella Resource Manager-nätverket för vilket du vill skapa en virtuell nätverksgateway.
2. I avsnittet **Inställningar** på sidan för det virtuella nätverket, klickar du på **Undernät** så att sidan Undernät öppnas.
3. På sidan**Undernät** klickar du på **+Gateway-undernät** så att sidan **Lägg till undernät** öppnas.

  ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Lägg till gatewayundernätet")
4. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera de automatiskt ifyllda värdena för **Adressintervall** så att de matchar konfigurationskraven, och klicka sedan på **OK** längst ned på sidan för att skapa undernätet.

  ![Lägga till undernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Lägga till undernätet")