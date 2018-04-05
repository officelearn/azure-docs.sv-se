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
ms.openlocfilehash: 2c1a4a1931bc2e38b0bee5f90518b01fdf4767a1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
Om du arbetar med Resource Manager-distributionsmodellen kan ändra du till den nya gatewayen SKU: er. När du byter från en äldre gateway-SKU till en ny SKU du ta bort den befintliga VPN-gatewayen och skapa en ny VPN-gateway.

Arbetsflöde:

1. Ta bort flera anslutningar till en virtuell nätverksgateway.
2. Ta bort VPN-gatewayen.
3. Skapa den nya VPN-gatewayen.
4. Uppdatera din lokala VPN-enhet med den nya IP-adressen för VPN-gatewayen (för plats-till-plats-anslutningar).
5. Uppdatera gateway IP-adressvärdet för alla lokala nätverket VNet-till-VNet-gateways som ska ansluta till den här gatewayen.
6. Hämta den nya klientpaket för VPN-konfiguration för P2S-klienter som ansluter till det virtuella nätverket via den här VPN-gatewayen.
7. Skapa om flera anslutningar till en virtuell nätverksgateway.

Att tänka på:

* Om du vill flytta till de nya SKU: er, måste din VPN-gateway vara i Resource Manager-distributionsmodellen.
* Om du har en klassiska VPN-gateway, måste du fortsätta med de äldre äldre SKU: er för denna gateway, men du kan ändra storlek på mellan äldre SKU: er. Du kan inte ändra till de nya SKU: er.
* Du har anslutning avbrottstid när du byter från en äldre SKU till en ny SKU.
* När du byter till en ny gateway SKU ändras den offentliga IP-adressen för VPN-gateway. Detta inträffar även om du anger samma offentliga IP-adress-objekt som du använde tidigare.