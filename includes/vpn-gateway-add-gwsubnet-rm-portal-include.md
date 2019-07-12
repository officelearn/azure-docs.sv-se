---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 42e983ead6f7562c6a31cf9ef4ad2d97d0ff9707
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673542"
---
1. I den [Azure-portalen](https://portal.azure.com), Välj det virtuella Resource Manager-nätverk som du vill skapa en virtuell nätverksgateway.

2. I den **inställningar** på virtuellt nätverkssidan, väljer **undernät** att expandera den **undernät** sidan.

3. På den **undernät** väljer **gatewayundernätet** att öppna den **Lägg till undernät** sidan.

   ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Lägg till gatewayundernätet")

4. Den **namn** för ditt undernät är automatiskt autofilled med värdet *GatewaySubnet*. Det här värdet krävs för Azure för att kunna identifiera undernätet som gateway-undernätet. Justera autofilled **adressintervall** värden så att de matchar konfigurationskraven, välj sedan **OK** att skapa undernätet.

   ![Lägga till undernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Lägga till undernätet")
