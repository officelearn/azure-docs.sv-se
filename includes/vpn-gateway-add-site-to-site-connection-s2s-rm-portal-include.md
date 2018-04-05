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
ms.openlocfilehash: ea616786d69d41435be2a46e90d4973b21270935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
1. Gå till och öppna sidan för den virtuella nätverksgatewayen. Det finns flera sätt att göra detta på. I vårt exempel gick vi till gatewayen 'VNet1GW' genom att gå till **TestVNet1 -> Översikt -> Anslutna enheter -> VNet1GW**.
2. Klicka på **Anslutningar** på sidan för VNet1GW. Klicka på **+Lägg till** överst på sidan Anslutningar och öppna sidan **Lägg till anslutning**.

    ![Skapa en plats-till-plats-anslutning](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. Skapa anslutningen genom att fylla i värden på sidan **Lägg till anslutning**.

  - **Namn:** Namnge din anslutning. Vi använder **VNet1toSite2** i vårt exempel.
  - **Anslutningstyp:** Välj du **Plats-till-plats(IPSec)**.
  - **Virtuell nätverksgateway:** Värdet är låst eftersom du ansluter från den här gatewayen.
  - **Lokal nätverksgateway:** Klicka på **Välj en lokal nätverksgateway** och väljer den lokala nätverksgateway som du vill använda. I vårt exempel använder vi **Webbplats2**.
  - **Delad nyckel:** Värdet måste matcha det värde som du använder för din lokala VPN-enhet. I det här exemplet använder vi ”abc123”, men du kan (och bör) använda ett mer komplext värde. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.
  - De återstående värdena för **Prenumeration**, **Resursgrupp** och **Plats** är låsta.

4. Klicka på **OK** för att skapa din anslutning. Du kommer att se *Skapar anslutningen* blinka fram på skärmen.
5. Anslutningen visas på sidan **Anslutningar** för den virtuella nätverksgatewayen. Statusen kommer att gå från *Okänd* till *Ansluter* och sedan till *Klar*.