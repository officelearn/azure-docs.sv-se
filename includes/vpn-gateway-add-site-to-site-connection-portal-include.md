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
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "68780223"
---
1. Öppna sidan för din virtuella nätverksgateway. Det finns flera sätt att göra detta på. Du kan navigera till gatewayen genom att gå till **namnet på din VNet-> översikt – > anslutna enheter – > namnet på din gateway**.
2. På sidan för gatewayen klickar du på **anslutningar**. Klicka på **+Lägg till** överst på sidan Anslutningar och öppna sidan **Lägg till anslutning**.

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
