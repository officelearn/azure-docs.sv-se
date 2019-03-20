---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908696"
---
Om du arbetar med Resource Manager-distributionsmodellen kan ändra du till den nya gatewayen SKU: er. När du byter från en äldre gateway-SKU till en ny SKU du ta bort den befintliga VPN-gatewayen och skapa en ny VPN-gateway.

Arbetsflöde:

1. Ta bort flera anslutningar till en virtuell nätverksgateway.
2. Ta bort VPN gatewayen.
3. Skapa den nya VPN gatewayen.
4. Uppdatera din lokala VPN-enhet med den nya IP-adressen för VPN-gatewayen (för plats-till-plats-anslutningar).
5. Uppdatera gateway IP-adressvärdet för alla lokala nätverket VNet-till-VNet-gateways som ska ansluta till den här gatewayen.
6. Hämta den nya klientpaket för VPN-konfiguration för P2S-klienter som ansluter till det virtuella nätverket via den här VPN-gatewayen.
7. Skapa om flera anslutningar till en virtuell nätverksgateway.

Överväganden:

* Om du vill flytta till de nya SKU: er, måste din VPN-gateway vara i Resource Manager-distributionsmodellen.
* Om du har en klassiska VPN-gateway, måste du fortsätta med de äldre äldre SKU: erna för denna gateway, men du kan ändra storlek mellan äldre SKU: er. Du kan inte ändra till de nya SKU: er.
* Du har stilleståndstid för anslutningar när du byter från en äldre SKU till en ny SKU.
* När du byter till en ny gateway-SKU, offentliga IP-adress för VPN-gateway kommer att ändras. Detta gäller även om du anger samma offentliga IP-adressobjektet som du använde tidigare.