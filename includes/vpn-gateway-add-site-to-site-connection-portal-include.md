---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0dcb591db5f487a103feccf92ffa577a1cbf8b23
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
1. Gå till och öppna sidan för den virtuella nätverksgatewayen. Det finns flera sätt att göra detta på. Du kan navigera till gatewayen VNet1GW genom att öppna **TestVNet1 -> Översikt -> Anslutna enheter -> VNet1GW**.
2. Klicka på **Anslutningar** på sidan för VNet1GW. Klicka på **+Lägg till** överst på sidan Anslutningar och öppna sidan **Lägg till anslutning**.

  ![Skapa en plats-till-plats-anslutning](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. På sidan **Lägg till anslutning** ställer du in värdena för anslutningen.

  - **Namn:** Namnge din anslutning.
  - **Anslutningstyp:** Välj du **Plats-till-plats(IPSec)**.
  - **Virtuell nätverksgateway:** Värdet är låst eftersom du ansluter från den här gatewayen.
  - **Lokal nätverksgateway:** Klicka på **Välj en lokal nätverksgateway** och väljer den lokala nätverksgateway som du vill använda.
  - **Delad nyckel:** Värdet måste matcha det värde som du använder för din lokala VPN-enhet. I det här exemplet använder vi abc123. Du kan (och bör) dock använda ett mer komplext värde. Det är viktigt att värdet du anger här är samma värde som du anger när du konfigurerade VPN-enheten.
  - De återstående värdena för **Prenumeration**, **Resursgrupp** och **Plats** är låsta.

4. Klicka på **OK** för att skapa din anslutning. Du kommer att se *Skapar anslutningen* blinka fram på skärmen.
5. Anslutningen visas på sidan **Anslutningar** för den virtuella nätverksgatewayen. Statusen kommer att gå från *Okänd* till *Ansluter* och sedan till *Klar*.