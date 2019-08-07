---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780180"
---
Gateway-undernätet innehåller de reserverade IP-adresser som används av tjänsten för virtuell nätverksgateway. Skapa ett Gateway-undernät.

1. På portalen går du till det virtuella Resource Manager-nätverk som du vill skapa en virtuell nätverksgateway för.
2. På sidan virtuellt nätverk klickar du på **undernät** för att expandera sidan med **VNet1-undernät** .
3. Klicka på **+ Gateway-undernät** längst upp för att öppna sidan **Lägg till undernät** .

   ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Lägg till gatewayundernätet")
4. **Namnet** på ditt undernät fylls i automatiskt med det obligatoriska värdet ' GatewaySubnet '. Justera det automatiskt ifyllda **adress intervallet (CIDR-block)** så att det matchar följande värde:

   **Adress intervall (CIDR-block)** : 10.1.255.0/27

   ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "Lägg till gatewayundernätet")
5. Lämna resten av inställningarna som standardvärden **none** eller **0 markerade**. Klicka sedan på **OK** för att skapa Gateway-undernätet.